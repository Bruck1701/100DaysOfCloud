
## EBS vs EFS vs Instance Store 

### EBS Volume

• It’s a network drive (i.e. not a physical drive)
• It uses the network to communicate the instance, which means there might be a bit of latency
• It can be detached from an EC2 instance and attached to another one quickly
• It’s locked to an Availability Zone (AZ)
• An EBS Volume in us-east-1a cannot be attached to us-east-1b
• To move a volume across, you first need to snapshot it
• Have a provisioned capacity (size in GBs, and IOPS)
• You get billed for all the provisioned capacity
• You can increase the capacity of the drive over time

EBS Volume Types

• EBS Volumes come in 4 types
• GP2 (SSD): General purpose SSD volume that balances price and performance for a wide variety of workloads
• IO1 (SSD): Highest-performance SSD volume for mission-critical low-latency or highthroughput workloads
• ST1 (HDD): Low cost HDD volume designed for frequently accessed, throughput intensive workloads
• SC1 (HDD): Lowest cost HDD volume designed for less frequently accessed workloads
• EBS Volumes are characterized in Size | Throughput | IOPS (I/O Ops Per Sec)
• Only GP2 and IO1 can be used as boot volumes

### Instance Store

Instance store is physically attached to the machine (EBS is a network drive)

• Pros:
    • Better I/O performance (EBS gp2 has an max IOPS of 16000, io1 of 64000)
    • Good for buffer / cache / scratch data / temporary content
    • Data survives reboots

• Cons:
    • On stop or termination, the instance store is lost
    • You can’t resize the instance store
    • Backups must be operated by the user

Very High IOPS (because physical)
• Disks up to 7.5 TiB, stripped to reach 30 TiB (currently)
• Block Storage (just like EBS)
• Cannot be increased in size
• Risk of data loss if hardware fails

### EFS

Managed NFS (network file system) that can be mounted on many EC2 instances.EFS works with EC2 instances in multi-AZ and is Highly available, scalable, but a bit expensive (3x gp2), but pay per use!

• Use cases: content management, web serving, data sharing, Wordpress
• Uses NFSv4.1 protocol
• Uses security group to control access to EFS
• Compatible with Linux based AMI (not Windows)
• Encryption at rest using KMS
• POSIX file system (~Linux) that has a standard file API
• File system scales automatically, pay-per-use, no capacity planning!

EFS Scale:
    • 1000s of concurrent NFS clients, 10 GB+ /s throughput
    • Grow to Petabyte-scale network file system, automatically
    • Performance mode (set at EFS creation time)
    • General purpose (default): latency-sensitive use cases (web server, CMS, etc…)
    • Max I/O – higher latency, throughput, highly parallel (big data, media processing)
    • Storage Tiers (lifecycle management feature – move file after N days)
    • Standard: for frequently accessed files
    • Infrequent access (EFS-IA): cost to retrieve files, lower price to store

