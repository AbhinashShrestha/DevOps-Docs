It is mostly used for on perm (banking stuff) it has been super-seeded by compute engines that used hadoop as backend.

## For any issues with no data node go to hdfs-site.xml
find the path of namenode then rm -rf 

## Check if data node is working
```
hdfs dfsadmin -report
```
### Go to [Download page](https://dlcdn.apache.org/hadoop/common/hadoop-3.3.6/)
and follow [Doc](https://www.baeldung.com/linux/hadoop-install-configure)

## Commands 
```
start-dfs.sh, stop-dfs.sh and start-yarn.sh, stop-yarn.sh
jps
```
### Environmental Variables Setup

sudo vim $HADOOP_HOME/etc/hadoop/hadoop-env.sh
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.412.b08-2.el9.x86_64
```
sudo vim $HADOOP_HOME/etc/hadoop/hadoop-env.sh
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.412.b08-2.el9.x86_64
export PATH=$JAVA_HOME/bin:$PATH
export HADOOP_CLASSPATH+=" $HADOOP_HOME/lib/*.jar"
```

### After installing spark add the following to ~/.bashrc
```
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop
source ~/.bashrc
```

## Command to submit a Spark application in YARN cluster mode[doc](https://spark.apache.org/docs/latest/running-on-yarn.html)

```
/home/hadoop/spark-3.5.1-bin-hadoop3/bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn \
--deploy-mode client \
--driver-memory 512m \
--executor-memory 512m \
--executor-cores 1 \
/home/hadoop/spark-3.5.1-bin-hadoop3/examples/jars/spark-examples_2.12-3.5.1.jar \
10
```

### How to create a directory in hdfs 

```
hdfs dfs -mkdir /dir-name
```

### How to put a file in hdfs 

```
hdfs dfs -put local-file-path /hdfs-path
```


### How to view files 

```
hdfs dfs -ls /path-to-hdfs-dir
hdfs dfs -ls /my-dir
```
## Block size
 - The limit is from 128MB to 256MB in hadoop 2.x and 3.x (dfs.block.size)

#### Custom block size for individual file
```
hdfs dfs -D dfs.block.size=838860800 -put files-5GB /
```

### Make sure you configure core-site.xml and hdfs-site.xml

```core-site.xml
<configuration>
        <property>
                 <name>fs.defaultFS</name>
                 <value>hdfs://localhost:9000</value>
         </property>
          <property>
                 <name>hadoop.http.staticuser.user</name>
                 <value>hadoop</value>
         </property>
</configuration>
```

```hdfs-site.xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.name.dir</name>
        <value>file:/home/hadoop/mero-hadoop/hdfs/namenode</value>
    </property>
    <property>
        <name>dfs.data.dir</name>
        <value>file:/home/hadoop/mero-hadoop/hdfs/datanode</value>
    </property>
    <property>
            <name>dfs.block.size</name>
            <value>268435456</value> <!-- 1024*1024*x MB-->
    </property>
    <property>
            <name>dfs.webhdfs.enabled</name>
            <value>false</value>
    </property>
</configuration>
```
## References

Main [doc](https://www.baeldung.com/linux/hadoop-install-configure) [alt](https://www.tutorialspoint.com/how-to-install-and-configure-apache-hadoop-on-a-single-node-in-centos-8) [another](https://github.com/chimms1/Hadoop-Install-Guide)
