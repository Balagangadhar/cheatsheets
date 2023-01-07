## AWS Regions
- AWS has the concept of a Region, which is a physical location around. It is a cluster data centers
- Sample region names can be us-east-1
- Each region has many availability zones(AZ), min 2, max 6
- AZ is represented by an AWS Region code followed by a letter identifier (for example, us-east-1a)
- AWS has 216 points of presense
## AWS Global Services
- IAM
- Route 53
- Cloud Front
- WAF
## IAM 
- Root acount created by default, shodn't be shared or used
- Users are people in the organization 
- Groups can only contain users, nested groups are not allowed 
- A user can be part of 0 or many groups 
### IAM Permission
- Users or groups can be assigned to JSON documents called policies these policies define the permissions a user
- In AWS apply least privilege principle, don't provide more permissions than user needs
### IAM Policies Structure
- An IAM policy is a JSON document that consists of one or more statements. Each statement is structured as follows.
```javascript
 {
  "Statement":[{
    "Effect":"effect",
    "Action":[
      "s3:GetObject",
      "s3;PutObject"
    ],
    "Resource":["arn:aws:s3:::mybucket/*"],
    "Condition":{
      "condition":{
        "key":"value"
        }
      }
    }
  ]
}
```
- Effect: The effect can be Allow or Deny. By default, IAM users don't have permission to use resources and API actions, so all requests are denied. An explicit allow overrides the default. An explicit deny overrides any allows.

- Action: The action is the specific API action for which you are granting or denying permission. 

- Resource: The resource that's affected by the action. Some Amazon EC2 API actions allow you to include specific resources in your policy that can be created or modified by the action. You specify a resource using an Amazon Resource Name (ARN) or using the wildcard (*) to indicate that the statement applies to all resources.

- Condition: Conditions are optional. They can be used to control when your policy is in effect.
### IAM Password Policy
- Set min pwd length, should include upper case, lower case, numbers and non alphanumeric
### Multi Factor Authentication - MFA
- Virtual MFA device
  - Eg. Google Authenticator, Authy
  - Supports Multiple tokens on a single device
- Hardware TOTP token
- FIDO security key
### Different ways to access AWS
- AWS Management Console(Protected by password + MFA) 
- AWS CLI
- AWS SDK 
### IAM Security Tools
#### IAM Credentials Report(account level)
- A credential report that lists all users in your account and the status of their various credentials, including passwords, access keys, and MFA devices
- - Audit permissions of your account
#### IAM Access Advisor(user-level)
- Access advisor shows the service permissions granted to a user and when those services were last accessed. This information helps to revise policies 
## EC2(Elastic Compute Cloud) 
It mainly consists in the capability of : 
- Renting virtual machines(EC2)
- Storing data on virtual drivers(EBS)
- Distributing load across machines(ELB)
- Scaling the services using an auto-scaling group(ASG)
### EC2 User Data
- It is possible to bootstrap(launching commands) instance when machine starts
- Script in user data is only run once at the instance first start(Not after every restart) 
- Typical usecases of user data are
  - Installing updates
  - Installing software
- EC2 user data script runs with the root user 
### EC2 Instance Types  
AWS has folliwng naming convension  
```m5.2xlarge``` where  m: instance class,  5: generation, 2xlarge: Size within the instance class 
- General Purpose :  Balance among Compute, Memory and Networking, great for web servers or code repositores
- Compute Optimized :  Great for compute-intesive taks require high performance   
- Memory Optimized :  Storage Optimized  
### Security Groups 
Securtiy gruops act like firewall on EC2 instances.
- Security groups only contain **allow** rules  
- Can reference by IP or by security group 
- Can be attached to multiple instances   
- Locked down to region/VPC combination  
- If application is not accessible(time out) then mostly it would be security group issue, but if there is connection refused error then its application error
- All inbound traffic is blocked by default 
- All outbound traffic is authorised by default 
### EC2 Instances Purchasing Options 
- On-Demand : Short workload, predictable pricing, pay by second 
- Reserved(1&3) : Long workloads
  - Convertible reserved instances : Can modify az, size etc  
