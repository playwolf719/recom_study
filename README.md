# recom_study

## 说明
------------------------

- 部分学习内容来自《推荐系统实践》项亮

## 评测指标
------------------------
### 预测准确度

#### 评分预测

##### RMSE与MAE
> 评分预测的预测准确度一般通过均方根误差（RMSE）和平均绝对误差（MAE）计算。对于
测试集中的一个用户u和物品i，令rui 是用户u对物品i的实际评分，而 pui 是推荐算法给出的预测评
分，那么 RMSE 的定义为：![image](https://user-images.githubusercontent.com/6240382/160744125-db372644-4208-4c52-bf25-1f8bcf240210.png)
> MAE 采用绝对值计算预测误差，它的定义为：![image](https://user-images.githubusercontent.com/6240382/160744858-d6ad1d58-4c10-480a-ae5a-e01d4e0a8008.png)


```python
# 假设我们用一个列表 records 存放用户评分数据，令 records[i] = [u,i,rui,pui] ，其
中 rui 是用户 u 对物品 i 的实际评分， pui 是算法预测出来的用户 u 对物品 i 的评分，那么下面的代
码分别实现了 RMSE 和 MAE 的计算过程。

def RMSE(records):
    return math.sqrt(sum([(rui-pui)*(rui-pui) for u,i,rui,pui in records]) / float(len(records) ) )


def MAE(records):
    return sum([abs(rui-pui) for u,i,rui,pui in records] )/ float(len(records) )

```

> 关于 RMSE 和 MAE 这两个指标的优缺点， Netflix 认为 RMSE 加大了对预测不准的用户物品评分的惩罚（平方项的惩罚），因而对系统的评测更加苛刻。研究表明，如果评分系统是基于整数建立的（即用户给的评分都是整数），那么对预测结果取整会降低 MAE 的误差


##### Top N推荐

> 网站在提供推荐服务时，一般是给用户一个个性化的推荐列表，这种推荐叫做 TopN 推荐。TopN 推荐的预测准确率一般通过准确率（ precision ） / 召回率（ recall ）度量。
> 令 R(u) 是根据用户在训练集上的行为给用户作出的推荐列表，而 T(u) 是用户在测试集上的行为列表。那么，推荐结果的召回率定义为：
![image](https://user-images.githubusercontent.com/6240382/160763265-53ad3f70-4c2c-45f3-910c-827ebad23826.png)
> 推荐结果的准确率定义为：
![image](https://user-images.githubusercontent.com/6240382/160763314-b2449391-d476-4e17-ad7f-5a791f9c79a8.png)

```python

def PrecisionRecall(test, N):
    hit = 0
    n_recall = 0
    n_precision = 0
    # test为user为key，items为val的字典，items为测试集上的推荐列表或set
    # rank代表，训练集上的针对当前user长度为N的推荐列表或set
    for user, items in test.items():
        rank = Recommend(user, N)
        hit += len(rank & items)
        n_recall += len(items)
        n_precision += N
    return [hit / (1.0 * n_recall), hit / (1.0 * n_precision)]

```

> 有的时候，为了全面评测 TopN 推荐的准确率和召回率，一般会选取不同的推荐列表长度 N ，计算出一组准确率 / 召回率，然后画出准确率 / 召回率曲线（ precision/recall curve ） 。

















