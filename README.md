Asiainfo streaming-app
=====


streaming-app  requires:

    Scala 2.10.3
    Hbase 0.98.1
    kafka_2.10-0.8.1
    



Configuration
=====
  conf/spark-env.sh
  <pre>
  export HADOOP_CONF_DIR=/home/ocdc/hadoop-2.3.0-cdh5.0.0-och3.1.0/etc/hadoop
  export SPARK_YARN_APP_JAR=/home/ocdc/spark_dev/target/spark-dev-V00B01C00-SNAPSHOT-jar-with-dependencies.jar
  export SPARK_JAR=/home/ocdc/spark-assembly-0.9.1-hadoop2.3.0-cdh5.0.0.jar
  
  </pre>
  
  
  
  conf/Sample.xml
  
  Sample.xml is used for processing flow, used for filtering rules, data judgment conditions, judging and dynamic accumulation factor
  
  <pre>
  ................
  
     <dataSource name="ds1">
        <class>com.asiainfo.ocdc.streaming.impl.KafkaSource</class>
        <zkQuorum></zkQuorum>
        <description>Specifies the ZooKeeper connection string in the form hostname:port,
        where hostname and port are the host and port for a node in your ZooKeeper cluster.
        To allow connecting through other ZooKeeper nodes when that host is down
        you can also specify multiple hosts in the form 
        hostname1:port1,hostname2:port2,hostname3:port3</description>
        <topics>cmbb3</topics>
        <description>topicProducername</description>
        <groupId>test-consumer-group</groupId>
        <consumerNum>3</consumerNum>
        <separator> </separator>
        <stream_columns>a,b,c,d,e,f,count,fee</stream_columns>
    </dataSource>

    <step>
        <class>com.asiainfo.ocdc.streaming.impl.StreamFilter</class>
        <HBaseTable>t1</HBaseTable>
        <HBaseCell>cell</HBaseCell>
        <HBaseKey>lac,cell</HBaseKey>
        <output>b,c,t1.cell,count,fee</output>
        <where>t1.cell!=null</where>
    </step>

    <step>
        <class>com.asiainfo.ocdc.streaming.impl.DynamicOperate</class>
        <HBaseTable>t2</HBaseTable>
        <HBaseKey>b</HBaseKey>
        <HBaseCells>Count,Fee</HBaseCells>
        <expressions>t2.Count+count,t2.Fee+fee</expressions>
        <output>b,c,t1.cell,count,fee</output>
    </step>

    <step>
        <class>com.asiainfo.ocdc.streaming.impl.KafkaOut</class>
        <topic>topicName</topic>
        <description>topic ConsumerName</description>
        <broker>dev001:9092</broker>
        <description>The port the socket server listens on,
        hostname1:port1,hostname2:port2,hostname3:port3</description>
        <OutCol>b,c</OutCol>
    </step>
    ......
  </pre>
Building streaming-app
=====
  <pre>
  mvn package
  </pre>
  
Start spark streaming
=====
The command format is as follows,To start the streaming app application
  <pre>
  ./bin/start-streaming-app.sh  &lt;streaming-app-name&gt; &lt;time&gt;  &lt;file&gt;
  </pre>
Parameter 1, When should the different configuration XML file, the corresponding streamingappname                         
Parameter 2, flow interval refresh time(seconds)                                                                          
Parameter 3, The configuration file (as:conf/Sample.xml)


Running Tests
=====
Test documentation, reference streaming-app project wiki
https://github.com/asiainfo/streaming-app/wiki
  
