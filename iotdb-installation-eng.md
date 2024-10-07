# Comprehensive Installation Guide for Apache IoTDB 1.3.2 on Offline Linux Servers

This guide provides step-by-step instructions to install and configure **Apache IoTDB 1.3.2** (both Single-Node and Cluster setups) on clean Linux servers without internet access. All necessary tools and binaries should be downloaded beforehand, transferred via USB, and installed on the target servers.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Required Tools and Binaries](#required-tools-and-binaries)
3. [Preparing the USB Drive](#preparing-the-usb-drive)
4. [Installation Steps](#installation-steps)
   1. [Install Java Runtime Environment (JRE) 8](#1-install-java-runtime-environment-jre-8)
      - [a. Transfer JRE to Target Server](#a-transfer-jre-to-target-server)
      - [b. Install JRE](#b-install-jre)
   2. [Install Additional Tools](#2-install-additional-tools)
      - [a. Transfer Additional Tools to Target Server](#a-transfer-additional-tools-to-target-server)
      - [b. Install Additional Tools](#b-install-additional-tools)
   3. [Install Apache IoTDB](#3-install-apache-iotdb)
      - [a. Transfer IoTDB Package](#a-transfer-iotdb-package)
      - [b. Install IoTDB](#b-install-iotdb)
   4. [Configure and Launch Single-Node IoTDB](#4-configure-and-launch-single-node-iotdb)
      - [a. Single-Node Configuration](#a-single-node-configuration)
      - [b. Launch IoTDB Single-Node](#b-launch-iotdb-single-node)
   5. [Configure and Launch IoTDB Cluster](#5-configure-and-launch-iotdb-cluster)
      - [a. Cluster Configuration](#a-cluster-configuration)
      - [b. Launch IoTDB Cluster](#b-launch-iotdb-cluster)
5. [Post-Installation Verification](#post-installation-verification)
6. [Troubleshooting](#troubleshooting)
7. [Apache IoTDB Official Deployment Guide](#apache-iotdb-official-deployment-guide)

## Prerequisites

- **Linux Servers**: Physical servers with a clean Linux OS installed (e.g., Ubuntu, CentOS).
- **USB Drive**: Sufficient storage capacity to hold all necessary installation files and tools.
- **Access Credentials**: Root or sudo access on all target servers.
- **Network Configuration**: Ensure that servers can communicate with each other over the network for cluster setup.

## Required Tools and Binaries

1. **Java Runtime Environment (JRE) 8**

   - **Version**: JRE 8
   - **Download**: [Oracle JRE 8 Downloads](https://www.oracle.com/java/technologies/downloads/?er=221886#license-lightbox)

2. **Apache IoTDB 1.3.2**

   - **Version**: Apache IoTDB 1.3.2
   - **Download**: [Apache IoTDB 1.3.2 Binary](https://www.apache.org/dyn/closer.cgi/iotdb/1.3.2/apache-iotdb-1.3.2-all-bin.zip)

3. **Additional Tools**

   - **SSH Server and Client**: For cluster communication
   - **Unzip Utility**: To extract compressed files
   - **Text Editor**: Nano

4. **Java Scripts**

   - Any custom Java scripts developed for IoTDB operations.

## Preparing the USB Drive

1. **Create Directory Structure**:

   ```bash
   mkdir -p USB_Install/{jre,iotdb,scripts,tools,additional_tools}
   ```

2. **Download Binaries and Tools**:

   - **JRE 8**: Download the appropriate JRE 8 tarball or installer and place it in `USB_Install/jre/`.
   - **Apache IoTDB 1.3.2**:
     - Download the `apache-iotdb-1.3.2-all-bin.zip` file and place it in `USB_Install/iotdb/`.
   - **Additional Tools**:
     - Download SSH server and client packages, unzip utility, and any other required tools. Place their binaries in `USB_Install/additional_tools/`.
   - **Java Scripts**: Place all custom Java scripts in `USB_Install/scripts/`.

3. **Verify Downloads**: Ensure all files are correctly downloaded and not corrupted.

4. **Safely Eject USB**: Once all files are copied, safely eject the USB drive to prevent data corruption.

## Installation Steps

### 1. Install Java Runtime Environment (JRE) 8

#### a. Transfer JRE to Target Server

1. **Insert USB** into the target Linux server.
2. **Mount USB** (if not auto-mounted):

   ```bash
   sudo mkdir -p /mnt/usb
   sudo mount /dev/sdX1 /mnt/usb  # Replace sdX1 with actual device identifier
   ```

3. **Copy JRE Package**:

   ```bash
   cp /mnt/usb/USB_Install/jre/jre-8uXXX-linux-x64.tar.gz /opt/
   ```

   _Replace `jre-8uXXX-linux-x64.tar.gz` with the actual JRE 8 filename._

#### b. Install JRE

1. **Extract JRE**:

   ```bash
   cd /opt/
   sudo tar -xzf jre-8uXXX-linux-x64.tar.gz
   ```

2. **Set Environment Variables**:

   - Open `/etc/profile` with a text editor:

     ```bash
     sudo nano /etc/profile
     ```

   - Add the following lines at the end:

     ```bash
     export JAVA_HOME=/opt/jre1.8.0_XXX
     export PATH=$PATH:$JAVA_HOME/bin
     ```

     _Replace `jre1.8.0_XXX` with the actual JRE directory name._

   - Save and exit the editor.

3. **Apply Changes**:

   ```bash
   source /etc/profile
   ```

4. **Verify Installation**:

   ```bash
   java -version
   ```

   _Expected Output_: Java version information indicating JRE 8.

### 2. Install Additional Tools

#### a. Transfer Additional Tools to Target Server

1. **Ensure the USB is mounted as described above.**
2. **Copy additional tools packages**:

   ```bash
   cp /mnt/usb/USB_Install/additional_tools/ssh-server.deb /opt/
   cp /mnt/usb/USB_Install/additional_tools/unzip.deb /opt/
   cp /mnt/usb/USB_Install/additional_tools/nano.deb /opt/
   ```

#### b. Install Additional Tools

1. **Install SSH Server and Client**:

   ```bash
   sudo dpkg -i /opt/ssh-server.deb
   ```

2. **Install Unzip Utility**:

   ```bash
   sudo dpkg -i /opt/unzip.deb
   ```

3. **Install Text Editor (Nano)**:

   ```bash
   sudo dpkg -i /opt/nano.deb
   ```

4. **Enable and Start SSH Service**:

   ```bash
   sudo systemctl enable ssh
   sudo systemctl start ssh
   ```

5. **Verify Installation**:

   ```bash
   ssh -V
   unzip -v
   nano --version
   ```

### 3. Install Apache IoTDB

#### a. Transfer IoTDB Package

1. **Copy IoTDB Package**:

   ```bash
   cp /mnt/usb/USB_Install/iotdb/apache-iotdb-1.3.2-all-bin.zip /opt/
   ```

#### b. Install IoTDB

1. **Extract IoTDB**:

   ```bash
   cd /opt/
   sudo unzip apache-iotdb-1.3.2-all-bin.zip
   ```

2. **Set IoTDB Environment Variables** (optional but recommended):

   - Open `/etc/profile`:

     ```bash
     sudo nano /etc/profile
     ```

   - Add:

     ```bash
     export IOTDB_HOME=/opt/apache-iotdb-1.3.2
     export PATH=$PATH:$IOTDB_HOME/bin
     ```

   - Save and exit.

   - Apply changes:

     ```bash
     source /etc/profile
     ```

### 4. Configure and Launch Single-Node IoTDB

#### a. Single-Node Configuration

1. **Navigate to IoTDB Configuration Directory**:

   ```bash
   cd $IOTDB_HOME/conf/
   ```

2. **Edit Common Configuration**:

   ```bash
   sudo nano iotdb-common.properties
   set cluster_name=defaultCluster
   set schema_replication_factor=<number> // for example, 3
   set data_replication_factor=<number> // for example, 2
   exit and save file
   ```

3. **Set Memory Allocation for ConfigNode**:

   ```bash
   sudo nano confignode-env.sh
   set MEMORY_SIZE='your value'
   exit and save file
   ```

4. **Set Memory Allocation for DataNode**:

   ```bash
   sudo nano datanode-env.sh
   set MEMORY_SIZE='your value'
   exit and save file
   ```

5. **Edit Configuration for ConfigNode**:

   ```bash
   sudo nano iotdb-confignode.properties
   set cn_internal_address=<name/ip of machine> // for example one of node-1
   set cn_seed_config_node=<name/ip of machine>:<10710> // for example, node-1:10710
   exit and save file
   ```

6. **Edit Configuration for DataNode**:

   ```bash
   sudo nano iotdb-datanode.properties
   set dn_rpc_address=0.0.0.0
   set dn_internal_address=<name/ip of machine> // for example one of node-1
   set dn_seed_config_node=<name/ip of machine>:<10710> // for example, node-1:10710
   exit and save file
   ```

#### b. Launch IoTDB Single-Node

1. **Start IoTDB**:

   ```bash
   sh $IOTDB_HOME/sbin/start-standalone.sh
   ```

2. **Check IoTDB Requirements Satisfaction**:

   ```bash
   sh $IOTDB_HOME/sbin/health_check.sh -ips <node-1>
   ```

3. **Access IoTDB CLI**:

   ```bash
   sh $IOTDB_HOME/sbin/start-cli.sh
   ```

   _Note_: Use `exit` to quit the CLI.

### 5. Configure and Launch IoTDB Cluster

#### a. Cluster Configuration

1. **Navigate to IoTDB Configuration Directory**:

   ```bash
   cd $IOTDB_HOME/conf/
   ```

2. **Edit Configuration for Cluster**":

   ```bash
   sudo nano iotdb-cluster.properties
   set confignode_address_list=<list of config nodes> // for example, node-1, node-2, node-3
   set datanode_address_list=<list of data nodes> // for example, node-1, node-2, node-3
   set confignode_deploy_path=<path/to/iotdb/data> // for example, /opt/iotdb/data
   set datanode_deploy_path=<path/to/iotdb/data> // for example, /opt/iotdb/data
   exit and save file
   ```

3. **Edit Common Configuration**:

   ```bash
   sudo nano iotdb-common.properties
   set cluster_name=defaultCluster
   set schema_replication_factor=<number> // for example, 3
   set data_replication_factor=<number> // for example, 2
   exit and save file
   ```

4. **Set Memory Allocation for ConfigNode**:

   ```bash
   sudo nano confignode-env.sh
   set MEMORY_SIZE='your value'
   exit and save file
   ```

5. **Set Memory Allocation for DataNode**:

   ```bash
   sudo nano datanode-env.sh
   set MEMORY_SIZE='your value'
   exit and save file
   ```

6. **Edit Configuration for ConfigNode**:

   ```bash
   sudo nano iotdb-confignode.properties
   set cn_internal_address=<name/ip of machine> // for example one of node-1 | node-2 | node3
   set cn_seed_config_node=<name/ip of main machine>:<10710> // for example, node-1:10710 (should be same on all nodes)
   exit and save file
   ```

7. **Edit Configuration for DataNode**:

   ```bash
   sudo nano iotdb-datanode.properties
   set dn_rpc_address=0.0.0.0
   set dn_internal_address=<name/ip of machine> // for example one of node-1 | node-2 | node3
   set dn_seed_config_node=<name/ip of main machine>:<10710> // for example, node-1:10710 (should be same on all nodes)
   exit and save file
   ```

8. **Configure Hosts File** (on each server):

   - Open `/etc/hosts`:

     ```bash
     sudo nano /etc/hosts
     ```

   - Add entries for all cluster nodes:

     ```plaintext
     192.168.1.1 node-1
     192.168.1.2 node-2
     192.168.1.3 node-3
     ```

   - Save and exit.

9. **Distribute Configuration**:

   - Ensure that the same configuration is present on all cluster nodes.
   - Use the USB to transfer any unique configuration files if necessary.

#### b. Launch IoTDB Cluster

1. **Start Config Nodes**:

   Start the main confignode first, ensuring that the seed confignode node starts first, and then start the second and third confignode nodes in sequence.

   On each Config Node server:

   ```bash
   ./$IOTDB_CLUSTER_HOME/sbin/start-confignode.sh
   ```

2. **Start Data Nodes**:

   On each Data Node server:

   ```bash
   ./$IOTDB_CLUSTER_HOME/sbin/start-datanode.sh
   ```

3. **Check IoTDB Requirements Satisfaction**:

   ```bash
   sh $IOTDB_HOME/sbin/health_check.sh -ips <node-1>
   ```

4. **Access IoTDB CLI**:

   ```bash
   sh $IOTDB_HOME/sbin/start-cli.sh
   ```

   _Note_: Use `exit` to quit the CLI.

## Post-Installation Verification

1. **Check Service Status**:

   - Ensure all IoTDB services are active.

     ```bash
     ps -aux | grep java
     ```

2. **Test Data Ingestion and Querying**:

   - Use IoTDB CLI to create a test database, insert data, and perform queries.

     ```sql
     CREATE DATABASE root.test;
     INSERT INTO root.test.d1(timestamp, value) VALUES(1, 100);
     SELECT * FROM root.test.d1;
     ```

3. **Review Logs**:

   - Check IoTDB logs for any errors or warnings.

     ```bash
     tail -f $IOTDB_HOME/logs/<needed-log-file>.log
     ```

## Troubleshooting

- **Java Not Found**:

  - Ensure `JAVA_HOME` is correctly set and exported.
  - Verify Java installation path.

- **IoTDB Service Fails to Start**:

  - Check log files for error messages.
  - Verify that required ports are open and not in use.
  - Ensure proper configurations in `conf` folder.

- **Cluster Nodes Not Communicating**:

  - Verify network connectivity between servers.
  - Check `/etc/hosts` entries for accuracy.
  - Ensure consistent configuration across all nodes.

- **Permission Issues**:
  - Ensure that IoTDB directories have appropriate read/write permissions.
  - Run startup scripts with sufficient privileges.

## Apache IoTDB Official Deployment Guide

- [Stand-Alone Deployment](https://iotdb.apache.org/UserGuide/latest/Deployment-and-Maintenance/Stand-Alone-Deployment_apache.html)
- [Cluster Deployment](https://iotdb.apache.org/UserGuide/latest/Deployment-and-Maintenance/Cluster-Deployment_apache.html)
