# **Build**

This page shows how to install and build BigDL (on both Linux and macOS), including:
  

* [Linking](#linking)
* [Download](#download)
* [Build](#build)    
    * [Build with make-dist.sh](#build-with-make-distsh)  
        * [Using the `make-dist.sh` script](#build-with-make-distsh)
        * [Build for macOS](#build-for-macos)
        * [Build for Spark 2.0](#build-for-spark-20)
        * [Build for Spark 2.1](#build-for-spark-21)
        * [Build for Cloudera Distributed Spark](#build-for-cloudera-distributed-spark)
        * [Build using Scala 2.10 or 2.11](#build-using-scala-210-or-211)
        * [Full Build](#full-build)
    * [Build with Maven](#build-with-maven)
* [IDE Settings](#ide-settings)
* [Next Steps](#next-steps)

## **Linking**

Currently, BigDL is host on maven central, here's an example to add the dependency to your own project:
```xml
<dependency>
    <groupId>com.intel.analytics.bigdl</groupId>
    <artifactId>bigdl</artifactId>
    <version>0.1.0</version>
</dependency>
```
SBT developers can use
```sbt
libraryDependencies += "com.intel.analytics.bigdl" % "bigdl" % "0.1.0"
```
*Note*: the BigDL lib default supports Spark 1.5.x and 1.6.x; if your project runs on Spark 2.0 and 2.1, use this
```xml
<dependency>
    <groupId>com.intel.analytics.bigdl</groupId>
    <artifactId>bigdl-SPARK_2.0</artifactId>
    <version>0.1.0</version>
</dependency>
```

If your project runs on MacOS, you should add the dependency below,

```
<dependency>
    <groupId>com.intel.analytics.bigdl.native</groupId>
    <artifactId>mkl-java-mac</artifactId>
    <version>0.1.0</version>
    <exclusions>
        <exclusion>
            <groupId>com.intel.analytics.bigdl.native</groupId>
            <artifactId>bigdl-native</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

SBT developers can use
```sbt
libraryDependencies += "com.intel.analytics.bigdl" % "bigdl-SPARK_2.0" % "0.1.0"
```
## **Download**
BigDL source code is available at [GitHub](https://github.com/intel-analytics/BigDL)

```sbt
$ git clone https://github.com/intel-analytics/BigDL.git
```

## **Build**
Maven 3 is needed to build BigDL, you can download it from the [maven website](https://maven.apache.org/download.cgi). 

After installing Maven 3, please set the environment variable MAVEN_OPTS as follows:
```sbt
$ export MAVEN_OPTS="-Xmx2g -XX:ReservedCodeCacheSize=512m"
```
When compiling with Java 7, you need to add the option “-XX:MaxPermSize=1G”. 

### **Build with `make-dist.sh`** 

#### **Using the [make-dist.sh](https://github.com/intel-analytics/BigDL/blob/master/make-dist.sh) script**
It is highly recommended that you build BigDL using the [make-dist.sh](https://github.com/intel-analytics/BigDL/blob/master/make-dist.sh).

Once downloaded, you can build BigDL with the following commands:
```sbt
$ bash make-dist.sh
```
After that, you can find a `dist` folder, which contains all the needed files to run a BigDL program. The files in `dist` include:  

 * dist/bin/bigdl.sh: A script used to set up proper environment variables and launch the BigDL program.
 * dist/lib/bigdl-VERSION-jar-with-dependencies.jar: This jar package contains all dependencies except Spark.

#### **Build for macOS**
The instructions above will only build for Linux. To build BigDL for macOS, pass `-P mac` to the `make-dist.sh` script as follows:

```sbt
$ bash make-dist.sh -P mac
```

#### **Build for Spark 2.0**
The instructions above will build BigDL with Spark 1.5.x or 1.6.x (using Scala 2.10); to build for Spark 2.0 (which uses Scala 2.11 by default), pass `-P spark_2.0` to the `make-dist.sh` script:

```sbt
$ bash make-dist.sh -P spark_2.0
```
It is highly recommended to use _**Java 8**_ when running with Spark 2.0; otherwise you may observe very poor performance.

#### Build for Spark 2.1
The instructions above will build BigDL with Spark 1.5.x or 1.6.x or spark 2.0; to build for Spark 2.1 (which uses Scala 2.11 by default), pass `-P spark_2.1` to the `make-dist.sh` script:
```sbt
$ bash make-dist.sh -P spark_2.1
```

It is highly recommended to use _**Java 8**_ when running with Spark 2.x; otherwise you may observe very poor performance.

#### **Build for Cloudera Distributed Spark**
The instructions above will build BigDL with Spark2-2.0.0-cloudera2 and cdh5-1.6.0_* ; to build for Spark2-2.0.0-cloudera2 (which uses Scala 2.11 by default), pass `-P cloudera` to the `make-dist.sh` script:
```sbt
$ bash make-dist.sh -P cloudera
```
To build for cdh5-1.6.0_*(which uses Scala 2.10 by default) with the following commands:
```sbt
$ bash make-dist.sh
```
#### **Build using Scala 2.10 or 2.11**
By default, `make-dist.sh` uses Scala 2.10 for Spark 1.5.x or 1.6.x, and Scala 2.11 for Spark 2.0. To override the default behaviors, you can pass `-P scala_2.10` or `-P scala_2.11` to `make-dist.sh` as appropriate.

#### **Full Build**

Note that the instructions above will skip the build of native library code, and pull the corresponding libraries from Maven Central. If you want to build the the native library code by yourself, follow the steps below:

1. Download and install [Intel Parallel Studio XE](https://software.intel.com//qualify-for-free-software/opensourcecontributor) in your Linux box.

2. Prepare build environment as follows:
```sbt
    $ source <install-dir>/bin/compilervars.sh intel64
    $ source PATH_TO_MKL/bin/mklvars.sh intel64
```
where the `PATH_TO_MKL` is the installation directory of the MKL.  

3. Full build  
Clone BidDL as follows:  
```sbt
   git clone git@github.com:intel-analytics/BigDL.git --recursive 
```
   For already cloned repos, just use:  
```sbt
   git submodule update --init --recursive 
```
If the Intel MKL is not installed to the default path `/opt/intel`, please pass your libiomp5.so's directory path tothe `make-dist.sh` script:  
```sbt
   $ bash make-dist.sh -P full-build -DiompLibDir=<PATH_TO_LIBIOMP5_DIR> 
```
Otherwise, only pass `-P full-build` to the `make-dist.sh` script:
```sbt
   $ bash make-dist.sh -P full-build
```
### **Build with Maven**
To build BigDL directly using Maven, run the command below:

```sbt
  $ mvn clean package -DskipTests
```
After that, you can find that the three jar packages in `PATH_To_BigDL`/target/, where `PATH_To_BigDL` is the path to the directory of the BigDL. 

Note that the instructions above will build BigDL with Spark 1.5.x or 1.6.x (using Scala 2.10) for Linux, and skip the build of native library code. Similarly, you may customize the default behaviors by passing the following parameters to maven:

 * `-P mac`: build for maxOS
 * `-P spark_2.0`: build for Spark 2.0 (using Scala 2.11). (Again, it is highly recommended to use _**Java 8**_ when running with Spark 2.0; otherwise you may observe very poor performance.)
 * `-P full-build`: full build
 * `-P scala_2.10` (or `-P scala_2.11`): build using Scala 2.10 (or Scala 2.11) 

## **IDE Settings**
We set the scope of spark related library to `provided` in pom.xml. The reason is that we don't want package spark related jars which will make bigdl a huge jar, and generally as bigdl is invoked by spark-submit, these dependencies will be provided by spark at run-time.

This will cause a problem in IDE. When you run applications, it will throw `NoClassDefFoundError` because the library scope is `provided`.

You can easily change the scopes by the `all-in-one` profile.

* In Intellij, go to View -> Tools Windows -> Maven Projects. Then in the Maven Projects panel, Profiles -> click "all-in-one". 

## **Next Steps**
* To learn how to run BigDL programs (as either a local Java program or a Spark program), you can check out the [Getting Started Page](#getting-started).
* To learn the details of Python support in BigDL, you can check out the [Python Support Page](#python-support)
---
