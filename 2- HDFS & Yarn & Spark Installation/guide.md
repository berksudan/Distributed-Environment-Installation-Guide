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

## 1.1. Install Java 8 in All Machines
> This section is for **all** (1 master, 2 slaves) machine.
**Note \#1:** This approach is valid when using version 8 (1.8) of Java. For Java-11 or other versions, you should follow the similar but not the same steps. 
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

- Now we will add ***java path***.
```bash
su spark-user # You must be spark-user, if you are ignore this command.
echo "export JAVA_HOME=/usr/lib/jvm/current-java" >> ~/.bashrc
```
**Attention:** If java will also be used by another users, you can do the same for thesesudo apt users.

- Confirm in all machines that ```$JAVA_HOME``` variable is set correctly:
```bash
source ~/.bashrc # Reload the changed bashrc file
echo $JAVA_HOME
```
You should see ```/usr/lib/jvm/current-java``` in command-line.

## 1.2. Download & Install Hadoop 2.7
> This section is for only **master** machine. We will transfer the files to slaves, later.
- Go to http://apache.claz.org/hadoop/common and find the current 2.7 release. At the time this document was written (June 23, 2020), Hadoop's version 2.7 was in the 7th sub-version.

![SS-2-1](./screenshots/2_download_install_hadoop2.7/1.png)
- Download hadoop using command-line:
```bash
wget http://apache.claz.org/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz
```


## In some point
```bash
chown spark-user:root -R /usr/local/spark-user
chmod g+rwx -R /usr/local/spark-user
```

## References
* https://dzone.com/articles/install-a-hadoop-cluster-on-ubuntu-18041
