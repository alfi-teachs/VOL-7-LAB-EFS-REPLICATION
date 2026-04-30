# VOL-7-LAB-EFS-REPLICATION

#  Step 1: Create EFS File System
Go to AWS Console → EFS (Elastic File System)

Click Create file system

Enter:

Name: EFS

VPC: Default

Keep everything as default (recommended)

Click Create

# Step 2: Configure Security Group for EFS

Here’s the important part—EFS won’t work without NFS access.

Go to EFS → Your File System

Scroll down → Network

Click Security Group

Add Rule:
Type: NFS
Protocol: TCP
Port: 2049

Source: EC2 Security Group (recommended) OR 0.0.0.0/0 (only for testing)

# Step 3: Launch EC2 Instance (Instance 1)

Go to EC2 → Launch Instance

Choose:
AMI: Amazon Linux / Ubuntu

Instance type: t2.micro

VPC: Default

Security Group:

Allow SSH (22)
Allow NFS (2049)

Launch and connect using SSH

# Step 4: Create Directory & Files (Before Mount)
```bash
mkdir efs
```
```bash
ls
```
# Step 5: Mount EFS (Instance 1)

Go to EFS → Attach

Copy Mount via IP command

Example:
```bash
sudo mount -t nfs4 -o nfsvers=4.1 fs-xxxxxx.efs.ap-south-1.amazonaws.com:/ efs
```
👉 Replace efs with your folder name if needed

Verify:
```bash
cd efs
```
# Step 6: Create Files Inside EFS
```bash
touch file{1..12}
```
```bash
ls
```
```bash
file1 file2 file3 file4 file5  file6 file7  file8 file9 file10  file11 file12 
```
👉 Now files are stored in EFS (shared storage)

# Step 7: Launch Second EC2 Instance

Repeat same steps:

Same VPC

Same Security Group (VERY IMPORTANT)

# Step 8: Mount EFS on Second Instance
Connect and run:
```bash
sudo su
```
```bash
mkdir data
```
Now mount using DNS method:
```bash
mount -t nfs4 -o nfsvers=4.1 fs-xxxxxx.efs.ap-south-1.amazonaws.com:/ data
```
# Step 9: Verify Shared Files
```bash
cd data
```
```bash
ls
```
👉 You will see:
```bash
file1 file2 file3 file4 file5  file6 file7  file8 file9 file10  file11 file12 
```
💡 This proves:

Both EC2 instances are using the same EFS storage

# Step 10: EFS Replication (Optional Advanced)

Go to EFS → Replication

Click Create replication

Select:

Destination Region (example: another AWS region)

Type: Regional

Click Create

# Step 11: Mount in Replicated Region (Same Steps)

Launch EC2 in destination region

Configure Security Group:

Allow NFS (2049)

Mount EFS:
```bash
mkdir data
```
```bash
mount -t nfs4 -o nfsvers=4.1 fs-xxxxxx:/ data
```
```bash
cd data
```
```bash
ls
```
```bash
file1 file2 file3 file4 file5  file6 file7  file8 file9 file10  file11 file12 
```
# 🎯 What This Lab Really Means

EFS = shared storage

Multiple EC2 instances can access same files

Changes in one instance appear in others instantly

# ⚠️ Common Mistakes (Avoid These)

❌ Forgetting NFS port 2049

❌ Using different security groups

❌ Mounting wrong directory name

❌ Not using correct DNS/IP from EFS

