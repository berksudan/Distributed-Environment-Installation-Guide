# HDFS & Yarn & Spark Installation Guide

We will install and configure virtual machines using VirtualBox. In this tutorial it is accepted that:
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

**Note:** If one of this criteria is absent, please check the first guide ***Virtual Machine Installation & Configuration Guide***, via the [link](../1-%20Virtual%20Machine%20Installation%20%26%20Configuration/guide.md).

## 1.1. Install Java 8
> This section is for **all** (1 master, 2 slaves) machines. Perform all steps in all machines.

**Note \#1:** This approach is valid when using version 8 (1.8) of Java. For Java-11 or other versions, you should perform the similar but not the same steps. 
**Note \#2:** OpenJDK (which is a free and open-source implementation of the Java Platform, Standard Edition) is used here.

- Install Java Runtime Environment (JRE) and Java Development Kit (JRE) in *all machines*:
```bash
sudo apt install openjdk-8-jre
sudo apt install openjdk-8-jdk
java -version
```

- Your JVM (Java Virtual Machine) directory should be located at ```/usr/lib/jvm```. We will create a symbolic link to your jvm for abstraction:
```bash
cd /usr/lib/jvm
sudo ln -sf java-8-openjdk* current-java
```
And we can access to our jvm using path ```/usr/lib/jvm/current-java```.

- Now we will add ***java path***:
```bash
su spark-user # You must be spark-user, if you are ignore this command.
echo 'export JAVA_HOME=/usr/lib/jvm/current-java' >> ~/.bashrc
```
**Attention:** If java will also be used by another users, you can do the same for thesesudo apt users.

- Confirm in all machines that ```$JAVA_HOME``` variable is set correctly:
```bash
source ~/.bashrc # Reload the changed bashrc file
echo $JAVA_HOME
```
You should see ```/usr/lib/jvm/current-java``` in command-line.

## 1.2. Download & Install Hadoop 2.7
- Go to http://apache.claz.org/hadoop/common and find the current 2.7 release. At the time this document was written (June 23, 2020), Hadoop's version 2.7 was in the 7th sub-version.

![SS-2-1](./screenshots/2_download_install_hadoop2.7/1.png)
- We will download Hadoop in *Master* first and then transfer Hadoop files to slaves. In ***master machine***, download, untar & move Hadoop files using command-line and copy hadoop files to remote slaves:
```bash
cd ~ # Go to home directory
wget http://apache.claz.org/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz
tar -xvf hadoop-2.7.7.tar.gz # Untar Hadoop files
scp -r ./hadoop-2.7.7 spark-user@slave-1:~/ # Copy files to slave-1
scp -r ./hadoop-2.7.7 spark-user@slave-2:~/ # Copy files to slave-2
rm hadoop-2.7.7.tar.gz # Delete redundant archive file
```

- In **all machines**, we will move the hadoop files under ```/opt``` directory for maintainability and handle permissions: 
```bash
# For master Machine
sudo mv hadoop-2.7.7 /opt/ # Move Hadoop files to /opt/ directory
sudo ln -sf /opt/hadoop-2.7.7 /opt/hadoop # Create symbolic link for abstraction
sudo chown spark-user:root /opt/hadoop* -R # Change user:spark-user, group:root
sudo chmod g+rwx /opt/hadoop* -R # Allow group to read-write-execute

# For slave-1 Machine
ssh slave-1
sudo mv hadoop-2.7.7 /opt/ # Move Hadoop files to /opt/ directory
sudo ln -sf /opt/hadoop-2.7.7 /opt/hadoop # Create symbolic link for abstraction
sudo chown spark-user:root /opt/hadoop* -R # Change user:spark-user, group:root
sudo chmod g+rwx /opt/hadoop* -R # Allow group to read-write-execute
exit # Logout from slave-1

# For slave-2 Machine
ssh slave-2
sudo mv hadoop-2.7.7 /opt/ # Move Hadoop files to /opt/ directory
sudo ln -sf /opt/hadoop-2.7.7 /opt/hadoop # Create symbolic link for abstraction
sudo chown spark-user:root /opt/hadoop* -R # Change user:spark-user, group:root
sudo chmod g+rwx /opt/hadoop* -R # Allow group to read-write-execute
exit # Logout from slave-2
```

- In **all machines**, append hadoop paths to ```$PATH``` variable and set ```$HADOOP_HOME``` variable:
```bash
echo 'export PATH=$PATH:/opt/hadoop/bin:/opt/hadoop/sbin' >> ~/.bashrc
echo 'export HADOOP_HOME=/opt/hadoop' >> ~/.bashrc
source ~/.bashrc
echo $PATH # Confirm that $PATH variable is changed properly
echo $HADOOP_HOME # Confirm that $HADOOP_HOME variable is set properly
```
Outputs should be:
```bash
# For $PATH:
<OTHER_PATHS>:/opt/hadoop/bin:/opt/hadoop/sbin
# For $HADOOP_HOME:
/opt/hadoop
```

## 1.2. Configure Hadoop
> We will first configure *master* machine and then transfer configuration file to remote slaves.

- In *master machine*, we will edit ```$HADOOP_HOME/etc/hadoop/core-site.xml```.  Your "configuration" tag in this file should look like this:
```xml
<configuration>
	<property>
		<name>fs.default.name</name>
		<value>hdfs://master:9000</value>
	</property>
</configuration>
```

- In *master machine*, we will edit ```$HADOOP_HOME/etc/hadoop/hdfs-site.xml```.  Your "configuration" tag in this file should look like this:
```xml
<configuration>
	<property>
		<name>dfs.namenode.name.dir</name>
		<value>$HADOOP_HOME/data/namenode</value>
	</property>
	<property>
		<name>dfs.datanode.data.dir</name>
		<value>$HADOOP_HOME/data/datanode</value>
	</property>
	<property>
		<name>dfs.replication</name>
		<value>2</value>
	</property>
</configuration>
```
- In *master machine*, we will edit ```$HADOOP_HOME/etc/hadoop/slaves```. If ```localhost``` line is present in the file delete it. Add the following lines only:
```
slave-1
slave-2
```
**Note that:** ```slaves``` file renamed to ```workers``` in further Hadoop versions. So if you follow this tutorial for further versions and sure that a file named ```workers``` exists under directory ```$HADOOP_HOME/etc/hadoop/```, edit ```workers``` file.

## References
* https://dzone.com/articles/install-a-hadoop-cluster-on-ubuntu-18041
