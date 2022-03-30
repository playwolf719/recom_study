# recom_study

## 说明
------------------------

- 部分学习内容来自《推荐系统实践》项亮

## 评测指标
------------------------
### 预测准确度

#### 评分预测

##### RMSE与MAE
- 评分预测的预测准确度一般通过均方根误差（RMSE）和平均绝对误差（MAE）计算。对于
测试集中的一个用户u和物品i，令rui 是用户u对物品i的实际评分，而 pui 是推荐算法给出的预测评
分，那么 RMSE 的定义为：

![image](https://user-images.githubusercontent.com/6240382/160744125-db372644-4208-4c52-bf25-1f8bcf240210.png)

- MAE 采用绝对值计算预测误差，它的定义为:

![image](https://user-images.githubusercontent.com/6240382/160744858-d6ad1d58-4c10-480a-ae5a-e01d4e0a8008.png)


```
# 假设我们用一个列表 records 存放用户评分数据，令 records[i] = [u,i,rui,pui] ，其
中 rui 是用户 u 对物品 i 的实际评分， pui 是算法预测出来的用户 u 对物品 i 的评分，那么下面的代
码分别实现了 RMSE 和 MAE 的计算过程。

def RMSE(records):
    return math.sqrt(sum([(rui-pui)*(rui-pui) for u,i,rui,pui in records]) / float(len(records) ) )


def MAE(records):
    return sum([abs(rui-pui) for u,i,rui,pui in records] )/ float(len(records) )

```

- 关于 RMSE 和 MAE 这两个指标的优缺点， Netflix 认为 RMSE 加大了对预测不准的用户物品评分的惩罚（平方项的惩罚），因而对系统的评测更加苛刻。研究表明，如果评分系统是基于整数建立的（即用户给的评分都是整数），那么对预测结果取整会降低 MAE 的误差


##### Top N推荐



