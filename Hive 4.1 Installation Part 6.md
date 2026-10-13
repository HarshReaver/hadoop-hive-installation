# Apache Hive 4.1.0 Installation Guide

### Windows 10 + WSL Ubuntu + Hadoop 3.5.0 + OpenJDK 21

---

# Part 6 — Automation Scripts, Daily Workflow, Troubleshooting and Internals

---

# Why We Created Startup Scripts

Starting Hadoop and Hive manually every time is repetitive.

Without scripts, every lab session requires

```
Start SSH

↓

Start HDFS

↓

Start YARN

↓

Wait

↓

Start HiveServer2

↓

Wait

↓

Open Beeline

↓

Connect
```

This takes around 1–2 minutes and is prone to mistakes.

Automation ensures the services always start in the correct order.

---

# Hadoop Startup Script

File

```bash
~/start_hadoop.sh
```

Purpose

- Starts SSH
- Starts HDFS
- Starts YARN
- Checks whether Hadoop is already running
- Displays running services

Run

```bash
~/start_hadoop.sh
```

Expected Output

```
Starting HDFS...

Starting YARN...

Running Services:

NameNode

DataNode

SecondaryNameNode

ResourceManager

NodeManager
```

---

# Hadoop Stop Script

File

```bash
~/stop_hadoop.sh
```

Purpose

Stops

- YARN
- HDFS

Run

```bash
~/stop_hadoop.sh
```

Expected

```
Stopping YARN...

Stopping HDFS...

✓ Hadoop stopped.
```

---

# Hive Startup Script

File

```bash
~/start_hive.sh
```

Purpose

1. Starts Hadoop if required.
2. Starts HiveServer2 in the background.
3. Waits until port 10000 is ready.
4. Opens Beeline automatically.

The script checks whether HiveServer2 has finished loading before attempting the connection.

This prevents

```
Connection refused
```

which occurs when Beeline starts too early.

---

# Hive Stop Script

File

```bash
~/stop_hive.sh
```

Purpose

Stops

- HiveServer2
- Hadoop

Run

```bash
~/stop_hive.sh
```

Expected

```
Stopping Hive...

Stopping Hadoop...

✓ Hadoop stopped.
```

---

# Complete Daily Workflow

Suppose you have just opened Ubuntu.

Run

```bash
~/start_hive.sh
```

The script automatically performs

```
Start SSH

↓

Start HDFS

↓

Start YARN

↓

Start HiveServer2

↓

Wait until Port 10000 is available

↓

Launch Beeline

↓

Connect automatically
```

You immediately receive

```
0: jdbc:hive2://localhost:10000/default>
```

No manual steps are required.

---

# Ending Your Work

When finished

Exit Beeline

```sql
exit;
```

Then stop everything

```bash
~/stop_hive.sh
```

---

# Recommended Folder Structure

```
/home/harsh/

│

├── hadoop-3.5.0/

├── apache-hive-4.1.0-bin/

├── hive-data/

│      ├── logs/

│      └── metastore_db/

├── start_hadoop.sh

├── stop_hadoop.sh

├── start_hive.sh

└── stop_hive.sh
```

Keeping Hadoop and Hive separated makes maintenance much easier.

---

# Complete Startup Sequence

```
Ubuntu

↓

SSH

↓

HDFS

↓

YARN

↓

HiveServer2

↓

Derby Metastore

↓

Port 10000

↓

Beeline

↓

Hive Query
```

---

# What Happens When You Execute a Query?

Suppose you type

```sql
SELECT * FROM student;
```

Internally

```
Beeline

↓

HiveServer2

↓

Hive Parser

↓

Semantic Analyzer

↓

Optimizer

↓

Execution Engine

↓

MapReduce Job

↓

HDFS

↓

Results

↓

Beeline
```

Hive hides all MapReduce complexity from the user.

---

# Understanding the Important Components

## HDFS

Stores data.

---

## YARN

Allocates CPU and memory.

---

## HiveServer2

Accepts SQL queries.

---

## Metastore

Stores metadata.

---

## Derby

Stores the metadata database.

---

## Beeline

Client used by the user.

---

# Common Errors

## Error

```
Connection refused
```

Reason

HiveServer2 is not running.

Check

```bash
ss -ltnp | grep 10000
```

---

## Error

```
Connection terminated
```

Reason

HiveServer2 crashed or was closed.

Restart HiveServer2.

---

## Error

```
Failed to open session
```

Reason

Metastore schema not initialized.

Solution

```bash
schematool -dbType derby -initSchema
```

---

## Error

```
User is not allowed to impersonate
```

Reason

Hive user impersonation.

Solution

```xml
<property>
    <name>hive.server2.enable.doAs</name>
    <value>false</value>
</property>
```

---

## Error

```
Port 10000 already in use
```

Reason

Another HiveServer2 instance is already running.

Check

```bash
ss -ltnp | grep 10000
```

---

# Frequently Used Commands

Check Hadoop

```bash
jps
```

---

Check HDFS

```bash
hdfs dfs -ls /
```

---

Check Hive Port

```bash
ss -ltnp | grep 10000
```

---

Start Hadoop

```bash
~/start_hadoop.sh
```

---

Stop Hadoop

```bash
~/stop_hadoop.sh
```

---

Start Hive

```bash
~/start_hive.sh
```

---

Stop Hive

```bash
~/stop_hive.sh
```

---

Start HiveServer2 manually

```bash
hiveserver2
```

---

Connect manually

```bash
beeline -u jdbc:hive2://localhost:10000/default -n harsh
```

---

Verify Schema

```bash
schematool -dbType derby -info
```

---

Initialize Schema

```bash
schematool -dbType derby -initSchema
```

---

# Curiosity Corner

## Why does Hive need Hadoop?

Hive does not execute queries itself.

It translates SQL into distributed jobs that Hadoop executes.

---

## Can Hive work without HDFS?

Yes.

Modern Hive can use cloud storage such as Amazon S3 or Azure Blob Storage.

However, in this lab it uses HDFS.

---

## Why is Derby not used in companies?

Derby allows only one active writer.

Enterprise systems require multiple concurrent users.

Therefore they use MySQL or PostgreSQL.

---

## Why is Beeline preferred over the old Hive CLI?

The original Hive CLI is deprecated.

Beeline communicates with HiveServer2 using JDBC, making it suitable for local and enterprise deployments.

---

## Why is Hive called a Data Warehouse?

Because it organizes large datasets into structured databases and tables, allowing analysts to query them using SQL rather than writing low-level processing programs.

---

# Summary

You have successfully configured

- OpenJDK 21
- Hadoop 3.5.0
- HDFS
- YARN
- Apache Hive 4.1.0
- Derby Metastore
- HiveServer2
- Beeline
- Automated startup and shutdown scripts

The environment is now ready for Hive experiments such as

- Database creation
- Table creation
- Loading data
- Partitioning
- Bucketing
- HiveQL queries
- MapReduce execution through Hive

---

**End of Apache Hive Installation Guide**
