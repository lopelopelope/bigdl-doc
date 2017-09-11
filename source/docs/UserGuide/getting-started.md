---
# **Getting Started**
---

This page shows how to run a BigDL program, including

* [Before running a BigDL program](#before-running-a-bigdl-program)
* [Interactive Spark Shell](#interactive-spark-shell)
* [Spark Program](#spark-program)
* [Next Steps](#next-steps)

## **Before running a BigDL program**
Before running a BigDL program, you need to set proper environment variables first.

### **Setting Environment Variables**
To achieve high performance, BigDL uses Intel MKL and multi-threaded programming; therefore, you need to first set the environment variables by running the provided script in `PATH_To_BigDL/bin/bigdl.sh` as follows:
```sbt
$ source PATH_To_BigDL/bin/bigdl.sh
```
Alternatively, you can also use the `PATH_To_BigDL/bin/bigdl.sh` script to launch your BigDL program; see the details below.

## **Interactive Spark shell**
You can quickly experiment with BigDL codes as a Spark program using the interactive Spark shell by running:
```sbt
$ source PATH_To_BigDL/bin/bigdl.sh
$ SPARK_HOME/bin/spark-shell --properties-file dist/conf/spark-bigdl.conf    \
  --jars bigdl-VERSION-jar-with-dependencies.jar
```
Then you can see something like:
```
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 1.6.0
      /_/

Using Scala version 2.10.5 (Java HotSpot(TM) 64-Bit Server VM, Java 1.7.0_79)
Spark context available as sc.
scala> 
```


For instance, to experiment with the ````Tensor```` APIs in BigDL, you may then try:
```scala
scala> import com.intel.analytics.bigdl.tensor.Tensor
import com.intel.analytics.bigdl.tensor.Tensor

scala> Tensor[Double](2,2).fill(1.0)
res9: com.intel.analytics.bigdl.tensor.Tensor[Double] =
1.0     1.0
1.0     1.0
[com.intel.analytics.bigdl.tensor.DenseTensor of size 2x2]
```
For more details about the BigDL APIs, please refer to the [Programming Guide](https://github.com/intel-analytics/BigDL/wiki/Programming-Guide).

## Spark Program
You can run a BigDL program, e.g., the [VGG](https://github.com/intel-analytics/BigDL/tree/master/spark/dl/src/main/scala/com/intel/analytics/bigdl/models/vgg) training, as a standard Spark program (running in either local mode or cluster mode) as follows:

1. Download the CIFAR-10 data from [here](https://www.cs.toronto.edu/%7Ekriz/cifar.html). Remember to choose the binary version.

2. Use the `bigdl.sh` script to launch the example as a Spark program as follows:

```
  # Spark local mode
  ./dist/bin/bigdl.sh -- \
  spark-submit --master local[core_number] --class com.intel.analytics.bigdl.models.vgg.Train \
  dist/lib/bigdl-VERSION-jar-with-dependencies.jar \
  -f path_to_your_cifar_folder \
  -b batch_size

  # Spark standalone mode
  ./dist/bin/bigdl.sh -- \
  spark-submit --master spark://... --executor-cores cores_per_executor \
  --total-executor-cores total_cores_for_the_job \
  --class com.intel.analytics.bigdl.models.vgg.Train \
  dist/lib/bigdl-VERSION-jar-with-dependencies.jar \
  -f path_to_your_cifar_folder \
  -b batch_size

  # Spark yarn mode
  ./dist/bin/bigdl.sh -- \
  spark-submit --master yarn --deploy-mode client \
  --executor-cores cores_per_executor \
  --num-executors executors_number \
  --class com.intel.analytics.bigdl.models.vgg.Train \
  dist/lib/bigdl-VERSION-jar-with-dependencies.jar \
  -f path_to_your_cifar_folder \
  -b batch_size
```

  The parameters used in the above command are:

  * -f: The folder where your put the CIFAR-10 data set. Note in this example, this is just a local file folder on the Spark driver; as the CIFAR-10 data is somewhat small (about 120MB), we will directly send it from the driver to executors in the example.

  * -b: The mini-batch size. The mini-batch size is expected to be a multiple of *total cores* used in the job. In this example, the mini-batch size is suggested to be set to *total cores * 4*
  
## Use log4j properity file to investigate issue using Debug log
1.an example log4j properity file
```
# Root logger option
log4j.rootLogger=INFO, stdout

# Direct log messages to stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

log4j.logger.com.intel.analytics.bigdl.optim=DEBUG,myappender
log4j.addivity.com.intel.analytics.bigdl.optim=false

log4j.appender.myappender=org.apache.log4j.ConsoleAppender
log4j.appender.myappender.Target=System.out
log4j.appender.myappender.layout=org.apache.log4j.PatternLayout
log4j.appender.myappender.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n
```
This file makes the classes in com.intel.analytics.bigdl.optim to output log on Debug level and the other classes on Info level.
2.To make the properity file work you have to add the following argument in the command that you are going to input.
```
--conf "spark.driver.extraJavaOptions=-Dlog4j.configuration=file:/xxx/xxx/..."
```
/xxx/xxx/... is the position where you put the properity file.
For example you can view the Debug level log of ./models/lenet/Train.scala (Details of this file can be found here:https://github.com/intel-analytics/BigDL/tree/master/spark/dl/src/main/scala/com/intel/analytics/bigdl/models/lenet) in Spark local mode using the following command:
```
spark-submit --master local[core_number] \
--driver-class-path dist/lib/bigdl-VERSION-jar-with-dependencies.jar \
--class com.intel.analytics.bigdl.models.lenet.Train \
--conf "spark.driver.extraJavaOptions=-Dlog4j.configuration=file:/xxx/xxx/..." \
dist/lib/bigdl-0.3.0-SNAPSHOT-jar-with-dependencies.jar \
-f path_to_your_mnist_folder \

```

## Next Steps
* To learn the details of Python support in BigDL, you can check out the [Python Support Page][pythonsupport]

* To learn how to create practical neural networks using BigDL in a couple of minutes, you can check out the [Examples Page][examples]

* You can check out the [Documents Page](https://intel-analytics.github.io/bigdl-doc/) for more details (including Models, Examples, Programming Guide, etc.)

* You can join the [BigDL Google Group](https://groups.google.com/forum/#!forum/bigdl-user-group) (or subscribe to the [mail list](mailto:bigdl-user-group+subscribe@googlegroups.com)) for more questions and discussions on BigDL

* You can post bug reports and feature requests at the [Issue Page](https://github.com/intel-analytics/BigDL/issues)

[pythonsupport]: ../PythonSupport/python-support
[examples]: ../UserGuide/examples
