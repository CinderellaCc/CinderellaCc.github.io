---
title: 用Optimum的ONNX runtime加速sentence transformer（pytorch）
tags: ONNX runtime
categories: 模型加速
abbrlink: 64162
date: 2023-08-04 15:13:41
---

在本篇教程中，你将会学习到如何使用Optimum来加快Sentence Transformer模型的推理速度。

本文使用的Sentence Transformer模型是[m3e-base](https://huggingface.co/moka-ai/m3e-base)，该模型是当前效果较好的中文句向量计算模型。

[Hugging Face Optimum](https://huggingface.co/docs/optimum/index)是Transformers的扩展，提供了一系列性能优化工具，使得模型的训练和推理更加高效。Optimum中集成了ONNX runtime加速工具。

本教程将按以下步骤展开：
1. 环境配置
2. 将Sentence Transformer模型转换成ONNX格式
3. 自定义模型推理Pipeline
4. 在ONNX模型上应用图优化技术
5. 优化模型推理 + 测试优化模型推理的正确性
7. 性能评估

# 环境配置
运行以下命令，安装Optimum所对应的依赖
``` python
pip install optimum[onnxruntime]==1.5.0 # CPU
pip install optimum[onnxruntime-gpu] # GPU
```

# 将Sentence Transformer模型转换成ONNX格式
（注意：ONNX是一种模型格式，而ONNX runtime是模型加速方法）  
将Sentence Transformer模型转换成ONNX格式需要用到`ORTModelForFeatureExtraction`类
```python
from optimum.onnxruntime import ORTModelForFeatureExtraction
from transformers import AutoTokenizer
import os

onnx_model_dir = './models/onnx' # ONNX模型保存路径
model = ORTModelForFeatureExtraction.from_pretrained('./models/m3e-base', from_transformers=True)
tokenizer = AutoTokenizer.from_pretrained('./models/m3e-base')
model.save_pretrained(onnx_model_dir)
tokenizer.save_pretrained(onnx_model_dir)

# 打印目录下的文件
print('在{}目录下生成模型文件：{}'.format(onnx_model_dir, os.listdir(onnx_model_dir)))
# 输出：在./models/onnx目录下生成模型文件：['tokenizer_config.json', 'vocab.txt', 'model.onnx', 'config.json', 'special_tokens_map.json', 'tokenizer.json']
```

# 自定义模型推理Pipeline
由于现在的模型是onnx格式，我们无法直接使用model.encode()进行推理，所以改写了transformers库的Pipeline类，使其支持onnx格式的模型推理
```python
from transformers import Pipeline
import torch.nn.functional as F
import torch

def mean_pooling(model_output, attention_mask):
    token_embeddings = model_output[0] #First element of model_output contains all token embeddings
    input_mask_expanded = attention_mask.unsqueeze(-1).expand(token_embeddings.size()).float()
    return torch.sum(token_embeddings * input_mask_expanded, 1) / torch.clamp(input_mask_expanded.sum(1), min=1e-9)

class SentenceEmbeddingPipeline(Pipeline):
    def _sanitize_parameters(self, **kwargs):
        # we don't have any hyperameters to sanitize
        preprocess_kwargs = {}
        return preprocess_kwargs, {}, {}

    def preprocess(self, inputs):
        encoded_inputs = self.tokenizer(inputs, padding=True, truncation=True, return_tensors='pt')
        return encoded_inputs

    def _forward(self, model_inputs):
        outputs = self.model(**model_inputs)
        return {"outputs": outputs, "attention_mask": model_inputs["attention_mask"]}

    def postprocess(self, model_outputs):
        # Perform pooling
        sentence_embeddings = mean_pooling(model_outputs["outputs"], model_outputs['attention_mask'])
        # # Normalize embeddings
        # sentence_embeddings = F.normalize(sentence_embeddings, p=2, dim=1)
        return sentence_embeddings
```
现在使用`SentenceEmbeddingPipeline`加载ONNX模型，进行推理测试
```python
vanilla_emb = SentenceEmbeddingPipeline(model=model, tokenizer=tokenizer)
pred = vanilla_emb("今天是周五啦，明天就是周末啦，开心，开心，开心")
print(pred[0][:5])
# 输出：tensor([ 0.3507,  0.3588,  1.1540, -0.4012, -0.4290])
```

# 在ONNX模型上应用图优化技术

使用`ORTOptimizer`类和`OptimizationConfig`类进行优化配置，运行下述代码，生成优化后的ONNX模型`model_optimized.onnx`
```python
from optimum.onnxruntime import ORTOptimizer
from optimum.onnxruntime.configuration import OptimizationConfig

# 创建ORTOptimizer并定义优化配置
optimizer = ORTOptimizer.from_pretrained(onnx_model_dir)
optimization_config = OptimizationConfig(optimization_level=99) # 启用所有优化，例如常量折叠、常量传播、OP融合等

# 在模型上应用优化配置
optimizer.optimize(
    save_dir=onnx_model_dir,
    optimization_config=optimization_config,
)

print('在{}目录下生成优化后的模型文件：{}'.format(onnx_model_dir, os.listdir(onnx_model_dir)))
# 输出：在./models/onnx目录下生成优化后的模型文件：['tokenizer_config.json', 'ort_config.json', 'vocab.txt', 'model.onnx', 'config.json', 'model_optimized.onnx', 'special_tokens_map.json', 'tokenizer.json']
```

# 优化模型推理 + 测试优化模型推理的正确性
```python
from optimum.onnxruntime import ORTModelForFeatureExtraction
from transformers import AutoTokenizer

model_optimized = ORTModelForFeatureExtraction.from_pretrained(onnx_model_dir, file_name="model_optimized.onnx")
tokenizer = AutoTokenizer.from_pretrained(onnx_model_dir)

optimized_emb = SentenceEmbeddingPipeline(model=model_optimized, tokenizer=tokenizer)

# 使用优化后的模型进行推理
pred = optimized_emb('今天是周五啦，明天就是周末啦，开心，开心，开心')
print(pred[0][:5])
# 输出：tensor([ 0.3507,  0.3588,  1.1540, -0.4012, -0.4290])

# 查看是否与原Sentence Transformer模型结果一致
from sentence_transformers import SentenceTransformer

ori_emb = SentenceTransformer('./models/m3e-base', device='cpu') # 加载原模型
pred = ori_emb.encode('今天是周五啦，明天就是周末啦，开心，开心，开心')
print(pred[:5])
# 输出：[ 0.35070744  0.35883522  1.1540244  -0.4012457  -0.42901722]
```

# 性能评估
现在，我们来对比一下在加速前后模型的性能（推理速度）
```python
# 对比优化前后的模型推理时效
import time
from tqdm import tqdm

text = ['今天是周五啦，明天就是周末啦，开心，开心，开心'] * 1000
batch_size = 32

start = time.time()
for i in tqdm(range(0, len(text), batch_size), desc='onnxruntime'):
    pred = optimized_emb(text[i:i+batch_size])
avg_time1 = (time.time() - start) / len(text)
print('onnxruntime平均耗时：', avg_time1)

start = time.time()
for i in tqdm(range(0, len(text), batch_size), desc='sentence_transformers'):
    pred = ori_emb.encode(text[i:i+batch_size])
avg_time2 = (time.time() - start) / len(text)
print('sentence_transformers平均耗时：', avg_time2)

# 输出：
# onnxruntime平均耗时： 0.16051861429214478
# sentence_transformers平均耗时： 0.3235261046886444
```

可以看到，在GPU上，使用Optimum的ONNX runtime加速Sentence Transformer后，模型的推理速度提升了约2倍

> 参考资料：[Accelerate Sentence Transformers with Hugging Face Optimum](https://www.philschmid.de/optimize-sentence-transformers)