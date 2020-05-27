# Ambari-Server

The [Apache Ambari](https://ambari.apache.org/) project is aimed at making Hadoop management simpler by developing software for provisioning, managing, and monitoring Apache Hadoop clusters.

Ambari provides an intuitive, easy-to-use Hadoop management web UI backed by its RESTful APIs.

Ambari enables System Administrators to:

+ Provision a Hadoop Cluster
  + Ambari provides a step-by-step wizard for installing Hadoop services across any number of hosts.
  + Ambari handles configuration of Hadoop services for the cluster.

+ Manage a Hadoop Cluster
  + Ambari provides central management for starting, stopping, and reconfiguring Hadoop services across the entire cluster.

+ Monitor a Hadoop Cluster
  + Ambari provides a dashboard for monitoring health and status of the Hadoop cluster.
  + Ambari leverages Ambari Metrics System for metrics collection.
  + Ambari leverages Ambari Alert Framework for system alerting and will notify you when your attention is needed (e.g., a node goes down, remaining disk space is low, etc).

Ambari enables Application Developers and System Integrators to:
+ Easily integrate Hadoop provisioning, management, and monitoring capabilities to their own applications with the Ambari REST APIs.

-----------------------------
## Table-of-Contents

+ 1.  [Install Ambari from *Public* Packages](#1-Install-Ambari-from-Public-Packages)  
  + 1.1 [Create Ambari User](#1.1-Create-Ambari-User)  
  + 1.2 [Install Ambari DB Engine](#1.2-Install-Ambari-DB-Engine)  
    + 1.2.1 [Package Repository](#1.2.1-Package-Repository)  
    + 1.2.2 [Package Installation](#1.2.2-Packages-Installation)  
    + 1.2.3 [Testing](1.2.3-Testing)  

  + 1.3 [Network Configuration File](#1.3-Network-Configuration-File)  
  + 1.4 [IPTables Settings](#1.4-IPTables-Settings)  
  + 1.5 [SELinux and PackageKit Settings](#1.5-SELinux-an-PackageKit-settings)  
  + 1.6 [UMASK Settings](#1.6-UMASK-Settings)  
  + 1.7 [Databases Creation](#1.7-**Databases**-Creation)  



-----------------------------
### 1-Install-Ambari-from-Public-Packages
The last **public verion** of Ambari packages is the **2.7.4.0-118**. The latest Ambari version is the **2.7.5.0.0** and is maintained by [Cloudera](https://docs.cloudera.com/HDPDocuments/Ambari/Ambari-2.7.5.0/index.html). It's a commercial distribution and cannot be using without purchase a paying account.

This procedure is the simplest and preferred installation mode.

#### 1.1-Create-Ambari-User
Create *a user login* dedicated to **Ambari** or choose your one! :
```
sudo groupadd ambari
sudo useradd ambari --home /home/ambari --create-home --groups ambari,sudo --gid ambari --shell /bin/bash
sudo passwd ambari -> <your password>
```
[back](#Table-of-Contents)


-----------------------------

#### 1.2-Install-Ambari-DB-Engine
In the box, Ambari provide a embedded PostgreSQL database instance.
It's possible to use several existing databases:
+ Oracle
+ MySQL/MariaDB
+ PostgreSQL  


In my case, I choose my PostgreSQL instance as Amabri database.

 + ##### 1.2.1-Package-Repository

      ```
      wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
      sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
      sudo apt update
      sudo apt-cache search postgresql-12

      // The ProgreSQL Packages avalaible are depending on your OS System Configuration.
      // On mine computer (Ubuntu 18.04 LTS), the following packages are available (I focus on the principal mines):

      - postgresql-12
        Object-Relational SQL database, version 12 server

      - postgresql-server-dev-12
        Development files for PostgreSQL 12 server-side programming

      - postgresql-doc-12
        Documentation for the PostgreSQL database management system

      - postgresql-client-12
        front-end programs for PostgreSQL 12

      - postgresql-12-pljava
        Java procedural language for PostgreSQL 12

      - postgresql-contrib
        Additional features for PostgreSQL (supported version)
      ```
    [back](#Table-of-Contents)

 + ##### 1.2.2-Packages-Installation
    ```
    sudo apt install postgresql-12 postgresql-server-dev-12 postgresql-doc-12 postgresql-client-12 postgresql-12-pljava postgresql-contrib
    ```
    [back](#Table-of-Contents)
    
 + ##### 1.2.3-Testing
    To verify that your PostgreSQL installation is successfully, perform the following operations:
    ```
    sudo su - postgres
    psql
    ```
    The folowing output must be (*depends on your version*):
    ```
    psql  (12.3 (Ubuntu 12.3-1.pgdg18.04+1))
    Type "help" for help.
    postgres=#
    ```
    [back](#Table-of-Contents)


-----------------------------

#### 1.3-Network-Configuration-File
```
cd /etc/network
touch network
vi network
// Insert the values:
NETWORKING=yes
HOSTNAME=<fully.qualified.domain.name>

// You can get your FQDN (Full Qualified Domain Name) by running the command:
hostname -A
```
[back](#Table-of-Contents)

-----------------------------

#### 1.4-IPTables-Settings
For Ambari to communicate during setup with the hosts it deploys to and manages, certain ports must be open and available. The easiest way to do this is to temporarily disable iptables, as follows:
```
// If "ufw" command not found, install it by running:
// sudo apt install ufw

sudo ufw disable
sudo iptables -X
sudo iptables -t nat -F
sudo iptables -t nat -X
sudo iptables -t mangle -F
sudo iptables -t mangle -X
sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT
```
[back](#Table-of-Contents)

-----------------------------

#### 1.5-SELinux-an-PackageKit-Settings 
You must disable SELinux for the Ambari setup to function. On each host in your cluster, enter:
```
setenforce 0
```

<ins>Note:</ins>
To permanently disable SELinux set **SELINUX=disabled** in ```/etc/selinux/config``` This ensures that SELinux does not turn itself on after you reboot the machine .

[back](#Table-of-Contents)

-----------------------------

#### 1.6-UMASK-Settings
UMASK (User Mask or User file creation MASK) sets the default permissions or base permissions granted when a new file or folder is created on a Linux machine. Most Linux distros set **022** as the default umask value. A umask value of **022** grants read, write, execute permissions of 755 for new files or folders. A umask value of **027** grants read, write, execute permissions of 750 for new files or folders.

Ambari HDP support umask values of **022** (0022 is functionally equivalent), **027** (0027 is functionally equivalent).

These values must be set on all hosts.

Checking your current umask:
```
umask
```

Permanently changing the umask for all interactive users:
```
echo umask 0022 >> /etc/profile
```

Changing the umask for *ambari* user:
```
sudo su - ambari
echo umask 0022 >> .profile
```
[back](#Table-of-Contents)

-----------------------------

#### 1.7-Install Ambari-Packages
The several

List of databases:

| Ambari |Base URL | Repo File | Tarball |
| -------- | -------- | -------- | -------- |
| Base URL | http://public-repo-1.hortonworks.com/ambari/ubuntu18/2.x/updates/2.7.4.0 |[ambari.list](http://public-repo-1.hortonworks.com/ambari/ubuntu18/2.x/updates/2.7.4.0/ambari.list)| [ambari-2.7.4.0-ubuntu18.tar.gz](http://public-repo-1.hortonworks.com/ambari/ubuntu16/2.x/updates/2.7.4.0/ambari-2.7.4.0-ubuntu18.tar.gz) |
