# hadoop-hive-installation

Documentation and automation for installing and configuring Apache Hadoop and Apache Hive.

This repository contains step-by-step installation guides for:

- Hadoop 3.5.0 on Windows 10 + WSL Ubuntu
- Apache Hive 4.1.0 on top of Hadoop
- Metastore initialization and startup workflow
- Daily operating scripts and troubleshooting notes

## Included guides

- [Hadoop 3.5.0 Installation Part 1.md](./Hadoop%203.5.0%20Installation%20Part%201.md)
- [Hadoop 3.5.0 Installation Part 2.md](./Hadoop%203.5.0%20Installation%20Part%202.md)
- [Hadoop 3.5.0 Installation Part 3.md](./Hadoop%203.5.0%20Installation%20Part%203.md)
- [Hive 4.1 Installation Part 4.md](./Hive%204.1%20Installation%20Part%204.md)
- [Hive 4.1 Installation Part 5.md](./Hive%204.1%20Installation%20Part%205.md)
- [Hive 4.1 Installation Part 6.md](./Hive%204.1%20Installation%20Part%206.md)

## Environment

These guides are based on:

- Windows 10
- WSL Ubuntu 24.04 LTS
- OpenJDK 21
- Hadoop 3.5.0
- Apache Hive 4.1.0

## Typical workflow

1. Install WSL and Java
2. Download and extract Hadoop
3. Configure environment variables and XML files
4. Start SSH and initialize HDFS
5. Install Hive and configure metastore
6. Initialize schema and start HiveServer2
7. Connect with Beeline and run Hive queries

## Notes

These documents are intended for learning and local single-node installation setups. Production clusters usually use additional configuration, high availability, and external metastore systems such as MySQL or PostgreSQL.
