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

# Security Groups

* Security Groups are the fundamental of network security in AWS 
* They control how traffic is allowed into or out of our EC2 Instances.
* Security groups only contain allow rules
* Security groups rules can reference by IP or by security group
* Firewall attached to the EC2 instance
* Stateful: return traffic is automatically allowed, regardless of any rules
* All rules are evaluated before deciding whether to allow traffic

# Network Access Control List (NACL)

* NACL are like a firewall which control traffic from and to subnets
* One NACL per subnet, new subnets are assigned the Default NACL
* NACL are a great way of blocking a specific IP address at the subnet level
* Stateless: return traffic must be explicitly allowed by rules (think of ephemeral ports)
* Rules are evaluated in order (lowest to highest) when deciding whether to allow traffic, first match wins

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

# Creation of VPC

We are creating VPC to ensure the infrasturce of our project having the services like s3, Redshift, Glue, step functions. Always we keep critical confidentail data in private subnet and anything open to public in public subnet.

So we create VPC with 2 private and 2 public subnets along with NACL and internet gateway connection.
 
 1. Login into aws couunt.
 2. In search control give VPC
 3. Go for your VPC's
 4. There will be default VPC after account creation, then create VPC
 5. Give name tag  as myprojectVPC
 6. Give IPv4 CIDR as 10.71.0.0/16 (in this 16 bits are fixed out of 32 so we can have other 16 bits of different IP addresss. for every point it considere as 8 bits so there are 4 points 4*8 =32 bits)  [CIDAR BLOCK DETAILS](https://medium.com/geekculture/aws-vpc-and-subnet-cidr-calculation-and-allocation-cfbe69050712)
 7. Keep rest as default and select create VPC
 
 # Creation of Subnets
 
 9. VPC got created, now inside VPC there will be subnets, so click on subnets. As we already having default VPC there will also default subnets for default VPC. So now create subnets.
 10. In that create subnet select VPC ID which we created VPC as myproject VPC
 11. Consider this Subnet as public and give name as public Subnet A
 12. Give availability zone as 'US East (N.Virginia)/us-east-1a'
 13. Choose IPv4 CIDR block as 10.71.0.0/20. so last 20 bits of different IP address are alocated for one subnet.
 14. Now create other subnet as 'Private Subnet A' with IPV4 CIDR as 10.71.16.0/20
 15. The above private and public sunets create in one AZ as us-east-1a, now create other private and public subnet in other AZ us-east-1b
 16. Now create Public subnet B with 10.71.32.0/20 address
 17. Now create Private subnet B with 10.71.48.0/20 address
 
 # Creation of Internet Gateway
 
 19. Now create internet gateway( which is required public subnets to communicate with internet), click on internet gateway there will default internet gateway for default VPC so now click on create internet gateway and give 'myprojectinternetgateway'. Then click on action and attach created VPC status will be moved to "attached".

 # Creation of NAT Gateway

 21. Now create NAT gateway (which is required private subnet to communicate with internet).,click on NAT gateway give name as 'mynatgateway'
 22. As we observed from above image NAT will be in public subnet so we select public subnet A and we allocate elastic ip address and we clcik on create NAT 

  # Creation of Route Tables
  
 24. Now create route table, select on route tables we observer route table with our created VPC which is created automatically when we create VPC.
 25. Now Click on routes obsereved route is for VPC and create routes for internet gateway and NAT gateway
 26. Create route table for public subnet as 'my public route A' and then click on routes and edit routes provide 0.0.0.0/0 i.e what ever traffic from internet to public subnet should go through iinternet gateway. So select internetgateway also, which we created.
 27. Then assosiate public subnet A to this route table.
 28. Similary create other route table as 'my public route B' follow reest of steps and assosicate with public subnet B
 29. Similary create route tables for private subnets A , B where traffic need to go through NAT. So if the NAT is failed then recreate it and then associate the private subnet A,B respectively.

  # Creation of NACL's
  
  30. For blocking the traffic at subnet level attach the NACL. Create NACL by clicking on Network ACLs under security. As we created 4 subnets there is default NACL rules and also security rules so let keep all of them as default.


  
