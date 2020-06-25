# 3 - Spark Installation Guide

We will install and configure Spark 2.4. In this tutorial it is accepted that:
- You have 3 machines (1 master, 2 slaves).
- You have installed Ubuntu 18.04.4 LTS in your *local* (master) machine and in slaves.
- You have updated and upgraded all packages in all machines.
- You have a valid internet connection for master and slaves.
- There is a user with the same name for all machines, such as ```spark-user```.
- You have passwordless SSH access to slaves from master.
- Hosts (```/etc/hosts```) file is configured for all machines, like shown below:

| Host-Name | IP Address     | Info                      |
|-----------|----------------|---------------------------|
| master    | 192.168.10.107 | Local Ubuntu 18 Machine   |
| slave-1   | 192.168.10.140 | Virtual Ubuntu 18 Machine |
| slave-2   | 192.168.10.141 | Virtual Ubuntu 18 Machine |

- Java 8 is installed.
- Hadoop, HDFS and Yarn is configured and installed.

**Note:** If one of this criteria is absent, please check the first guide [Virtual Machine Installation & Configuration Guide](../1%20-%20Virtual%20Machine%20Installation%20%26%20Configuration/guide.md) and second guide [HDFS & Yarn Installation Guide](../2%20-%20HDFS%20&%20Yarn%20Installation/guide.md).

## 3.1. Install Scala
> This section is for **all** (1 master, 2 slaves) machines. Perform all steps in all machines.

- Install Scala in *all machines*:
```bash
sudo apt install scala
```

- Confirm that Scala is installed correctly, _in all machines_:
```bash
scala -version
```
Output should look like _in all machines_:
```bash
Scala code runner version 2.11.12 -- Copyright 2002-2017, LAMP/EPFL
```

## 3.2. Download & Install Spark 2.4
- Go to https://downloads.apache.org/spark and find the current 2.4 release. At the time this document was written (June 25, 2020), Spark's version 2.4 was in the 5th sub-version and 6th sub-version. We will choose _version 2.4.5_ in this tutorial.

![SS-2-1](./screenshots/2_download_install_spark/1.png)
- We will download Spark in *Master* first and then transfer Spark files to slaves. In ***master machine***, download, untar & move Spark files using command-line and copy Spark files to remote slaves:
```bash
cd ~ # Go to home directory
wget https://downloads.apache.org/spark/spark-2.4.5/spark-2.4.5-bin-hadoop2.7.tgz
tar -xvf spark-2.4.5-bin-hadoop2.7.tgz  # Untar Spark files
rm ./spark-2.4.5-bin-hadoop2.7.tgz # Delete redundant archive file
scp -r ./spark-2.4.5-bin-hadoop2.7 spark-user@slave-1:~/ # Copy files to slave-1
scp -r ./spark-2.4.5-bin-hadoop2.7 spark-user@slave-2:~/ # Copy files to slave-2
```
**Note:** We will use binary distro builded with _Hadoop 2.7_, since we installed Hadoop 2.7 before. Make sure that your version is not _src (source)_ or different than your installed _Hadoop version_.

- In **all machines**, we will move the hadoop files under ```/opt``` directory for maintainability and handle permissions: 
```bash
# For master Machine
sudo mv spark-2.4.5-bin-hadoop2.7 /opt/ # Move Spark files to /opt/ directory
sudo ln -sf /opt/spark-2.4.5-bin-hadoop2.7 /opt/spark # Create symbolic link for abstraction
sudo chown spark-user:root /opt/spark* -R # Change user:spark-user, group:root
sudo chmod g+rwx /opt/spark* -R # Allow group to read-write-execute

# For slave-1 Machine
ssh slave-1
sudo mv spark-2.4.5-bin-hadoop2.7 /opt/ # Move Spark files to /opt/ directory
sudo ln -sf /opt/spark-2.4.5-bin-hadoop2.7 /opt/spark # Create symbolic link for abstraction
sudo chown spark-user:root /opt/spark* -R # Change user:spark-user, group:root
sudo chmod g+rwx /opt/spark* -R # Allow group to read-write-execute
exit # Logout from slave-1

# For slave-2 Machine
ssh slave-2
sudo mv spark-2.4.5-bin-hadoop2.7 /opt/ # Move Spark files to /opt/ directory
sudo ln -sf /opt/spark-2.4.5-bin-hadoop2.7 /opt/spark # Create symbolic link for abstraction
sudo chown spark-user:root /opt/spark* -R # Change user:spark-user, group:root
sudo chmod g+rwx /opt/spark* -R # Allow group to read-write-execute
exit # Logout from slave-2
```

- In **all machines**, append Spark paths to ```$PATH``` variable and export ```$SPARK_HOME```:
```bash
echo '
# For Spark
export PATH=$PATH:/opt/spark/bin
export SPARK_HOME=/opt/spark
' >> ~/.bashrc
source ~/.bashrc # Reload the changed bashrc file
echo $PATH $SPARK_HOME # Confirm that $PATH and $SPARK_HOME variable is changed properly
```
Output should be: ```<OTHER_PATHS>:/opt/hadoop/bin:/opt/spark/bin /opt/spark```.

## 3.3. Configure Spark
> We will configure *master* machine only.

- In *master machine*, we will edit ```$SPARK_HOME/conf/spark-env.sh```. But before, change ```spark-env.sh.template``` to ```spark-env.sh```:
```bash
mv $SPARK_HOME/conf/spark-env.sh.template $SPARK_HOME/conf/spark-env.sh
```

- Open up ```$SPARK_HOME/conf/spark-env.sh``` with a text editor (e.g., GNU Emacs, Vim, Gedit, Nano) and set the following parameters by appending the following lines:
```bash
export SPARK_MASTER_HOST=master
export JAVA_HOME=/usr/lib/jvm/current-java
```

- In *master machine*, we will edit ```$SPARK_HOME/conf/slaves```. But before, change ```slaves.template``` to ```slaves```:
```bash
mv $SPARK_HOME/conf/slaves.template $SPARK_HOME/conf/slaves
```

- Open up ```$SPARK_HOME/conf/slaves``` with a text editor.  If ```localhost``` line is present in the file, delete it. Add the following lines only:
```
slave-1
slave-2
```

Your ```$SPARK_HOME/conf/slaves``` file should look like:

![SS-3-1](./screenshots/3_configure_spark/1.png)

- In **master machine**, start Spark:
```bash
$SPARK_HOME/sbin/start-all.sh
```

- Validate that everything started right by running the ```jps``` command as spark-user on all machines.

On master node, you should see ```Master``` like shown below:

![SS-3-2](./screenshots/3_configure_spark/2.png)

On slave nodes (slave-1, slave-2), you should see ```Worker``` like shown below:

![SS-3-3](./screenshots/3_configure_spark/3.png)

![SS-3-4](./screenshots/3_configure_spark/4.png)

- If everything is OK, you can now access the Spark Web UI by browsing via the link [http://master:8080/](http://master:8080/):

![SS-3-5](./screenshots/3_configure_spark/5.png)

If you see a web page like shown above and _**Alive Workers**_ attribute is _2_, then everything is OK. This indicates that you have 1 master-node (the one which this web-site runs on) and 2 alive worker-nodes (as _alive nodes_).

- In **master machine**, if you want to stop Spark run the following command:
```bash
$SPARK_HOME/sbin/stop-all.sh
```

## References
* https://medium.com/ymedialabs-innovation/apache-spark-on-a-multi-node-cluster-b75967c8cb2b
