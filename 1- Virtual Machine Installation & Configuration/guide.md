# Virtual Machine Installation & Configuration Guide

## 1. Install and Configure Virtual Machines using Virtualbox

In this tutorial it is accepted that:
- You have already installed Ubuntu 18.04.4 LTS in your *local* computer.
- You have updated and upgraded all packages.
- You have a valid internet connection.

### 1.1. Install VirtualBox from Command Line:
```bash
sudo apt install virtualbox
```
### 1.2. Get 18.04.4 LTS Server Install Image:
You can download iso file from the link: [Official Ubuntu 18.04.4 LTS Live Server ISO](http://releases.ubuntu.com/bionic/ubuntu-18.04.4-live-server-amd64.iso)

***OR***

You can download iso file from command line:
```bash
cd ~/Downloads
wget http://releases.ubuntu.com/bionic/ubuntu-18.04.4-live-server-amd64.iso
```

## 1.3. Create First Slave Machine
- Firstly, start VirtualBox using command below:
```bash 
virtualbox &
```
- Click "New" button.
- Enter _Name_ as "slave-1", _Type_ as "Linux", _Version_ as "Ubuntu (64-bit)".

![VM-1-1](screenshots/create_first_slave_machine/1.png)

- Select the amount of memory (RAM). We select _2048 MB_ (2 GB), arbitrarily.  You can change it up to your needs.

![VM-1-2](screenshots/create_first_slave_machine/2.png)

- Create a _virtual hard disk_ for convenience.

![VM-1-3](screenshots/create_first_slave_machine/3.png)

- Choose _VDI (VirtualBox Disk Image)_.

![VM-1-4](screenshots/create_first_slave_machine/4.png)

- _Dynamically allocated_ has its own advantages, so we will choose it. But _fixed-size_ option would also be selected if we were sure that disk size don't need to change in the future.

![VM-1-5](screenshots/create_first_slave_machine/5.png)

- We will enter the name of the new virtual hard disk file as "slave-1". This name is arbitrary, you can change it. Size of the virtual hard disk is **very important**. You should set it at least **20 GB**, we will set it as _50 GB_ to be safe.

![VM-1-6](screenshots/create_first_slave_machine/6.png)

- Choose the Ubuntu ISO, you downloaded before.

![VM-1-7](screenshots/create_first_slave_machine/7.png)
![VM-1-8](screenshots/create_first_slave_machine/8.png)
![VM-1-9](screenshots/create_first_slave_machine/9.png)

- Pass the initial parts and select the options. When you are in  _Profile setup_ part. We will set "Your Name" as _spark-user_, "Your server's name" as _slave-1_, "Pick a username" as _spark-user_. Names can change but user-names should be the same among all machines to be safe in future ssh connections.

![VM-1-10](screenshots/create_first_slave_machine/10.png)

- Select "Install OpenSSH Server".

![VM-1-11](screenshots/create_first_slave_machine/11.png)

- No need to install any packages listed here. We can install them after the installation if needed.

![VM-1-12](screenshots/create_first_slave_machine/12.png)

- Reboot after installation and you are done!

## 1.4. Create Second Slave Machine

You don't have to do all the things again. Simply, clone the first virtual machine.

- Shut machine "slave-1" down.
- Click right click on "slave-1" and select "clone" option. Rename the machine to "slave-2".

![VM-2-1](screenshots/create_second_slave_machine/1.png)

- Choose _full clone_ option and hit _Clone_.

![VM-2-2](screenshots/create_second_slave_machine/2.png)

- Our second virtual machine is ready, but the hostname of the machine is still "slave-1". In order to fix this, start slave-2 and login with credentials you defined in the installation of slave-1.

![VM-2-3](screenshots/create_second_slave_machine/3.png)

- After login enter the command below:
```bash
sudo hostnamectl set-hostname slave-2
exit
```

- If you see a screen like this, then everything is fine:

![VM-2-4](screenshots/create_second_slave_machine/4.png)

## 1.5. Update both machines

- Login to both machines and enter the command:
```bash
sudo apt update && sudo apt upgrade
```


## In VirtualBox
Use bridged network and check the ip for being started with "192.168"