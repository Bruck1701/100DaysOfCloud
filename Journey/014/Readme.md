## VPC concepts

VPC - Virtual Private Cloud is AWS name for you private network on AWS to deploy your resources. VPC is a regional resource, that means that different regions are on different networks.

Subnets allow you to partition your network inside your VPC. Subnets, on the other hand, are an AZ resource. different AZ will be on different subnets.

A public subnet is a subnet that is accessible from the internet
A private subnet is a subnet that is not accessible from the internet

• To define access to the internet and between subnets, we use Route Tables

Default VPC:
one public subnet for each AZ. ( no private subnet)

![vpcDiagram](https://user-images.githubusercontent.com/17711277/88486821-c7055380-cf80-11ea-8a1d-11ad5812a96a.jpg)


### Internet Gateway and NAT Gateways

![internet-gateway-diagram](https://user-images.githubusercontent.com/17711277/88486819-c5d42680-cf80-11ea-8cde-63e85066469a.png)

Internet Gateway is responsible to connect the the instances on the public subnet to the internet (Public subnets have a route to the internet gateway).

For the instances on the private subnet, we either need NAT Gateways(AWS-managed) or NAT instances (user-managed) to allow to access the internet( update softwares for example)

### Network ACL & Security Groups

NACL (Network ACL)
* A firewall which controls traffic from and to subnet
* Can have ALLOW and DENY rules
* Are attached at the Subnet level
* Rules only include IP addresses

### Security Groups
* A firewall that controls traffic to and from an ENI or an EC2 Instance
* Can have only ALLOW rules
* Rules include IP addresses and other security group

# VPC Flow Logs

Capture information about IP traffic going into your interfaces:
* VPC Flow Logs
* Subnet Flow Logs
* Elastic Network Interface Flow Logs

Helps to monitor & troubleshoot connectivity issues. 
* Subnets to internet
* Subnets to subnets
* Internet to subnets

Captures network information from AWS managed interfaces too: ElasticLoad Balancers, ElastiCache, RDS, Aurora, etc…
* VPC Flow logs data can go to S3 / CloudWatch Logs

## VPC Peering ( connecting different VPCs )

* Connect two VPC, privately using AWS’ network
* Make them behave as if they were in the same network
* Must not have overlapping CIDR (IP address range)
* VPC Peering connection is not transitive (must be established for each VPC that need to communicate with one another)

VPC Endpoints ( connecting resources from the VPC to AWS'network)
* Endpoints allow you to connect to AWS Services using a private network instead of the public www network
* This gives you enhanced security and lower latency to access AWS services

* VPC Endpoint Gateway: S3 & DynamoDB
* VPC Endpoint Interface: the rest
* Only used within your VPC

## Site to Site VPN & Direct Connect ( connecting your on-premisse machines to AWS )

Site to Site VPN
* Connect an on-premises VPN to AWS
* The connection is automatically encrypted
* Goes over the public internet

Direct Connect (DX)
* Establish a physical connection between onpremises and AWS
* The connection is private, secure and fast
* Goes over a private network
* Takes at least a month to establish

-> Site-to-site VPN and Direct Connect cannot access VPC endpoints


AWS 3 Tier Architecture:

![threeTier](https://user-images.githubusercontent.com/17711277/88487129-c2419f00-cf82-11ea-912f-3777ee0e8b0d.jpg)



