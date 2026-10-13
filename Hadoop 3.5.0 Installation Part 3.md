# Step 20 — Format the NameNode

## What is Formatting?

Formatting the NameNode creates the initial HDFS filesystem.

Think of it like formatting a brand-new hard disk before storing files.
 
It creates the metadata that Hadoop needs to operate.

---

## IMPORTANT

Run this command **ONLY ONCE** after a fresh installation.

```bash
hdfs namenode -format
```

Do NOT run it every time you start Hadoop.

Running it again deletes the existing HDFS metadata and creates a new filesystem.

If you already have files stored in HDFS, they will become inaccessible.

---

## Expected Output

The output is very long.

The important part is near the end.

```text
Storage directory ... has been successfully formatted
```

followed by

```text
SHUTDOWN_MSG:
```

This means formatting completed successfully.

---

# Step 21 — Start HDFS

Start the Hadoop Distributed File System.

```bash
start-dfs.sh
```

Expected output

```text
Starting namenodes
Starting datanodes
Starting secondary namenodes
```

If you see

```text
NameNode is running...
```

or

```text
DataNode is running...
```

it simply means Hadoop is already running.

It is NOT an error.

---

# Step 22 — Start YARN

Start the Resource Manager and Node Manager.

```bash
start-yarn.sh
```

Expected output

```text
Starting resourcemanager
Starting nodemanagers
```

Again,

```
already running
```

is not an error.

---

# Step 23 — Verify Hadoop

Run

```bash
jps
```

Expected

```text
NameNode
DataNode
SecondaryNameNode
ResourceManager
NodeManager
Jps
```

Meaning

| Process           | Purpose                       |
| ----------------- | ----------------------------- |
| NameNode          | Stores HDFS metadata          |
| DataNode          | Stores actual HDFS data       |
| SecondaryNameNode | Checkpoints NameNode metadata |
| ResourceManager   | Manages cluster resources     |
| NodeManager       | Executes YARN tasks           |
| Jps               | Shows Java processes          |

If one of these is missing

Do NOT continue.

Fix the missing service first.

---

# Step 24 — Open Hadoop Web UI

Open Windows browser.

NameNode

```
http://localhost:9870
```

This shows

- Filesystem health
- DataNodes
- Storage usage
- Live nodes

---

YARN

```
http://localhost:8088
```

This shows

- Running applications
- Cluster resources
- Node status

If both pages load

Congratulations.

Your Hadoop installation is complete.

---

# Step 25 — Test HDFS

Create a directory

```bash
hdfs dfs -mkdir /user
```

Create another

```bash
hdfs dfs -mkdir /user/harsh
```

Create another

```bash
hdfs dfs -mkdir /user/harsh/input
```

Create a local file

```bash
echo "Hello Hadoop" > sample.txt
```

Upload

```bash
hdfs dfs -put sample.txt /user/harsh/input
```

Verify

```bash
hdfs dfs -ls /user/harsh/input
```

Read

```bash
hdfs dfs -cat /user/harsh/input/sample.txt
```

Expected

```text
Hello Hadoop
```

Your HDFS is now working.

---

# Step 26 — Create start_hadoop.sh

Instead of typing multiple commands every day,

create a script.

Go to your home directory.

```bash
cd ~
```

Create file

```bash
nano start_hadoop.sh
```

Paste

```bash
#!/bin/bash

echo "Starting SSH..."
sudo service ssh start

echo "Starting HDFS..."
start-dfs.sh

echo "Starting YARN..."
start-yarn.sh

echo ""
echo "Running Hadoop Processes"
jps
```

Save

```
Ctrl + O

Enter

Ctrl + X
```

Make executable

```bash
chmod +x ~/start_hadoop.sh
```

Run

```bash
~/start_hadoop.sh
```

Explanation

```
#!/bin/bash
```

tells Linux

"This file should be executed using the Bash shell."

Without

```bash
chmod +x
```

Linux treats it as a normal text file.

---

# Step 27 — Create stop_hadoop.sh

Create

```bash
nano stop_hadoop.sh
```

Paste

```bash
#!/bin/bash

echo "Stopping YARN..."
stop-yarn.sh

echo "Stopping HDFS..."
stop-dfs.sh

echo "Done."
```

Save

```
Ctrl + O

Enter

Ctrl + X
```

Make executable

```bash
chmod +x ~/stop_hadoop.sh
```

Run

```bash
~/stop_hadoop.sh
```

---

# Daily Workflow

## When you turn on your computer

Open Ubuntu

Run

```bash
~/start_hadoop.sh
```

Wait until

```bash
jps
```

shows

```text
NameNode
DataNode
SecondaryNameNode
ResourceManager
NodeManager
```

Do your experiments.

When finished

```bash
~/stop_hadoop.sh
```

Close Ubuntu.

---

# Common Linux Commands Used

| Command | Purpose                 |
| ------- | ----------------------- |
| pwd     | Show current directory  |
| ls      | List files              |
| cd      | Change directory        |
| mkdir   | Create folder           |
| cp      | Copy files              |
| mv      | Move/Rename files       |
| rm      | Delete files            |
| nano    | Terminal text editor    |
| cat     | Display file contents   |
| echo    | Print text              |
| chmod   | Change file permissions |
| source  | Reload configuration    |
| find    | Search files            |
| tar     | Extract archives        |
| jps     | Show Java processes     |

---

# Common Mistakes

### Forgot to save Nano

Solution

```
Ctrl + O

Enter

Ctrl + X
```

---

### Forgot

```bash
source ~/.bashrc
```

Symptoms

- JAVA_HOME empty
- Hadoop command not found

Solution

```bash
source ~/.bashrc
```

---

### Put XML outside `<configuration>`

Wrong

```xml
<property>
...
</property>

<configuration>

</configuration>
```

Correct

```xml
<configuration>

<property>
...
</property>

</configuration>
```

---

### Ran

```bash
hdfs namenode -format
```

again

If you already had HDFS data,

you have reset the filesystem metadata.

Only use this command on a fresh installation or when you intentionally want to start over.

---

### Hadoop says

```text
already running
```

This is **not** an error.

It means the service is already active.

---

### `jps` does not show all processes

Restart Hadoop

```bash
stop-yarn.sh
stop-dfs.sh

start-dfs.sh
start-yarn.sh
```

---

# Installation Complete

At this point you have successfully installed:

- OpenJDK 21
- Hadoop 3.5.0
- HDFS
- YARN
- Passwordless SSH
- Hadoop Web UI
- Startup Script
- Shutdown Script
