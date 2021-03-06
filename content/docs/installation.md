<!--
    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.
-->

# Installation Guide
This tutorial will guide you through the installation and configuration of CarbonData in the following two modes :

* [On Standalone Spark cluster](https://cwiki.apache.org/confluence/display/CARBONDATA/Cluster+deployment+guide)  
* [On Spark on Yarn cluster](https://cwiki.apache.org/confluence/display/CARBONDATA/Cluster+deployment+guide)

followed by :
* [Query Execution using Carbon Thrift Server](https://cwiki.apache.org/confluence/display/CARBONDATA/Cluster+deployment+guide)

## Installing and Configuring CarbonData on "Standalone Spark" Cluster
   
   ### Prerequisite
   * Hadoop HDFS and Yarn should be installed and running.
   * Spark should be installed and running in all the clients.
   * CarbonData user should have permission to access HDFS.
   
   ### Procedure
   The following steps are only for Driver Nodes.(Driver nodes are the one which starts the spark context.)
   1. [Build the CarbonData](https://cwiki.apache.org/confluence/display/CARBONDATA/Building+CarbonData+And+IDE+Configuration) project and get the assembly jar from "./assembly/target/scala-2.10/carbondata_xxx.jar" and put in the "<SPARK_HOME>/carbonlib" folder. 
       
       (Note: - Create the carbonlib folder if does not exists inside "<SPARK_HOME>" path.)
      
   2.  carbonlib folder path must be added in Spark classpath. (Edit "<SPARK_HOME>/conf/spark-env.sh" file and modify the value of SPARK_CLASSPATH by appending "<SPARK_HOME>/carbonlib/*" to the existing value)  
   
   3.  Copy the carbon.properties.template to "<SPARK_HOME>/conf/carbon.properties" folder from "./conf/" of CarbonData repository.
     
   4.  Copy "carbonplugins" folder  to "<SPARK_HOME>/carbonlib" folder from "./processing/" folder of CarbonData repository.
       
        (Note: -carbonplugins will contain .kettle folder.)
   
   5.  In Spark node, configure the properties mentioned as the below table in "<SPARK_HOME>/conf/spark-defaults.conf" file   
         
| Property | Description | Value |
|---------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| carbon.kettle.home | Path that will be used by CarbonData internally to create graph for loading the data | $SPARK_HOME /carbonlib/carbonplugins |
| spark.driver.extraJavaOptions | A string of extra JVM options to pass to the driver. For instance, GC settings or other logging. | -Dcarbon.properties.filepath=$SPARK_HOME/conf/carbon.properties |
| spark.executor.extraJavaOptions | A string of extra JVM options to pass to executors. For instance, GC settings or other logging. NOTE: You can enter multiple values separated by space. | -Dcarbon.properties.filepath=$SPARK_HOME/conf/carbon.properties |
    
   6.  Add the following properties in "<SPARK_HOME>/conf/" carbon.properties:
 
| Property | Required | Description | Example | Remark |
|----------------------|----------|----------------------------------------------------------------------------------------|-------------------------------------|---------|
| carbon.storelocation | NO | Location where data Carbon will create the store and write the data in its own format. | hdfs://IP:PORT/Opt/CarbonStore | Propose |
| carbon.kettle.home | YES | Path that will used by Carbon internally to create graph for loading the data. | $SPARK_HOME/carbonlib/carbonplugins |  |
     
   7.  Installation verification,for example:
   ```
   ./spark-shell --master spark://IP:PORT --total-executor-cores 2 --executor-memory 2G
   ``` 
 Note: Make sure that user should have permission of carbon jars and files through which driver and executor will start.
       
To get started with CarbonData : [Quick Start](https://cwiki.apache.org/confluence/display/CARBONDATA/Quick+Start) ,[DDL Operations](https://cwiki.apache.org/confluence/display/CARBONDATA/DDL+operations+on+CarbonData)       

## Installing and Configuring Carbon on "Spark on YARN" Cluster
   
   This section provides the procedure to install Carbon on "Spark on YARN" cluster.
   ### Prerequisite
   * Hadoop HDFS and Yarn should be installed and running.
   * Spark should be installed and running in all the clients.
   * CarbonData user should have permission to access HDFS.
 
   ### Procedure
   1. [Build the CarbonData](https://cwiki.apache.org/confluence/display/CARBONDATA/Building+CarbonData+And+IDE+Configuration) project and get the assembly jar from "./assembly/target/scala-2.10/carbondata_xxx.jar" and put in the "<SPARK_HOME>/carbonlib" folder. 
             
      (Note: - Create the carbonlib folder if does not exists inside "<SPARK_HOME>" path.)
   
   2. Copy the carbon.properties.template to "<SPARK_HOME>/conf/carbon.properties" folder from "./conf/" of CarbonData repository.
      carbonplugins will contain .kettle folder. 
   
   3. Copy the "carbon.properties.template" to "<SPARK_HOME>/conf/carbon.properties" folder from conf folder of carbondata repository.
  
   4. Modify the parameters in "spark-default.conf" located in the "<SPARK_HOME>/conf"      

| Property | Description | Value |
|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| spark.master | Set this value to run the Spark in yarn cluster mode. | Set "yarn-client" to run the Spark in yarn cluster mode. |
| spark.yarn.dist.files | Comma-separated list of files to be placed in the working directory of each executor. | "<YOUR_SPARK_HOME_PATH>"/conf/carbon.properties |
| spark.yarn.dist.archives | Comma-separated list of archives to be extracted into the working directory of each executor. | "<YOUR_SPARK_HOME_PATH>"/carbonlib/carbondata_xxx.jar |
| spark.executor.extraJavaOptions | A string of extra JVM options to pass to executors. For instance  NOTE: You can enter multiple values separated by space. | -Dcarbon.properties.filepath=carbon.properties |
| spark.executor.extraClassPath | Extra classpath entries to prepend to the classpath of executors. NOTE: If SPARK_CLASSPATH is defined in spark-env.sh, then comment it and append the values in below parameter spark.driver.extraClassPath | "<YOUR_SPARK_HOME_PATH>"/carbonlib/carbonlib/carbondata_xxx.jar |
| spark.driver.extraClassPath | Extra classpath entries to prepend to the classpath of the driver. NOTE: If SPARK_CLASSPATH is defined in spark-env.sh, then comment it and append the value in below parameter spark.driver.extraClassPath. | "<YOUR_SPARK_HOME_PATH>"/carbonlib/carbonlib/carbondata_xxx.jar |
| spark.driver.extraJavaOptions | A string of extra JVM options to pass to the driver. For instance, GC settings or other logging. | -Dcarbon.properties.filepath="<YOUR_SPARK_HOME_PATH>"/conf/carbon.properties |
| carbon.kettle.home | Path that will used by Carbon internally to create graph for loading the data. | "<YOUR_SPARK_HOME_PATH>"/carbonlib/carbonplugins |      
      
   5. Add the following properties in <SPARK_HOME>/conf/ carbon.properties:
   
| Property | Required | Description | Example | Default Value |
|----------------------|----------|----------------------------------------------------------------------------------------|-------------------------------------|---------------|
| carbon.storelocation | NO | Location where data Carbon will create the store and write the data in its own format. | hdfs://IP:PORT/Opt/CarbonStore | Propose |
| carbon.kettle.home | YES | Path that will used by Carbon internally to create graph for loading the data. | $SPARK_HOME/carbonlib/carbonplugins |  |
   
   6. Installation verification 
   ```
   ./bin/spark-shell --master yarn-client --driver-memory 1g --executor-cores 2 --executor-memory 2G 
   ```
   Note: Make sure that user should have permission of carbon jars and files through which driver and executor will start.
      
To get started with CarbonData : [Quick Start](https://cwiki.apache.org/confluence/display/CARBONDATA/Quick+Start) ,[DDL Operations](https://cwiki.apache.org/confluence/display/CARBONDATA/DDL+operations+on+CarbonData)       
      
## Query execution using Carbon thrift server

### Start Thrift server         
   a. cd <SPARK_HOME>
   
   b. Run below command to start the Carbon thrift server
   ```
  ./bin/spark-submit --conf spark.sql.hive.thriftServer.singleSession=true --class org.apache.carbondata.spark.thriftserver.CarbonThriftServer
   $SPARK_HOME/carbonlib/$CARBON_ASSEMBLY_JAR <carbon_store_path>
   ```  
| Parameter | Description | Example |
|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| CARBON_ASSEMBLY_JAR | Carbon assembly jar name present in the ""/carbonlib/ folder. | carbondata_2.10-0.1.0-incubating-SNAPSHOT-shade-hadoop2.7.2.jar |
| carbon_store_path | This is parameter to the CarbonThriftServer class. This a HDFS path where carbon files will be kept. Strongly Recommended to put same as carbon.storelocation parameter of carbon.proeprties. | hdfs//hacluster/user/hive/warehouse/carbon.storehdfs//10.10.10.10:54310 /user/hive/warehouse/carbon.store |
   
### Examples
   1. Start with default memory and executors
   ```
   ./bin/spark-submit --conf spark.sql.hive.thriftServer.singleSession=true --class org.apache.carbondata.spark.thriftserver.CarbonThriftServer $SPARK_HOME/carbonlib/carbondata_2.10-0.1.0-incubating-SNAPSHOT-shade-hadoop2.7.2.jar hdfs://hacluster/user/hive/warehouse/carbon.store
   ```
   2. Start with Fixed executors and resources
   ```
   ./bin/spark-submit --conf spark.sql.hive.thriftServer.singleSession=true --class org.apache.carbondata.spark.thriftserver.CarbonThriftServer --num-executors 3 --driver-memory 20g --executor-memory 250g --executor-cores 32 /srv/OSCON/BigData/HACluster/install/spark/sparkJdbc/lib/carbondata_2.10-0.1.0-incubating-SNAPSHOT-shade-hadoop2.7.2.jar hdfs://hacluster/user/hive/warehouse/carbon.store
   ```
### Connecting to Carbon Thrift Server Using Beeline
   
   ```
   cd <SPARK_HOME>
   ./bin/beeline jdbc:hive2://<thrftserver_host>:port
    
   Example 
   ./bin/beeline jdbc:hive2://10.10.10.10:10000
   ```
