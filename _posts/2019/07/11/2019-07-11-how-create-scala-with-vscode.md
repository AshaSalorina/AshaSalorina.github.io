---
layout: post
title:  "如何使用VSCode从空文件夹构建一个Scala项目"
date:   2019-07-11 11:50:00 +0800
categories: study
img: https://i.loli.net/2019/07/11/5d26582f5d69d58772.png
---

## 需要的VSCode插件

|插件名称|作者名称|  
|----|----|
|Scala(sbt) |@Lightbend|  
|Scala Language Server |@Iulian Dragos|

----

## 建立空项目结构  

在空文件夹中，右键点击“Open With Code”  
&nbsp;(也可以使用【VSCode->文件->打开文件夹】来指定位置)  

打开shell终端(Ctrl+`),输入  

```shell
sbt
```

----

## 为sbt添加ensime插件

有了基本的项目结构后，ctrl+c结束sbt，然后在  

```dic
/project
```

下放入以下文件：plugins.sbt

```sbt
//2.5.1是对应的sbt版本，请根据自己的情况调整
addSbtPlugin("org.ensime" % "sbt-ensime" % "2.5.1")
  
//这里一定要有一行空行
//所有的.sbt后缀文件每一行之间都需要存在间隔
```

----

## 指导sbt工作

在根目录创建以下文件

```dic
//同样，这里的2.12.8/2.12/2.4.3也请根据自己的版本来进行调整
/build.sbt
    name := "AppName"

    version := "1.0"

    scalaVersion in ThisBuild := "2.12.8"

    ensimeScalaVersion in ThisBuild := "2.12.8"

    libraryDependencies += "org.apache.spark" % "spark-core_2.12" % "2.4.3"

    libraryDependencies += "org.apache.spark" % "spark-sql_2.12" % "2.4.3" % "compile"

/ensime.sbt
    ensimeIgnoreScalaMismatch in ThisBuild := true
```

----

## 让sbt完整的创建服务

再次打开终端，输入sbt，等待其出现sbt-shell  
输入ensimeConfig

```shell
sbt:AppName>ensimeConfig
```

之后保持sbt的运行(有点吃内存)

----

## 编写你的Scala

向以下目录添加.scala文件

```dic
/src/main/scala/..
```

例如，我添加了一个helloW.sbt

```scala
import org.apache.spark.SparkContext
import org.apache.spark.SparkContext._
import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.sql.SparkSession
import org.apache.spark.sql.Encoder
import org.apache.spark.sql._

object helloW
{
    def CreateSparkSession() : SparkSession ={
        val config = new SparkConf()
            .setAppName("Hello_Spark")
            .setJars(Array("编译后的文件地址，从根目录开始，Windows下不写盘符"))
            //.setJars(Array("/Scala/ProjectT/target/scala-2.12/hello_spark_2.12-1.0.jar"))

        val spark = SparkSession.builder
            .config(config)
            .config("spark.master", "local")
            .getOrCreate();
        return spark;
    }

    def main(args: Array[String]):Unit={
        //begin a SparkSession
        val spark = CreateSparkSession()
        import spark.implicits._
        //do something

        spark.stop()
    }
}
```

----

## 打包

在sbt终端输入package

```shell
sbt:AppName>package
```

终端会输出被打包的jar地址，这就可以填入你的“setJars”了

----

## VSCode运行和Submit运行

### VSCode run

在sbt终端输入run

```shell
sbt:AppName>run
```

scala执行方式，可以很方便的进行小规模测试

### Spark Submit

在powershell或者你的任何其他终端输入以下内容

```shell
spark-submit xxx.jar
```

提交到真实的Spark环境运行
