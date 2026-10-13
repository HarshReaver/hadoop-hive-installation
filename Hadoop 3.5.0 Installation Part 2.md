# Step 9 — Configure Hadoop

Go to the Hadoop configuration folder.

```bash
cd ~/hadoop-3.5.0/etc/hadoop
```

Verify

```bash
ls
```

You should see files similar to

```text
core-site.xml
hdfs-site.xml
mapred-site.xml
yarn-site.xml
workers
hadoop-env.sh
```

These are the main configuration files.

Each file controls one part of Hadoop.

---

# Step 10 — Configure hadoop-env.sh

## Why?

Hadoop needs to know where Java is installed.

Open

```bash
nano hadoop-env.sh
```

Search for

```bash
export JAVA_HOME=
```

or

```bash
# export JAVA_HOME=
```

Replace that line with

```bash
export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
```

Do NOT add another JAVA_HOME.

Replace the existing one.

Save

```
Ctrl + O

Enter

Ctrl + X
```

---

# Step 11 — Configure core-site.xml

## Purpose

Defines the default Hadoop filesystem.

Open

```bash
nano core-site.xml
```

You will see something like

```xml
<configuration>

</configuration>
```

Do NOT delete

```xml
<configuration>
```

or

```xml
</configuration>
```

Paste the property INSIDE these tags.

Correct

```xml
<configuration>

    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>

</configuration>
```

Wrong

```xml
<property>
...
</property>

<configuration>

</configuration>
```

The property must always be inside the configuration tags.

Explanation

```
fs.defaultFS
```

tells Hadoop

"My default filesystem is HDFS running on localhost at port 9000."

Save

Ctrl + O

Enter

Ctrl + X

---

# Step 12 — Configure hdfs-site.xml

## Purpose

Defines where Hadoop stores data.

Open

```bash
nano hdfs-site.xml
```

Again

Keep

```xml
<configuration>

</configuration>
```

Paste INSIDE

```xml
<property>
    <name>dfs.replication</name>
    <value>1</value>
</property>

<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///home/harsh/hadoopdata/hdfs/namenode</value>
</property>

<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///home/harsh/hadoopdata/hdfs/datanode</value>
</property>
```

Explanation

```
dfs.replication
```

Number of copies of every block.

For one computer

```
1
```

is correct.

---

```
dfs.namenode.name.dir
```

Folder storing NameNode metadata.

---

```
dfs.datanode.data.dir
```

Folder storing DataNode blocks.

Save

Ctrl + O

Enter

Ctrl + X

---

# Step 13 — Configure mapred-site.xml

## Purpose

Enable MapReduce.

Open

```bash
nano mapred-site.xml
```

Paste INSIDE

```xml
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
```

Explanation

Without this

MapReduce will not use YARN.

Save

Ctrl + O

Enter

Ctrl + X

---

# Step 14 — Configure yarn-site.xml

## Purpose

Configure YARN.

Open

```bash
nano yarn-site.xml
```

Paste inside

```xml
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>

<property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
```

Explanation

Shuffle transfers intermediate MapReduce output from mappers to reducers.

Without it

MapReduce jobs fail.

Save

Ctrl + O

Enter

Ctrl + X

---

# Step 15 — Configure workers

Purpose

Lists worker machines.

Open

```bash
nano workers
```

Delete everything.

Keep only

```text
localhost
```

Explanation

Our Hadoop cluster has only one machine.

That machine is

```
localhost
```

Save

Ctrl + O

Enter

Ctrl + X

---

# Step 16 — Verify Configuration

Run

```bash
echo $JAVA_HOME

echo $HADOOP_HOME

hadoop version
```

If all three work

Your configuration is correct.

---

# Step 17 — Install SSH

## Why?

Even on one computer

Hadoop launches services using SSH.

Install

```bash
sudo apt install openssh-server -y
```

Start SSH

```bash
sudo service ssh start
```

Verify

```bash
sudo service ssh status
```

Expected

```text
active (running)
```

---

# Step 18 — Generate SSH Keys

Generate key

```bash
ssh-keygen -t rsa -P ""
```

When asked

```
Enter file in which to save key
```

Simply press

```
Enter
```

When asked

```
Passphrase
```

Press

```
Enter
```

again.

When asked

```
Confirm passphrase
```

Press

```
Enter
```

again.

Expected

```text
Your identification has been saved...
Your public key has been saved...
```

---

# Step 19 — Enable Passwordless Login

Run

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Change permissions

```bash
chmod 600 ~/.ssh/authorized_keys
```

Test

```bash
ssh localhost
```

The first time

Ubuntu asks

```text
Are you sure you want to continue connecting?
```

Type

```text
yes
```

Press Enter.

You should enter another Ubuntu shell without entering your password.

Exit

```bash
exit
```

If SSH asks for your password every time

Repeat Step 19.

---

End of Part 2.
