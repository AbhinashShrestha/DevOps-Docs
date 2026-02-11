set up hive config [Doc](https://www.tutorialspoint.com/hive/hive_installation.htm) [Main doc](https://www.edureka.co/blog/apache-hive-installation-on-ubuntu) [alt](https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.3.2/bk_dataintegration/content/beeline-vs-hive-cli.html)

## Beeline CLI
```
beeline

Beeline version 3.1.3 by Apache Hive

beeline> !connect jdbc:hive2:// 

Connecting to jdbc:hive2://

Enter username for jdbc:hive2://: APP

Enter password for jdbc:hive2://: mine
```

### Download Derby [link](https://db.apache.org/derby/releases/release-10_14_2_0.html)

### Initialize the derby db
```
schematool -initSchema -dbType derby
```

### It will create /home/hadoop/metastore_db

then in the place where derby will create the metadata_db we must 777 permission to that folder
```$HIVE_HOME/conf/hive-site.xml 

<?xml version="1.0" encoding="UTF-8" standalone="no"?>

<?xml-stylesheet type="text/xsl" href="configuration.xsl"?><!--

   Licensed to the Apache Software Foundation (ASF) under one or more

   contributor license agreements.  See the NOTICE file distributed with

   this work for additional information regarding copyright ownership.

   The ASF licenses this file to You under the Apache License, Version 2.0

   (the "License"); you may not use this file except in compliance with

   the License.  You may obtain a copy of the License at

  

       http://www.apache.org/licenses/LICENSE-2.0

  

   Unless required by applicable law or agreed to in writing, software

   distributed under the License is distributed on an "AS IS" BASIS,

   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.

   See the License for the specific language governing permissions and

   limitations under the License.

--><configuration>

  <!-- WARNING!!! This file is auto generated for documentation purposes ONLY! -->

  <!-- WARNING!!! Any changes you make to this file will be ignored by Hive.   -->

  <!-- WARNING!!! You must make your changes in hive-site.xml instead.         -->

  <!-- Hive Execution Parameters -->

<property>

<name>javax.jdo.option.ConnectionURL</name>

<value>jdbc:derby:;databaseName=/home/edureka/apache-hive-2.1.0-bin/metastore_db;create=true</value>

<description>

JDBC connect string for a JDBC metastore.

To use SSL to encrypt/authenticate the connection, provide database-specific SSL flag in the connection URL.

For example, jdbc:postgresql://myhost/db?ssl=true for postgres database.

</description>

</property>

<property>

<name>hive.metastore.warehouse.dir</name>

<value>/user/hive/warehouse</value>

<description>location of default database for the warehouse</description>

</property>

<property>

<name>hive.metastore.uris</name>

<value/>

<description>Thrift URI for the remote metastore. Used by metastore client to connect to remote metastore.</description>

</property>

<property>

   <name>javax.jdo.option.ConnectionURL</name>

   <value>jdbc:derby:;databaseName=/home/hadoop/metastore_db;create=true </value>

   <description>JDBC connect string for a JDBC metastore </description>

</property>

<property>

<name>javax.jdo.PersistenceManagerFactoryClass</name>

<value>org.datanucleus.api.jdo.JDOPersistenceManagerFactory</value>

<description>class implementing the jdo persistence</description>

</property>

</configuration>
```

```$HIVE_HOME/conf/jpox.properties 

javax.jdo.PersistenceManagerFactoryClass =

  

org.jpox.PersistenceManagerFactoryImpl

org.jpox.autoCreateSchema = false

org.jpox.validateTables = false

org.jpox.validateColumns = false

org.jpox.validateConstraints = false

org.jpox.storeManagerType = rdbms

org.jpox.autoCreateSchema = true

org.jpox.autoStartMechanismMode = checked

org.jpox.transactionIsolation = read_committed

javax.jdo.option.DetachAllOnCommit = true

javax.jdo.option.NontransactionalRead = true

javax.jdo.option.ConnectionDriverName = org.apache.derby.jdbc.ClientDriver

javax.jdo.option.ConnectionURL = jdbc:derby:metastore_db;create=true

javax.jdo.option.ConnectionUserName = APP

javax.jdo.option.ConnectionPassword = mine
```


## Database import from HDFS storage

1. Download the proper sample database
2. Now give appropriate permissions
		 hdfs dfs -chmod g+w /user/hive/data.csv


```
CREATE TABLE IF NOT EXISTS financial_data (
    Year INT,
    Industry_aggregation_NZSIOC STRING,
    Industry_code_NZSIOC STRING,
    Industry_name_NZSIOC STRING,
    Units STRING,
    Variable_code STRING,
    Variable_name STRING,
    Variable_category VARCHAR,
    Value STRING,
    Industry_code_ANZSIC06 VARCHAR
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;

```

[Reference](https://www.geeksforgeeks.org/hive-load-data-into-table/)

```
LOAD DATA INPATH '/user/hive/warehouse/annual-enterprise-survey-2023-financial-year-provisional.csv' INTO TABLE financial_data;
```

```
SELECT * FROM financial_data  LIMIT 10;
```

## [CHEATSHEET](https://hortonworks.com/wp-content/uploads/2016/05/Hortonworks.CheatSheet.SQLtoHive.pdf)
[alt](https://sparkbyexamples.com/apache-hive/apache-hive-installation-on-hadoop/)

