# [预训练模型推理加速] DeeBERT: Dynamic Early Exiting for Accelerating BERT Inference


## [预训练模型推理加速] DeeBERT: Dynamic Early Exiting for Accelerating BERT Inference

**Author: Xianyu Zhang Data: 20211115**

[论文链接](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2004.12993.pdf)

[开源实现](https://link.zhihu.com/?target=https%3A//github.com/castorini/%20DeeBERT.)

前言：可能存在细节理解不到位的部分，欢迎各位大佬探讨，不喜勿喷

### **摘要&&Introduction**

提速预训练模型推理，可以提速约`40%`的时间，精度保持不掉太多

通过在模型中每个transformer层之间插入一个分类层，一起在下游任务进行`finetune`

<img src="/Users/htzhang/XianyuZhang/static/pic/deebert.jpeg" alt="deebert" style="zoom:50%;" />





### **Early Exit for BERT inference**

不改变模型的结构，仅是在每个transformer层添加一个判断是否早退`Early Exit`的层。当在进行推理时，可以通过早退层进行退出，而不用走完整个网络结构



### DeeBERT at Fine-Tuning

第`i`层的损失函数，计算该层所有样本的交叉熵。

网络分两阶段进行`fine-tuning`

1、跟原始的`BERT fine-tuning`的步骤一致，训练所有样本

2、冻结所有的参数，但是在每层多一个分类层，用以计算层的损失函数。（需要保证`Transformers`的参数不要改变）



### DeeBERT at Inference

<img src="/Users/htzhang/XianyuZhang/static/pic/deebert-infer.jpeg" alt="deebert-infer" style="zoom:50%;" />

当某层的交叉熵小于阈值，就采用该层的预测结果



### **Experiment**

#### 实验结果

<img src="/Users/htzhang/XianyuZhang/static/pic/experiment.jpeg" alt="experiment" style="zoom:50%;" />

### 实验结论

1、使用了同样的`early exit`结构，`RoBERTa`下降的速度相对于 `BERT` 更快。

2、使用部分层的transformers可能会有更好的效果



### **DeeBERT优势**

1、同样可以拿到一个小一些的模型

2、与`DistilBERT`和`LayerDrop`不同，并不需要额外的预训练，会有些省时



### **Conclusion**

1. 通过每层的分类，减少了模型的冗余程度。提速性能不掉，但如何找到这个`trade off`还挺重要
2. 有些层对最终结果都的贡献程度不高的原因还未知
3. 感觉Early Exit是个很好的机制，相比起裁剪，蒸馏和量化等加速的机制，其保证了复杂样本在推理时，能够走完整个网络结构的可能性。

