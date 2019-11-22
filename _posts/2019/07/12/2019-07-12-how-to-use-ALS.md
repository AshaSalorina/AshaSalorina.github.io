---
layout: post
title:  "企业实训-利用Spark-ALS实现推荐功能(更新评估部分)"
date:   2019-07-12 16:00:00 +0800
categories: study
img: https://i.loli.net/2019/07/11/5d26582f5d69d58772.png
---

## 基本概念  

要完成机器学习，我们需要做以下事情：  
&emsp;1.准备数据集  
&emsp;2.训练模型  
&emsp;3.使用模型来进行推荐  

在开始之前，请添加并引用以下库(版本请自行调整)：  

```scala
// build.sbt
libraryDependencies += "org.apache.spark" % "spark-mllib_2.12" % "2.4.3"

// your-code.scala
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

### 评估标准

如何评估一个modle的准确性?  
我们需要知道一些评估标准：  
| 标准      | 意义                                   |
| --------- | -------------------------------------- |
| precision | 真实为A样本的数量占所有被预测为A样本数的比值   |
| accuracy  | 正确预测的样本数占总预测样本数的比值   |
| recall    | 正确预测的A样本数占真实A样本总数的比值 |

更为详尽的标准可以参考这些文章：  
[准确率(Accuracy), 精确率(Precision), 召回率(Recall)和F1-Measure](https://blog.argcv.com/articles/1036.c)  
[Accuracy and precision](https://en.wikipedia.org/wiki/Accuracy_and_precision)  
[Precision and recall](https://en.wikipedia.org/wiki/Precision_and_recall)

----

### 使用modle自带的评估

```scala
//double
modle.predict(UID,PID)
```

predict可以得出用户对产品可能的评分  
利用这个函数我们可以自己完成评估过程（但是非常繁琐，看心情后续更新代码）

----

### 使用Metrics库

先给一个传送梦：  
[Metrics](https://github.com/erikvanoosten/metrics-scala)  
这是一个相当老牌的Scala库，提供了非常有用的评估计算，接下来我将<S>开花</S>使用这个库完成对之前训练模型的评估  

等待后续更新...
