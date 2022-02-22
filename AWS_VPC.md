# AWS VPC
VPC = Virtual Private Cloud. You can have multiple VPCs in an AWS region (max. 5 per region – soft limit.  Your VPC CIDR should NOT overlap with your other networks (e.g., corporate)

![](https://raw.githubusercontent.com/sudheera96/Orchestrate-Redshift-ETL-using-AWS-Glue-and-Step-Functions/main/images/VPC.jpg)

# Default VPC Walkthrough
* All new AWS accounts have a default VPC
* New EC2 instances are launched into the default VPC if no subnet is specified
* Default VPC has Internet connectivity and all EC2 instances inside it have public IPv4 addresses
* We also get a public and a private IPv4 DNS names

# Internet Gateway

* Allows resources (e.g., EC2 instances) in a VPC connect to the Internet
* It scales horizontally and is highly available and redundant
* Must be created separately from a VPC
* One VPC can only be attached to one IGW and vice versa
* Internet Gateways on their own do not allow Internet access...
* Route tables must also be edited!

# NAT Gateway

* AWS-managed NAT, higher bandwidth, high availability, no administration
* Pay per hour for usage and bandwidth
* NATGW is created in a specific Availability Zone, uses an Elastic IP
* Can’t be used by EC2 instance in the same subnet (only from other subnets)
* Requires an IGW (Private Subnet => NATGW => IGW)

# Network Access Control List (NACL)

* NACL are like a firewall which control traffic from and to subnets
* One NACL per subnet, new subnets are assigned the Default NACL
* NACL are a great way of blocking a specific IP address at the subnet level

  ## Default NACL
   * Accepts everything inbound/outbound with the subnets it’s associated with
   * Do NOT modify the Default NACL, instead create custom NACLs

# VPC Peering
* Privately connect two VPCs using AWS’ network
* Make them behave as if they were in the same network
* Must not have overlapping CIDRs
* VPC Peering connection is NOT transitive (must be established for each VPC that need to communicate with one another)
* You must update route tables in each VPC’s subnets to ensure EC2 instances can communicate with each other
*  You can create VPC Peering connection between VPCs in different AWS accounts/regions
*  You can reference a security group in a peered VPC (works cross accounts – same region)

# VPC Endpoints( AWS Private Link)
* Every AWS service is publicly exposed (public URL)
* VPC Endpoints (powered by AWS PrivateLink) allows you to connect to AWS services using a private network instead of using the public Internet
* They’re redundant and scale horizontally
* They remove the need of IGW, NATGW, ... to access AWS Services

   ## Types of Endpoints
   
   ### Interface Endpoints
   * Provisions an ENI (private IP address) as an entry point (must attach a Security Group)
   * Supports most AWS services
  ### Gateway Endpoints
   * Provisions a gateway and must be used as a target in a route table
   * Supports both S3 and DynamoDB

# VPC Flow Logs

* Capture information about IP traffic going into your interfaces:
  • VPC Flow Logs
  • Subnet Flow Logs
  • Elastic Network Interface (ENI) Flow Logs
* Helps to monitor & troubleshoot connectivity issues
* Flow logs data can go to S3 / CloudWatch Logs
* Captures network information from AWS managed interfaces too: ELB, RDS, ElastiCache, Redshift,WorkSpaces, NATGW,Transit Gateway.

# AWS Site-to-Site VPN

  ###  Virtual Private Gateway (VGW)
  
   * VPN concentrator on the AWS side of the VPN connection
   * VGW is created and attached to the VPC from which you want to create the Site-to-Site VPN connection
   * Possibility to customize the ASN (Autonomous System Number)
 
  ## Customer Gateway (CGW)
   * Software application or physical device on customer side of the VPN connection

# Direct Connect (DX)

* Provides a dedicated private connection from a remote network to your VPC
* Dedicated connection must be setup between your DC and AWS Direct Connect locations
* You need to setup a Virtual Private Gateway on your VPC
* Access public resources (S3) and private (EC2) on same connection
  * Use Cases:
   * Increase bandwidth throughput - working with large data sets – lower cost
   * More consistent network experience - applications using real-time data feeds
   * Hybrid Environments (on prem + cloud)
* Supports both IPv4 and IPv6
