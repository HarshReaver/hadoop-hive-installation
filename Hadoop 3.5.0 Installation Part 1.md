# Hadoop 3.5.0 Installation Guide

### Windows 10 + WSL Ubuntu + OpenJDK 21

---

# Introduction

This guide explains how to install Apache Hadoop 3.5.0 on Windows using WSL (Windows Subsystem for Linux). It assumes no prior Linux experience and explains every step performed during the installation.

Environment used:

- Windows 10
- WSL Ubuntu 24.04 LTS
- Hadoop 3.5.0
- OpenJDK 21
- VS Code
- Python 3

---

# Before You Start

## Required Software

| Software            | Purpose                               |
| ------------------- | ------------------------------------- |
| WSL Ubuntu          | Linux environment required for Hadoop |
| OpenJDK 21          | Hadoop runs on Java                   |
| Hadoop 3.5.0 Binary | Hadoop software                       |
| VS Code             | Editing files (optional)              |
| Python              | Later Big Data experiments            |

---

# Step 1 — Install WSL Ubuntu

If Ubuntu is already installed, skip this section.

Open PowerShell as Administrator.

Run

```powershell
wsl --install
```

Restart your computer if prompted.

Install Ubuntu from Microsoft Store.

Open Ubuntu.

Create

- Username
- Password

These will be used whenever Ubuntu asks for administrator permission.

---

# Step 2 — Install Java

## Why?

Hadoop is written in Java.

Without Java, Hadoop cannot run.

Update Ubuntu.

```bash
sudo apt update
sudo apt upgrade -y
```

Install Java.

```bash
sudo apt install openjdk-21-jdk -y
```

Verify.

```bash
java -version
javac -version
```

Expected

```text
openjdk version "21..."
javac 21...
```

---

# Step 3 — Find JAVA_HOME

Hadoop needs to know where Java is installed.

Run

```bash
readlink -f $(which java)
```

Example output

```text
/usr/lib/jvm/java-21-openjdk-amd64/bin/java
```

Remove

```text
/bin/java
```

Therefore

```text
JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
```

Remember this path.

We will use it later.

---

# Step 4 — Download Hadoop

Go to

https://hadoop.apache.org/releases.html

Download

✔ Hadoop 3.5.0 Binary

Do NOT download

✘ Source

Reason

Source code is only for developers who want to compile Hadoop themselves.

Do NOT download

✘ Binary AArch64

Reason

That version is only for ARM processors.

Most Intel and AMD laptops require the normal Binary version.

---

# Step 5 — Copy Hadoop into Ubuntu

Assume the file is inside Windows Downloads.

Copy it into Ubuntu.

```bash
cp /mnt/c/Users/admin/Downloads/hadoop-3.5.0.tar.gz ~/
```

Explanation

cp
: Copy file

/mnt/c
: Windows C Drive inside Linux

~
: Home directory

Check

```bash
ls -lh ~
```

Expected

```text
hadoop-3.5.0.tar.gz
```

---

# Step 6 — Extract Hadoop

Run

```bash
tar -xzf ~/hadoop-3.5.0.tar.gz
```

Explanation

tar
: Archive utility

-x
: Extract

-z
: gzip archive

-f
: File

Verify

```bash
ls
```

Expected

```text
hadoop-3.5.0
```

---

# Step 7 — Configure Environment Variables

## What are Environment Variables?

Instead of searching your computer every time, Linux stores important software locations inside variables.

Example

JAVA_HOME

contains the Java installation.

HADOOP_HOME

contains the Hadoop installation.

---

Open

```bash
nano ~/.bashrc
```

Explanation

nano

Text editor

~

Home folder

.bashrc

Configuration file automatically loaded whenever Ubuntu starts.

---

Scroll

Use the ↓ arrow until the very bottom.

Paste the following BELOW everything already present.

Never paste in the middle.

```bash
# Java
export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin

# Hadoop
export HADOOP_HOME=$HOME/hadoop-3.5.0
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME

export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"

export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```

---

# Saving in Nano

Save

```
Ctrl + O
```

Nano asks

```
File Name to Write
```

Press

```
Enter
```

Exit

```
Ctrl + X
```

---

# Apply Changes

Editing `.bashrc` does NOT immediately change your terminal.

Reload it.

```bash
source ~/.bashrc
```

---

# Verify

Run

```bash
echo $JAVA_HOME
echo $HADOOP_HOME
hadoop version
```

Expected

```text
/usr/lib/jvm/java-21-openjdk-amd64

/home/harsh/hadoop-3.5.0

Hadoop 3.5.0
```

If JAVA_HOME is blank

You probably

- forgot to save
- pasted in the wrong place
- forgot source ~/.bashrc

---

# Step 8 — Create Hadoop Storage Directories

Run

```bash
mkdir -p ~/hadoopdata/hdfs/namenode
mkdir -p ~/hadoopdata/hdfs/datanode
```

Explanation

HDFS needs locations to store

- NameNode metadata
- DataNode blocks

Without these folders Hadoop cannot initialize.

Verify

```bash
ls ~/hadoopdata/hdfs
```

Expected

```text
namenode
datanode
```

---

End of Part 1.
