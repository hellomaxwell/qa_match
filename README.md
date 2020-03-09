# 项目介绍
qa-match是一款基于深度学习的问答匹配工具，通过完成问题与问题的匹配来实现。qa-matching支撑层级（2层）结构知识库问答，实现了一种类似集成学习bagging方法的模型融合方式。为了实现层级问答，我们要求知识库中的每个问题都包含两个标签：领域标签和意图标签，领域标签是对意图标签的进一步抽象。

对于输入的问题，qa-matching会有三种回答：
1. 唯一回答（识别为用户具体的意图）
2. 列表回答（识别为用户可能的多个意图）
3. 拒绝回答（没有识别出具体的用户意图）

为了实现上述目标，qa-matching流程如下：
1.  用bilstm对用户语料实现领域级别的文本分类
2.  用dssm对用户语料实现意图级别的语义匹配
3.  通过设置参数对领域分类和意图匹配结果进行融合，支持层级结构知识库问答

其中第3步的融合结构如下：  

![结果融合](lstm_dssm_bagging.png)





# 如何使用
## 数据介绍
需要是用到的数据文件（data_demo文件夹下）介绍如下：  
- max.train，max.test：bilstm模型的训练和测试数据，数据格式`领域标签 问题`
- min.train，min.test，min.valid：dssm模型的训练、测试数据和验证数据，数据格式`意图标签 问题`
- standard：标准问题（对于同一类意图问题，我们包括一个标准问题和若干个扩展问题），数据格式`意图标签 问题`
- max_min.map：领域和意图的映射关系，数据格式`领域标签 意图标签`

数据中的`问题`要求以空格分隔。

## 项目运行
1. 把目标数据放入到data目录下。  
2. 运行项目：  

```
bash run.sh
```

## tips
1. 由于dssm模型训练选取负样本时是将原样本对应标签随机打散，所以模型参数需要满足`batch_size >= negitive_size`，否则模型无法有效训练。
2. 模型融合参数选取方法：首先会在测试集上计算同一参数不同阈值所对应标签的f1值，然后选取较大的f1值（根据项目需求可偏重准确率/覆盖率）对应的阈值做为该参数的取值。如：在选取bilstm拒绝回答标签对应参数阈值a1（上图a1）时,首先会在测试集上确定不同的a1取值对应模型的回答标签（模型top1回答为拒绝回答&分值大于a1时则认为拒绝回答，否则认为应该回答），然后根据样本的真实标签计算f1值，最后选取合适的f1值（根据项目需求可偏重准确率/覆盖率）对应的取值作为a1的值。这种基于统计的阈值选取方式比较复杂，将来会进一步的尝试模型拟合选取阈值的方式。                                             


# 运行环境
```
tensorflow 版本>r1.8 <r2.0, python3
```

# 如何贡献&问题反馈
我们诚挚地希望开发者向我们提出宝贵的意见和建议。您可以挑选以下方式向我们反馈建议和问题：
1. 在 github上 提交 PR 或者 Issue
2. 邮件发送至 ailab-opensource@58.com
