
# PostgreSQL Database Backup and Restore on Amazon RDS

## Description

This project focuses on enabling **native backup** and **restore** for **PostgreSQL databases** hosted on **Amazon RDS**. It covers the steps to:
- Create and upload **PostgreSQL backups** to **Amazon S3**.
- **Restore PostgreSQL backups** from **Amazon S3** to an **RDS PostgreSQL instance**.
- Take manual **database snapshots** in **Amazon RDS** and restore from them.
- Use **AWS Database Migration Service (DMS)** to migrate data from an **on-prem PostgreSQL** to **Amazon RDS PostgreSQL**.

---

<img src="https://github.com/vrajkmrpatel/psql_backup_AMZ_RDS/blob/main/postgresRDSBackup.png"></img>

---

## Objectives

### 1. Enable Native Backup and Restore on RDS
You need to complete the following:
- Upload a PostgreSQL database backup to an **Amazon S3 bucket**.
- **Restore** the backup from **S3** to your **RDS PostgreSQL** instance.

### 2. Migrate Database from On-Premises to RDS

Migrating a database from an **on-premises environment** to **Amazon RDS PostgreSQL** involves several steps to ensure a smooth transition:

### Steps:
- **Step 1: Select the Database Engine**
  - Choose the appropriate Amazon RDS database engine, such as **PostgreSQL**, based on your on-premises environment.

- **Step 2: Create an RDS Instance**
  - Go to the **AWS Management Console**.
  - Navigate to **RDS** and select **Create Database**.
  - Choose the database engine (PostgreSQL), version, instance class, and other configurations (storage, networking, security groups).

- **Step 3: Enable RDS to Receive Data**
  - Ensure that the target **RDS instance** is configured with a security group that allows connections from your on-premises network.
  - Set up **VPC peering** or **VPN/Direct Connect** to establish secure connectivity between your on-premises environment and AWS.

- **Step 4: Export Database from On-Premises**
  - Export the database from the on-premises environment based on the PostgreSQL engine:
    - Use **`pg_dump`** to export data.

- **Step 5: Use AWS DMS (Optional but Recommended)**
  - **AWS Database Migration Service (DMS)** can help simplify and automate the migration process, including data replication.
  - Configure DMS with the **on-prem database** as the source and the **RDS instance** as the target.

- **Step 6: Import Data into RDS**
  - After exporting, import the data into your RDS PostgreSQL instance using the following:
    - Use **`pg_restore`** command to restore the PostgreSQL database from the exported backup file.

- **Step 7: Test and Verify**
  - After migration, verify the data integrity and application functionality in the new RDS environment.

---

## How to Restore PostgreSQL Database from a Native Backup?

Amazon RDS for PostgreSQL supports **native backups**, allowing you to restore databases from `.bak` files, which are the native backup format for PostgreSQL.

### Steps to Restore PostgreSQL Backup from S3:

1. **Upload the Backup File to Amazon S3**
   - Upload the `.bak` file from your on-premises environment to an **Amazon S3 bucket**.
   - Ensure the S3 bucket is in the same **AWS Region** as the RDS instance.

2. **Grant RDS Permissions to Access the S3 Bucket**
   - Create an **IAM role** that allows Amazon RDS to access the S3 bucket.
   - Attach this IAM role to your PostgreSQL **RDS instance**.

3. **Use the RDS Stored Procedure to Restore**
   - Once the backup file is in S3, use the following **SQL stored procedure** to restore the database from S3:
   ```sql
   exec msdb.dbo.rds_restore_database 
      @restore_db_name='YourDatabaseName', 
      @s3_arn_to_restore_from='arn:aws:s3:::YourBucketName/YourBackupFile.bak';
   ```

4. **Monitor the Restore Process**
   - To monitor the restore progress, use the following command:
   ```sql
   exec msdb.dbo.rds_task_status 
      @db_name='YourDatabaseName';
   ```

5. **Verify the Restoration**
   - Once the restoration completes, verify that the database is restored correctly by connecting to the RDS instance.

---

## How to Take Manual Backup (Snapshots) on RDS?

In Amazon RDS, you can take **manual backups** in the form of **DB snapshots**. These snapshots are stored in **Amazon S3** and can be used to restore your database at any point.

### Steps to Take a Manual Backup (Snapshot):

1. **Go to the Amazon RDS Console**
   - Log in to the **AWS Management Console**.
   - Navigate to **RDS**.

2. **Select the Database Instance**
   - In the RDS Dashboard, select the database instance that you want to back up.

3. **Take a Snapshot**
   - Once the instance is selected, click on the **Actions** dropdown.
   - Select **Take Snapshot**.
   - Provide a name for the snapshot and confirm.

4. **Monitor the Snapshot Creation**
   - After submitting the request, the snapshot will be created. You can monitor its progress in the **Snapshots** section of the RDS console.

5. **Restoring from Manual Backup:**
   - To restore, go to the **Snapshots** section, select the snapshot, and choose **Restore DB Instance**. This creates a new RDS instance based on that snapshot.

---

## References:

- **How to perform native backups of an Amazon RDS DB instance?**
- **Setting Up for Native Backup and Restore**
- **Working With Backups**
- **Running PostgreSQL Databases on Amazon RDS for PostgreSQL - AWS Virtual Workshop**
- **Amazon RDS Free Tier**
  - Costs: Included in the Free Tier
  - **750 hours of Amazon RDS Single-AZ db.t2.micro** included in the free tier.


---

## Output

Once completed, you will have successfully restored your **PostgreSQL database** on **Amazon RDS** using **native backups** and **manual snapshots**. You can now verify the data and perform any necessary tests to ensure the migration is successful.

---

### **Next Steps**

- If you need to **restore specific tables**, you can adjust the `pg_restore` command with `--table` options.
- You can also set up **automated backups** on your RDS PostgreSQL instance using **AWS Backup**.

---

Feel free to **fork** or **clone** this repository for your own use, and **submit a pull request** if you'd like to contribute additional features!

---

### **Commands Used During This Project**

#### **1. Backup Database Using `pg_dump`**:

```bash
pg_dump -U postgres -h localhost -d my_database -F c -f /home/user/my_database.backup
```

#### **2. Transfer Backup File to EC2**:

```bash
scp -i /path/to/your-key.pem /path/to/local/my_database.backup ubuntu@<EC2_public_IP>:/home/ubuntu/
```

#### **3. Restore Backup to RDS Using `pg_restore`**:

```bash
pg_restore -h my-postgresql-db.csvo4kmciapf.us-east-1.rds.amazonaws.com -U postgres -d postgres --no-owner -v /home/ubuntu/my_database.backup
```

#### **4. Restore Data Only, Excluding a Table**:

```bash
pg_restore -h my-postgresql-db.csvo4kmciapf.us-east-1.rds.amazonaws.com -U postgres -d postgres --data-only -v --exclude-table=public.lineitem /home/ubuntu/my_database.backup
```

---
