---
title: "Install Hadoop on Windows 10"
author: "Santhosh Kumar N"
date: "2023-09-26"
output:
  pdf_document:
    toc: yes
    number_sections: yes
  html_document:
    toc: yes
    df_print: paged
---

# Install and Configure Hadoop on Windows 10

## Prerequisites

-   ***Java 8 runtime environment (JRE)***

    <https://www.java.com/en/download/windows_offline.jsp>

-   ***Java 8 development Kit (JDK)***

    <https://www.oracle.com/java/technologies/downloads/>

-   ***Downloaded Hadoop binaries***

    <https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.2.2/hadoop-3.2.2.tar.gz>

## Install and Configure Prerequisites

1.  Install Java 8 runtime environment (JRE)

    ![](images/01-04.png){width="404"}

2.  Java 8 development Kit (JDK)

    ![](images/02-01.png){width="605"}

3.  Downloaded Hadoop binaries and unzip

    ***Note***: Install '7Zip' to Extract Hadoop Binaries (<https://www.7-zip.org/download.html>)

    ![](images/03-01.png){width="562"}

4.  Install JRE

    ![](images/04-01.png){width="400"}

5.  Install Java DSK

    ![](images/05-01.png){width="402"}

    ![](images/06.png){width="402"}

    ![](images/07.png){width="300"}

6.  Setting up environment variables

    -   (Right-click \> properties on My Computer icon) and click on the "Advanced system settings" link.

    -   Set Environment Variable

        -   Click 'Advanced System Settings'

            ![](images/08.png){width="447"}

        -   Click 'Advanced tab'

        -   Click 'Environment Variables'

        -   Go to 'System Properties' section

            ![](images/09.png){width="447"}

        -   In the 'Environment Variables' dialog, press the 'New' button to add a new variable.

        -   There are two variables to define

            -   ***JAVA_HOME***: JDK installation folder path

                ![](images/10.png){width="392"}

            -   ***HADOOP_HOME***: Hadoop installation folder path

                ![](images/11.png){width="388"}

    -   Edit the 'PATH' variable to add the '***JAVA_HOME*** and'***HADOOP_HOME***' binaries paths.

        ![](images/12.png){width="370"}

    -   ![](images/13-01.png){width="300"}

## Configure Hadoop

1.  Extract the Hadoop Files to C:Drive

    ![](images/14.png){width="330"}

2.  Add the Hadoop native IO libraries

    -   Which can be found in the following GitHub repository <https://github.com/cdarlint/winutils>

    -   Note : Since we are installing `Hadoop 3.2.2`, we should download the files located in `hadoop-3.2.2/bin/`

    -   Copy them into the `'C:\hadoop-3.2.2\bin'` directory

        ![](images/15.png){width="490"}

        ![](images/16.png){width="489"}

3.  Check for the Hadoop Installation

    1.  Open the `Power Shell` and run command '`hadoop -version`'

    2.  If we get below the error ***JAVA_HOME*** is incorrectly set' means we should use the `windows 8.3` path instead.

        -   Changes on the `Environment Varibales`
            -   Use '`Progra~1'` instead of'`Program Files'`

            -   Use '`Progra~2'` instead of'`Program Files(x86)'`

        ![](images/17.png){width="473"}

## Configuring Hadoop cluster

1.  There are four files we should alter to configure Hadoop cluster:

    -   %HADOOP_HOME%\\etc\\hadoop\\hdfs-site.xml
    -   %HADOOP_HOME%\\etc\\hadoop\\core-site.xml
    -   %HADOOP_HOME%\\etc\\hadoop\\mapred-site.xml
    -   %HADOOP_HOME%\\etc\\hadoop\\yarn-site.xml

2.  HDFS site configuration

    1.  Create a directory to store all master node (name node) data and another one to store data (data node).

    2.  Create the following directories:

        -   C:\\hadoop-3.2.2\\data\\dfs\\namenode
        -   C:\\hadoop-3.2.2\\data\\dfs\\datanode

        ![](images/18.png){width="426"}

    3.  Open '`hdfs-site.xml`' from location '`C:\hadoop-3.2.2\etc\hadoop`'

        Add the following properties within the \<configuration\>\</configuration\> element

        ``` xml
        <property>
          <name>dfs.replication</name>
          <value>1</value>
        </property>
        <property>
          <name>dfs.namenode.name.dir</name>
          <value>file:///C:/hadoop-3.2.2/data/dfs/namenode</value>
        </property>
        <property>
          <name>dfs.datanode.data.dir</name>
          <value>file:///C:/hadoop-3.2.2/data/dfs/datanode</value>
        </property>
        ```

        ![](images/19.png){width="420"}

        **Note**: that we have set the replication factor to 1 since we are creating a single node cluster.

    4.  Core site configuration for name node URL

        -   Open 'core-site.xml' add following XML code into the \<configuration\> \</configuration\> element

            ``` xml
            <property>
               <name>fs.default.name</name>
               <value>hdfs://localhost:9820</value>
            </property>
            ```

    5.  Map Reduce site configuration

        -   Open 'mapred-site.xml'

        -   Add the following XML code into the \<configuration\> \</configuration\> element

            ``` xml
             <property>
              <name>mapreduce.framework.name</name>
                <value>yarn</value>
                <description>MapReduce framework name</description>
             </property>
            ```

    6.  Yarn `site configuration`

        -   Open '`yarn-site.xml`'

        -   Add the following `XML` code into the `<configuration> </configuration>` element

            ``` xml
            <property>
               <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
               <description>Yarn Node Manager Aux Service</description>
            </property>
            ```

    7.  Formatting `Name node`

        -   Open the powershell run the command '`hdfs namenode -format`'

            ![](images/20.png){width="443"}

    8.  Starting `Hadoop services`

        1.  Open the Powershell and Navigate to '`C:\hadoop-3.2.2\sbin`'

        2.  Run the command '`.\start-dfs.cmd`'

            **Note** : Two command prompt windows will open (one for the name node and one for the data node)

            ![](images/21-01.png){width="461"}

    9.  Start the Hadoop '`Yarn service'`

        -   Open the `Powershell` and Navigate to '`C:\hadoop-3.2.2\sbin`'

        -   Run the command '`./start-yarn.cmd`'

            **Note** : Two command prompt windows will open (one for the resource manager and one for the node manager)

            ![](images/22.png){width="462"}

    10. Make sure that all services started successfully

        -   Open the `Powershell` and Navigate to '`C:\hadoop-3.2.2\sbin`'

        -   Run the comand '`jps`'

        -   It should display the following services

            -   14560 DataNode

            -   4960 ResourceManager

            -   5936 NameNode

            -   768 NodeManager

            -   14636 Jps

            ![](images/23.png){width="438"}

## Check the Hadoop Web UI

1.  There are three web user interfaces to be used:
    1.  Name node web page

        <http://localhost:9870/dfshealth.html>

        ![](images/24.png){width="450"}

    2.  Data node web page

        <http://localhost:9864/datanode.html>

        ![](images/25.png){width="454"}

    3.  Yarn web page

        <http://localhost:8088/cluster>

        ![](images/26.png){width="456"}
