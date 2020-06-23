# HDFS & Yarn & Spark Installation Guide

We will install and configure virtual machines using VirtualBox. In this tutorial it is accepted that:
- You have 3 machines (1 master, 2 slaves).
- You have installed Ubuntu 18.04.4 LTS in your *local* (master) machine and in slaves.
- You have updated and upgraded all packages in all machines.
- You have a valid internet connection for master and slaves.
- There is a user with the same name for all machines, such as ***spark-user***.
- You have passwordless SSH access to slaves from master.
- Hosts (```/etc/hosts```) file is configured for all machines, like shown below:

| Host-Name | IP Address     | Info                      |
|-----------|----------------|---------------------------|
| master    | 192.168.10.107 | Local Ubuntu 18 Machine   |
| slave-1   | 192.168.10.136 | Virtual Ubuntu 18 Machine |
| slave-2   | 192.168.10.134 | Virtual Ubuntu 18 Machine |

**Note:** If one of this criteria is absent, please check the first guide ***Virtual Machine Installation & Configuration Guide***, via the [link](../1-%20Virtual%20Machine%20Installation%20%26%20Configuration/guide.md).

## 1.1. Download Hadoop and VirtualBox
> This section for only **master** machine. We will transfer the files to slaves, later.

## To next section
```bash
chown spark-user:root -R /usr/local/spark-user
chmod g+rwx -R /usr/local/spark-user
```


## References
* https://dzone.com/articles/install-a-hadoop-cluster-on-ubuntu-18041
