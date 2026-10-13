# Apache Hive 4.1.0 Installation Guide

### Windows 10 + WSL Ubuntu + Hadoop 3.5.0 + OpenJDK 21

---

# Part 5 — Initializing the Metastore and Starting Hive

---

# Why Hive Needs a Metastore

Hive does not store information about databases and tables inside HDFS.

Instead, it stores

- database names
- table names
- column names
- partitions
- table locations
- file formats

inside a **Metastore Database**.

The actual table data always remains inside HDFS.

Think of it like this:

```
HDFS
│
├── stores actual data

Metastore
│
├── stores information ABOUT the data
```

---

# What is Derby?

For local installations Hive uses an embedded database called **Apache Derby**.

Advantages

- No installation required
- Lightweight
- Good for learning
- Works inside a single computer

Disadvantages

- Only one process can access it at a time.
- Not suitable for production clusters.

Large companies replace Derby with

- MySQL
- PostgreSQL

---

# Creating the Metastore Database

Earlier we configured

```xml
jdbc:derby:;databaseName=/home/harsh/hive-data/metastore_db;create=true
```

Notice

```
create=true
```

This tells Derby

> If the database doesn't exist, create it.

However,

the database still has **no tables**.

We must initialize the schema.

---

# Initializing Hive Schema

Move into the configuration directory

```bash
cd ~/apache-hive-4.1.0-bin/conf
```

Run

```bash
schematool -dbType derby -initSchema
```

Explanation

```
schematool
```

Hive utility for managing the metastore.

```
-dbType derby
```

Specifies Derby as the backend.

```
-initSchema
```

Creates every table required by Hive.

---

# Expected Output

```
Initializing the schema to: 4.1.0

Initialization script completed
```

This only needs to be done **once**.

---

# Verifying the Schema

Run

```bash
schematool -dbType derby -info
```

Expected

```
Hive distribution version : 4.1.0

Metastore schema version : 4.1.0
```

If both versions match,

your metastore is correctly configured.

---

# Why This Step Is Important

Without schema initialization

HiveServer2 starts,

but cannot create sessions.

Symptoms include

```
Connection refused

Failed to open session

Could not connect to HiveServer2
```

because Hive cannot read metadata.

---

# Creating Warehouse Directory in HDFS

Hive stores table data here

```
/user/hive/warehouse
```

Create it

```bash
hdfs dfs -mkdir -p /user/hive/warehouse
```

Give permissions

```bash
hdfs dfs -chmod -R 777 /user/hive
```

Verify

```bash
hdfs dfs -ls /user/hive
```

Expected

```
warehouse
```

---

# Starting Hadoop

Hive requires Hadoop.

Start Hadoop

```bash
~/start_hadoop.sh
```

Verify

```bash
jps
```

Expected

```
NameNode
DataNode
SecondaryNameNode
ResourceManager
NodeManager
```

---

# Starting HiveServer2

Now launch HiveServer2

```bash
hiveserver2
```

What happens internally?

```
Reads hive-site.xml

↓

Connects to Derby

↓

Loads Metastore

↓

Starts Thrift Server

↓

Listens on Port 10000
```

---

# Why We Keep This Terminal Open

HiveServer2 is a server process.

Closing the terminal stops it.

Therefore

```
Terminal 1

↓

Run hiveserver2

↓

Leave it running
```

Open another terminal for Beeline.

---

# Verifying HiveServer2

Open another terminal.

Run

```bash
ss -ltnp | grep 10000
```

Expected

```
LISTEN
*:10000
```

Meaning

HiveServer2 is accepting client connections.

---

Verify Java Process

```bash
pgrep -af HiveServer2
```

Expected

```
java ... HiveServer2
```

---

Verify using JPS

```bash
jps -l
```

Expected

```
RunJar
```

HiveServer2 runs inside Hadoop's RunJar launcher.

---

# Connecting Through Beeline

Open another terminal.

Run

```bash
beeline -u jdbc:hive2://localhost:10000/default -n harsh
```

Expected

```
Connected to:
Apache Hive 4.1.0
```

Prompt

```
0: jdbc:hive2://localhost:10000/default>
```

You are now inside Hive.

---

# Testing Hive

Run

```sql
SHOW DATABASES;
```

Expected

```
default
```

---

Create a database

```sql
CREATE DATABASE college;
```

Verify

```sql
SHOW DATABASES;
```

Output

```
default

college
```

---

Use the database

```sql
USE college;
```

---

Create a table

```sql
CREATE TABLE student
(
id INT,
name STRING,
marks INT
);
```

Verify

```sql
SHOW TABLES;
```

Output

```
student
```

---

Describe the table

```sql
DESCRIBE student;
```

Expected

```
id

name

marks
```

---

Exit Hive

```sql
exit;
```

or

```sql
!quit
```

---

# Understanding the Complete Startup Flow

```
Start Hadoop

↓

Verify HDFS

↓

Start HiveServer2

↓

Hive loads Derby

↓

Hive loads Metastore

↓

Hive opens Port 10000

↓

Beeline connects

↓

Execute HiveQL

↓

Hive converts SQL into MapReduce jobs

↓

Results returned
```

---

# Common Mistakes

## Mistake

Running Beeline before HiveServer2.

Result

```
Connection refused
```

---

## Mistake

Forgetting to initialize schema.

Result

```
Could not open session
```

---

## Mistake

Closing the HiveServer2 terminal.

Result

```
Connection terminated
```

---

## Mistake

Not starting Hadoop first.

Result

Hive cannot access HDFS.

---

# Quick Verification Checklist

Before opening Beeline, verify

```bash
jps
```

Contains

```
NameNode

DataNode

SecondaryNameNode

ResourceManager

NodeManager

RunJar
```

---

Check Port

```bash
ss -ltnp | grep 10000
```

Must show

```
LISTEN
```

---

Finally

```bash
beeline -u jdbc:hive2://localhost:10000/default -n harsh
```

If successful

```
Connected to:
Apache Hive 4.1.0
```

---

End of Part 5.