- Saving Plans(1&3) : Short workloads, cheap, can lose instances, less reliable 
- Dedicated Hosts : Book an entire physical server, control instance placement , allows you address compliance requirements and use your existing server bound software licenses  
- Dedicated Instances : No other customers will share your hardware, but no control over instance placement. However, Dedicated Instances might share hardware with other instances from the same AWS account that are not Dedicated Instances 
- Capacity Reservations : Reserve capacity in a specific AZ for any duration, charged at on demand rate whether you run instance or not
#### AMI
- Are customization of EC2 instance 
- Can launch EC2 from public AMI(AWS Provided), your own AMI or AWS Market place AMI 
### EC2 Instance Storage Section 
#### EBS
- Allows instances to persist data even after instance termination 
- Can only be mounted to one instance at a time(Using multi-attach feature can attach to 16 instances but only of tpe Provisional IOPS. It has some limitations)
- They are bound to specific availability zone , to move volumes across , you first need to snapshot it  
- Each EBS has one root volume type in addition to other EBS volumens. By default root EBS is deleted(Delete on Termination is selected for other volumes is is not selected 
- EBS snapshot is a backup of EBS, not necessary to detach volume to do snapshot but recommended, can copy snapshots across AZ or Region 
- 6 types(General purpose, provisioned IOPS SSD etc) 
- Only gp2/gp3 and io1/io2 can be used as boot volumes  
#### EC2 Instance Store
- EBS volums are network drivers with goodbut limited performance, if you need high performance hardware disk, use EC2 instance store 
- Lose their storage if instances are stopped
- Backups and replicate are your responsibility 
#### EFS - Elastic FIle System 
- Managed NFS(Network file system) can be mounted on many EC2
- EFS works with EC2 instances in multi AZ
- Compatible only with Linux based AMI not windows 
- No capacity planning required, scales automatically 
- Higher pricing point than EBS 
## Scalabilty & High Availability 
- Vertically scalability means increasing size of the instance for eg. t2.micro to t2.large. Eg systems RDS, ElastiCache can scale veritically 
- Horizontal Scalability means increasing the number of instances, it implies distributed systems
### Elastic Load Balancer 
- Managed load balancer
- There are 4 types of load balancers 
#### Classic Load Balancer 
- Supports TCP(Layer ), HTTP & HTTPS(Layer 7) 
- Health checks are TCP or HTTP based 
- Fixed hostname 
- Cookie name is AWSELB  
- Cross zone load balancing(Equally balancing across all AZs) is disabled by default. No charges for inter AZ data if enabled
- Supports only one SSL, must use multiple CLB for multiple hostname with multiple SSL certicates 
#### Application Load Balancer
- Layer 7
- Supports Http/2 and websocket and also redirects  
- Can rounte based on path, hostname, query string, header 
- Great for container based applications(Like Docker, ECS) 
- Target couprs are EC2 instances, ECS tasks, Lambda functions, private ip addresses 
- Application servers don't see the ip of the client directly. Use X-FOrwarded-For header for that. X-Forwarded-Port, proto for port and protocal 
- Cookie name is AWSALB
- Cross zone load balancing(Equally balancing across all AZs) is enabled by default. No charges for inter AZ data if enabled
- Old Gen
- Supports multiple listeners, uses SNI(Server Name Indication) for hostname with multiple SSL certificates 
#### Network Load Balancer 
- Layer4
- Used for extreme perforamnce
- Target groups : EC2, private IP addresses, ALBs 
- Cross zone load balancing(Equally balancing across all AZs) is disabled by default. Incor charges for inter AZ data if enabled
- Supports multiple listeners, uses SNI(Server Name Indication) for hostname with multiple SSL certificates 
#### Gateway Load Balancer
- Operates at Layer 30 ip pockets 
- Target groups EC2 and private ips 
### Auto Scaling Group(ASG) 
- Scales out(Add EC2 instances) Scales in(remove EC2 instances) 
- Automatically register new instances to load balancer 
- ASG are free(Only pay for underlying EC2 instances) 
- Attributes
  - Launch template( With which EC2 should be launched, it has ami type, ec2 user data, ebs volumes, ssh etc) 
  - Mini Size, Max Size, Initial Capacity 
  - Scaling Policies 
- Metrics such as avg CPU are computed for the overall ASG instances 
- Dynamic Scaling 
  - Target Tracking Scaling : Eg. average ASG CPU to stay around 40$
  - Step Scaling : Eg. When alrm is triggered then add 2 units 
  - Scheduled Actions : Increase min capacity to 10 at 3pm on Fridays
- Predictive Scaling : Forecast load and schedule
- Good metrics to scale on are CPUUtilization, RequestCountPerTarget, Average Network In/Out etc 
- Scaling Cooldowns 
  - After scaling activity happens, you are in the cooldown period(Default 300 seconds) 
  - During cooldown perios ASG will not launch or terminate additional instances 
  - Use ready to use AMI to redue configuration time in order to be serving request faster and reduce cooldown period 
## Database Services
### RDS
- Managed Service
- Can't SSH into RDS instances 
- Increase storage dynamically(You have to set Maximum Storage Threshold) 
### RDS Read Replicas
- Up to 5 read replicas, within AZ, Cross AZ or Cross Region
- Replication is Async so reads are eventually consistent 
- Applications must update the connection string to leverage read replics 
- In AWS data goes from one az to another, there is a network cost but for RDS read replicas within region is free 
## Aurora
-  AWS proprietary technology(Not open sourced), costlier than RDS  
-  Both postgres and mysql are supported as Aurora DB 
-  AWS cloud optimized 
-  Storage automaticcally grows in increments of 10GB up to 128 TB 
-  Can have 15 replicas 
### ElastiCache
- Managed Redis or Memcached
- Caches are in-memory with really high perofrmance and low latency 
- Using ElastiCache involves heavy application code changes
- Do not support IAM authentication 
- Data is partitioned across shards 
## Route 53 Section 
- DNS and domain registrar
- DNS record types
  - A : Maps a hostname to IPv4
  - AAAA : Maps a hostname to IPv6
  - CNAME : Maps a hostname to another hostname, target hostname must have A or AAAA record, can t create CNAME for top nodes(eg. cang create eg.com but www.eg.com is possible) 
  - NS : Name Servers for the hosted zone , control how traffic is routed for a domain 
  - Other advanced record types like SRV etc 
-  Hosted Zones 
  - A container for records that defned how to route traffic to a domaon and its sub domains 
  - Public Hosted Zones : Contains records that specific how to route traffic on the internet 
  - Private HOsted Zone 
  - $.50 per month per hosted zone 
- Alias Records
  - TTL mandatory for each DNS except Alias records 
  - You cannot set an alias recordfor an EC2 DNS name 
- Routing policies 
  - Simple, weighted, failover, latency based, geolocation, multi-value answer, geoproximity

## VPC  
- Private network to deploy resources. 
- Subnets allow you to partition your network inside VPC, pubcli and private subnets are available. 
- To define access to the internet and between subnets we use Route Tables
- Internet Gateways help VPC instances connect with internet 
- NAG gateways(AWS managed) and NAT instances (Self managed) allow your instances in your private subnets to access the internet while remainign private   
### Network ACL
- A firewall which controls traffic from and to subnet
- Can have ALLOW and DENY rules
- Are attached at the subnet level 
- Rules only include IP addresses
- Stateless, return traffic must be explicitely allowed by rules 
### VPC Flow Logs 
- Capture information about IP traffic going into your interfaces, VPC Flow Logs, Subnet Flow Logs, Elastic Network Interface Flow Logs
### VPC Peering
- Connect two VPC, privately using AWS network 
- Make them behave as if they were in the same network
- Must not have overlapping CIDR(IP address range) 
- VPC Peering connection is not transtive 
### Site-to-Site VPN and Direct Connect(DX)
- Site to site VPN connect an on-premise VPN to AWS, goes over public internet 
- DX, establish a physical connection between on-premise and AWS, connection is private, tkes at least a month to establish 

