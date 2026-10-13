# Apache Hive 4.1.0 Installation Guide

### Windows 10 + WSL Ubuntu + Hadoop 3.5.0 + OpenJDK 21

---

# Part 4 — Hive Installation & Configuration

---

# Introduction

This guide explains how to install Apache Hive 4.1.0 on top of an already working Hadoop 3.5.0 installation.

Unlike Hadoop, Hive is **not** a storage system.

Hive is a **Data Warehouse** built on top of Hadoop.

It allows users to write SQL-like queries instead of Java MapReduce programs.

Instead of writing hundreds of lines of Java code, Hive converts SQL queries into MapReduce, Tez or Spark jobs automatically.

---

# Prerequisites

Before installing Hive, ensure Hadoop is already working.

Verify the following.

```bash
jps
```

Expected output

```text
NameNode
DataNode
SecondaryNameNode
ResourceManager
NodeManager
```

---

Check HDFS.

```bash
hdfs dfs -ls /
```

Expected output

```text
/tmp
/user
```

If Hadoop is not working, complete the Hadoop Installation Guide first.

---

# What is Apache Hive?

Imagine a company stores 10 TB of employee data.

Without Hive:

You must

- Write Java
- Create Mapper
- Create Reducer
- Compile
- Package JAR
- Submit Job
- Wait for output

With Hive

Simply write

```sql
SELECT * FROM employee;
```

Hive converts the SQL query into jobs that Hadoop understands.

Therefore

```
SQL
        ↓
Hive
        ↓
MapReduce / Tez / Spark
        ↓
HDFS
```

---

# Why Do We Need Hive?

Suppose a manager asks

> "Show all employees earning more than ₹50,000."

Without Hive

You must write Java MapReduce.

With Hive

```sql
SELECT *
FROM employee
WHERE salary > 50000;
```

Much simpler.

---

# Hive Architecture

```
                User

                  │

             HiveQL Query

                  │

              Beeline CLI

                  │

             HiveServer2

                  │

        Compiler + Optimizer

                  │

      MapReduce / Tez / Spark

                  │

                HDFS
```

---

# Main Hive Components

## HiveServer2

Purpose

Accepts SQL queries from users.

Without HiveServer2

Beeline cannot connect.

Default Port

```
10000
```

---

## Beeline

Command-line client.

Acts like

```
MySQL Client

or

psql

or

SQL*Plus
```

It sends SQL statements to HiveServer2.

---

## Metastore

Stores

- Databases
- Tables
- Partitions
- Schema
- Metadata

It does NOT store actual data.

Actual data remains inside HDFS.

---

## Derby Database

For learning purposes

Hive stores metadata inside Apache Derby.

Advantages

- Simple
- Embedded
- No configuration

Disadvantages

- Single user only

Large companies replace Derby with

- MySQL
- PostgreSQL

---

# Download Hive

Visit

https://hive.apache.org/downloads.html

Download

```
Apache Hive 4.1.0 Binary
```

Copy into Ubuntu

Example

```bash
cp /mnt/c/Users/admin/Downloads/apache-hive-4.1.0-bin.tar.gz ~/
```

Verify

```bash
ls
```

Expected

```
apache-hive-4.1.0-bin.tar.gz
```

---

# Extract Hive

Run

```bash
tar -xzf apache-hive-4.1.0-bin.tar.gz
```

Explanation

```
tar
```

Archive utility.

```
-x
```

Extract.

```
-z
```

gzip archive.

```
-f
```

File.

Verify

```bash
ls
```

Expected

```
apache-hive-4.1.0-bin
```

---

# Hive Directory Structure

Enter

```bash
cd ~/apache-hive-4.1.0-bin
```

View

```bash
ls
```

Important folders

```
bin/
```

Contains Hive commands.

```
conf/
```

Configuration files.

```
lib/
```

All Hive libraries.

```
examples/
```

Example programs.

---

# Configure Environment Variables

Open

```bash
nano ~/.bashrc
```

Add

```bash
# Hive

export HIVE_HOME=$HOME/apache-hive-4.1.0-bin
export PATH=$PATH:$HIVE_HOME/bin
```

Save

```
Ctrl + O

Enter

Ctrl + X
```

Reload

```bash
source ~/.bashrc
```

Verify

```bash
echo $HIVE_HOME
```

Expected

```
/home/harsh/apache-hive-4.1.0-bin
```

---

# Verify Hive Installation

Run

```bash
hive --version
```

Expected

```
Hive 4.1.0
```

---

# Create Hive Working Directory

Unlike Hadoop,

Hive requires its own working directories.

Create

```bash
mkdir -p ~/hive-data
mkdir -p ~/hive-data/logs
```

Purpose

```
hive-data/
```

Stores Hive-specific files.

```
logs/
```

Stores future log files.

Keeping Hive separate from Hadoop makes troubleshooting easier.

---

# Configure hive-env.sh

Move

```bash
cd ~/apache-hive-4.1.0-bin/conf
```

Create configuration

```bash
cp hive-env.sh.template hive-env.sh
```

Open

```bash
nano hive-env.sh
```

Append

```bash
export HADOOP_HOME=$HOME/hadoop-3.5.0
export HIVE_CONF_DIR=$HOME/apache-hive-4.1.0-bin/conf
```

Explanation

```
HADOOP_HOME
```

Allows Hive to locate Hadoop binaries.

```
HIVE_CONF_DIR
```

Tells Hive where configuration files are stored.

---

# Configure hive-site.xml

If missing, create

```bash
nano hive-site.xml
```

Insert

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:derby:;databaseName=/home/harsh/hive-data/metastore_db;create=true</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>org.apache.derby.jdbc.EmbeddedDriver</value>
    </property>

    <property>
        <name>datanucleus.schema.autoCreateAll</name>
        <value>true</value>
    </property>

    <property>
        <name>hive.server2.enable.doAs</name>
        <value>false</value>
    </property>

</configuration>
```

---

# Understanding Each Property

## hive.metastore.warehouse.dir

Specifies where Hive tables are stored in HDFS.

```
/user/hive/warehouse
```

---

## ConnectionURL

Specifies the Derby database location.

```
jdbc:derby:
```

Embedded Derby.

```
databaseName=/home/harsh/hive-data/metastore_db
```

Stores metadata.

```
create=true
```

Automatically creates the database if absent.

---

## ConnectionDriverName

Specifies the JDBC driver used to communicate with Derby.

---

## datanucleus.schema.autoCreateAll

Automatically creates required metadata tables.

---

## hive.server2.enable.doAs

```
false
```

Disables user impersonation.

Recommended for single-user local installations because it avoids unnecessary permission issues.

---

# Verify Configuration

Run

```bash
cat hive-site.xml
```

Check for

- XML syntax errors
- Missing closing tags
- Correct Derby path
- Correct warehouse directory

Configuration is now complete.

---

End of Part 4.
