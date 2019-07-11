---
layout: post
title:  "企业实训-利用Spark-ALS实现推荐功能"
date:   2019-07-11 15:40:00 +0800
categories: study
img: https://i.loli.net/2019/07/11/5d26582f5d69d58772.png
---

## 基本概念  

要完成机器学习，我们需要做以下事情：  
&emsp;1.准备数据集  
&emsp;2.训练模型  
&emsp;3.使用模型来进行推荐  

在开始之前，请引用以下库：  

```scala
import org.apache.spark.mllib.recommendation.ALS
import org.apache.spark.mllib.recommendation.Rating
```

----

## 准备数据集

我们准备了两个 <B>数据集</B>  

```dir
u.data
    uid movieId rating time

u.item
    movieId movieName time
```

用scala读入数据的代码如下：

```scala
    //注意替换成自己的数据集
    val data = spark.read.textFile("a data file")
    val item = spark.read.textFile("an item file")
    //分隔符请自行制定
    //Spark-ALS需要RDD[Rating]类型
    val _dataRDD = data.map(_.split("\t"))
        .map(arr=>new Rating(arr(0).toInt,arr(1).toInt,arr(2).toDouble)).rdd
    val _itemRDD = item.map(_.split("|"))
        .map(arr=>(arr(0),arr(1))).toDF("movieId","movieName").rdd
```

接下来 我们将利用这两个文本来实现电影推荐功能  

----

## 训练模型

利用ALS库训练出结果模型modle

```scala
    //n*m => n * rank X rank * m,便于矩阵计算
    //RDD[Rating],rank,iterations
    val modle = ALS.train(_dataRDD, 10 ,20)
```

----

## 使用模型进行推荐  

ALS.recommend有4种推荐方式

```scala
//userID,Num:给用户推荐N个产品
modle.recommendProducts(1,5)
//Num:为所有用户推荐N个产品
modle.recommendProductsForUsers(5)
//movieId,Num:为产品挑选合适的N个用户
modle.recommendUsers(1,5)
//Num:为所有产品挑选合适的N个用户
modle.recommendUsersForProducts(5)
```

我们可以利用之前的item数据集来把movieID转化为Name

```scala
modle.recommendProducts(1,5).map(v=>(v.user,v.product match {
            case v.product => if (_itemRDD.filter(p=>p._1==v.product).count != 0){
                _itemRDD.filter(p=>p._1==v.product).first._2.toString
            }else{
                v.product.toString
            }
        }))
```

----

## 模型评估  

以下列出一些评估用函数，待后续更新

```scala
modle.predict(UID,PID)
```
