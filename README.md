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
    # test为user为key，items为val的字典，items为推荐列表或set
    # rank代表，针对当前user的长度为N的推荐列表或set
    for user, items in test.items():
        rank = Recommend(user, N)
        hit += len(rank & items)
        n_recall += len(items)
        n_precision += N
    return [hit / (1.0 * n_recall), hit / (1.0 * n_precision)]

```

> 有的时候，为了全面评测 TopN 推荐的准确率和召回率，一般会选取不同的推荐列表长度 N ，计算出一组准确率 / 召回率，然后画出准确率 / 召回率曲线（ precision/recall curve ） 。


##### 覆盖率
> 覆盖率（ coverage ）描述一个推荐系统对物品长尾的发掘能力。覆盖率有不同的定义方法，最简单的定义为推荐系统能够推荐出来的物品占总物品集合的比例。假设系统的用户集合为 U ，推荐系统给每个用户推荐一个长度为 N 的物品列表 R(u) 。那么推荐系统的覆盖率可以通过下面的公式计算：![image](https://user-images.githubusercontent.com/6240382/160765109-c03bc040-3f53-4a39-859a-ecd9ce165cf5.png)

```python

def coverage(recommends, all_items):
    """
        计算覆盖率
        @param recommends : dict形式 { userID : Items }
        @param all_items :  所有的items，为list或set类型
    """
    recommend_items = set()
    for _, items in recommends.items():
        for item in items:
            recommend_items.add(item)
    return len(recommend_items) / len(all_items)

```

##### 平均热门程度（平均流行度）
> 至于推荐结果的新颖性，我们简单地用推荐结果的平均热门程度（ AveragePopularity ）度量。对于物品 i ，定义它的流行度 item_pop(i) 为给这个物品打过标签的用户数。而对推荐系统，我
们定义它的平均热门度如下：![image](https://user-images.githubusercontent.com/6240382/160767485-cc65662f-2935-4f62-9f38-dda0c12892bf.png)


```python


def Popularity(train, test, N):
    # 物品流行度
    item_popularity = dict()
    for user, items in train.items():
        for item in items.keys()
            if item not in item_popularity:
                item_popularity[item] = 0
            item_popularity[item] += 1
    ret = 0
    n = 0
    for user in train.keys():
        rank = GetRecommendation(user, N)
        for item, pui in rank:
            ret += math.log(1 + item_popularity[item])
            n += 1
    ret /= n * 1.0
    return ret

```



## 用户行为数据分析
------------------------


### 用户活跃度和物品流行度的分布

> 横坐标是用户的活跃度 K ，纵坐标是活跃度为 K 的用户总数的log10值。（取对数，是为了更清晰看出分布）这里，用户的活跃度为用户产生过行为的物品总数。

![image](https://user-images.githubusercontent.com/6240382/160795074-8b8b1f60-5d80-4ad9-8e8a-2ec65dc112ad.png)

```python


item_popularity = movieLen_df.groupby(["userID"],as_index = False).count()
item_popularity.rename(mapper = {"movieID" : "user_act"},axis = 1,inplace = True)
item_popularity = item_popularity.groupby(["user_act"],as_index = False).count()
item_popularity.rename(mapper = {"userID" : "user_act_degree"},axis = 1,inplace = True)
item_popularity["log_user_act"] = np.log10(item_popularity.user_act)

sns.set_style("whitegrid")

sns.jointplot(data = item_popularity,x = "log_user_act",y = "user_act_degree")
plt.title("")

```




















































