# HDFS, Hadoop, Yarn & Spark Stand-Alone Cluster Installation in Ubuntu 18.04.4 LTS

**At the end we will have:**

- 1 Master Ubuntu-18.04.4 LTS Desktop  Local Machine
- 2 Slave Ubuntu-18.04.4 LTS Server Virtual Machine
## Guided
https://dzone.com/articles/install-a-hadoop-cluster-on-ubuntu-18041

2 virtual machines ubuntu 18.04, 1 local 18.04 machine. 1 namenodes, 2 datanodes.

## Install and Configure Virtual Machines using Virtualbox
Install VirtualBox using bash:
```bash
sudo apt install virtualbox
```
Get 18.04.4 LTS Server Install Image: [Official Ubuntu 18.04.4 LTS Live Server ISO](http://releases.ubuntu.com/bionic/ubuntu-18.04.4-live-server-amd64.iso)







## In VirtualBox
Use bridged network and check the ip for being started with "192.168"


## To ssh localhost without password, used:
https://www.linuxbabe.com/linux-server/setup-passwordless-ssh-login

Note: remote-user@server-ip -> hadoopuser@localhost


## For environment variables in \~/.bashrc
> https://stackoverflow.com/a/35695878 

```bash
# For Java & Hadoop & Spark ############
## For Java
export JAVA_HOME="/usr/lib/jvm/current-java"

## For Hadoop
export HADOOP_HOME="/opt/hadoop"
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export HADOOP_PREFIX=$HADOOP_HOME
export HADOOP_LIBEXEC_DIR=$HADOOP_HOME/libexec
export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native:$JAVA_LIBRARY_PATH
export HADOOP_CONF_DIR=$HADOOP_PREFIX/etc/hadoop

## For Yarn
export YARN_HOME=$HADOOP_HOME

## For HDFS
export HADOOP_HDFS_HOME=$HADOOP_HOME

# For Spark
export SPARK_HOME="/opt/spark"
export PATH=$PATH:$SPARK_HOME/bin
# ######################################
```

## Java-Home Update
java-home in $HADOOP_HOME/etc/hadoop/hadoop-env.sh updated in each node

## Check Validity of Hadoop (HDFS) and Yarn Job Scheduler
### For hdfs-web-ui
Checked: [master:50070/dfshealth.html](master:50070/dfshealth.html)
Number of active nodes must be "2"

### For yarn-web-ui
Checked:  [http://master:8088/cluster](http://master:8088/cluster)


# Install Spark

## Main Guide
https://medium.com/ymedialabs-innovation/apache-spark-on-a-multi-node-cluster-b75967c8cb2b

# For spark file transfer
```bash
# scp /usr/local/hadoop/etc/hadoop/* hadoop2.admintome.lab:/usr/local/hadoop/etc/hadoop/
# scp /usr/local/hadoop/etc/hadoop/* hadoop3.admintome.lab:/usr/local/hadoop/etc/hadoop/
```