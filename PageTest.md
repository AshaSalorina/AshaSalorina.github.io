# 开始记录第一个博客  

作为测试文档，通过企业实训学习记录来练习博客构建  

近期作业：  
&emsp;1.web网站分析  
&nbsp;  

```scala

    class WebScanner{
    var _path : String = ""

    var _Data : DataFrame = null

    def SetPath(path:String){
        _path = path
    }

    def ScanAndSplit(spark:SparkSession){
        import spark.implicits._
        //过滤出所有非#开头的行
        val data = spark.read.textFile(_path).filter(v=>v(0)!='#')
        //data.take(5).foreach(println)
        //以空格划分、缓存
        _Data = data.map(p=>p.split(" ")).filter(v=>v.length==11)
            .map(arr=>(arr(0),arr(1),arr(2),arr(3),arr(4),arr(5),arr(6),arr(7),arr(8),arr(9),arr(10)))
            .toDF("date","time","c-ip","cs-username", "s-ip", "s-port", "cs-method", "cs-uri-stem", "cs-uri-query", "sc-status", "cs(User-Agent)").persist()
    }

    /*
    *@Doc
    * 日活跃
    */
    def CountCustomer(spark:SparkSession){
        import spark.implicits._
        val rdd =_Data.rdd.map(v=>(v(0).toString,v(2).toString)).distinct()
        rdd.map(v=>(v._1,1)).reduceByKey(_+_).toDF("Date","Client").show()
        //_Data.groupBy("c-ip").sum().collect.length
    }
    /*
    *@Doc
    * GET网页活跃
    */
    def CountWebView(spark:SparkSession){
        import spark.implicits._
        val rdd =_Data.rdd.filter(v=>v(6)=="GET").map(v=>(v(7).toString,v(2).toString)).distinct()
        val rdd2 = rdd.mapValues(v=>1).reduceByKey(_+_).sortBy(-_._2)
        rdd2.map(v=>
            (v._1.substring(v._1.lastIndexOf('/')+1, v._1.length),v._2)
        ).toDF("Page","Client").show()
        //.toDF("Page","Client")
        //_Data.groupBy("c-ip").sum().collect.length
    }
    /*
    *@Doc
    * 压力
    */
    def DatePressure(spark:SparkSession){
        _Data.select("date").groupBy("date").count.show()
    }

}


object MyMath{
    def Max(spark:SparkSession,path:String):Int={
        import spark.implicits._
        val file = spark.read.textFile(path)
        var rt = file.flatMap(v=>v.split(" ")).map(v=>v.toInt).rdd.max
        return rt
    }
    def Min(spark:SparkSession,path:String):Int= {
        import spark.implicits._
        val file = spark.read.textFile(path)
        val rt = file.flatMap(v=>v.split(" ")).map(v=>v.toInt).rdd.min
        return rt
    }
}

```

&emsp;2.Sougou分析

```scala

```
