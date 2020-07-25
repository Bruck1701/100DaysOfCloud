## Amazon Aurora

• Aurora is AWS proprietary technology
• Postgres and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgres or MySQL database)
• Aurora is “AWS cloud optimized” and claims 5x performance improvement over MySQL on RDS, over 3x the performance of Postgres on RDS ( but at a higher cost (20% more) NO Free tier for Aurora)
• Aurora storage automatically grows in increments of 10GB, up to 64 TB.
• Aurora can have 15 replicas while MySQL has 5, and the replication process is faster (sub 10 ms replica lag)
• Failover in Aurora is instantaneous. Key word: High Availability

Features of Aurora

• Automatic fail-over
• Backup and Recovery
• Isolation and security
• Industry compliance
• Push-button scaling
• Automated Patching with Zero Downtime
• Advanced Monitoring
• Routine Maintenance
• Backtrack: restore data at any point of time without using backups
• Encryption at rest using KMS
• Automated backups, snapshots and replicas are also encrypted
• Encryption in flight using SSL (same process as MySQL or Postgres)
• Possibility to authenticate using IAM token (same method as RDS)

## Share reponsability security model:

• You are responsible for protecting the instance with security groups
• You can’t SSH ( OS and DB maintenance is on AWS)


## High Availability on Aurora 
6 copies of the data across 3 AZ:
    • 4 copies out of 6 needed for writes
    • 3 copies out of 6 needed for reads
    
• Self healing with peer-to-peer replication
• Storage is striped across hundreds of volumes
• One Aurora Instance takes writes (master)

• Automated failover for master in less than 30 seconds
• Master + up to 15 Aurora Read Replicas serve reads
• Support for Cross Region Replication

-> Important : Different entrypoints for read and write!


## Aurora Serverless 
Automated database Client instantiation and autoscaling based on actual usage
• Good for infrequent, intermittent or unpredictable workloads
• No capacity planning needed
• Pay per second, can be more cost-effective

## Seting up Aurora cross Region for Availability and Disaster Recovery
Aurora Cross Region Read Replicas:
• Useful for disaster recovery
• Simple to put in place

• Aurora Global Database (recommended):
• 1 Primary Region (read / write)
• Up to 5 secondary (read-only) regions, replication lag is less than 1 second
• Up to 16 Read Replicas per secondary region
• Helps for decreasing latency
• Promoting another region (for disaster recovery) has an RTO of < 1 minute

