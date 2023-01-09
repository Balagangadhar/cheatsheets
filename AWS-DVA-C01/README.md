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
- EC2 userdata : Launch script of EC2 instance 
- EC2 metadata : Info about the EC2 instance
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
## S3
TBD
## Container Section
### ECS(Elastic Container Service)
#### EC2 Launch Type
- Launches ECS tasks on ECS cluster
- Must provision infrastructure(EC2 instances)
- Must run ECS Agent to register in the EC2 instances 
- AWS takes care of Starting and stopping EC2 instances
#### Fargate Launch Type
- Launch Docker containers on AWS
- Serverless
- TO scale, need to increase number of tasks
### Data VOlumes
- Mound EFS file systems onto ECS tasks
### ECS Service Auto Scaling
- Target Tracking
- Step Scaling
- Scheduled Scaling
### Task Placement
- Task palcement strategies
  - binpack : Placed on the least available amount of CPU and memory, cost saving, minimizes no.of EC2 instance usage 
  - random : Placed randomly 
  - spread : Spread based on specific value, for eg. instanceid,attribute etc 
- Task palcement constraints
  - distictinstance
  - memberof 
### EKS
- Way to launch Amazon Kubernetes cluster
- Usecase: If infra is already on kubernetes, can use EKS for aws migration
## Elastic Beanstalk
- Managed service, automatically provisions, auto scales, load balances etc
- Its free but pay for underlying services
- Full control over configuration
### Elastic Deployment Options
- All at once
- Rolling
- Rolling with additional batches
- Immutable
#### Elastic Beanstalk Blue/Green Deployment
- Not a direction feature
- Create a new stage, deploy v2 on this, can validate gree deployment independently
- Once validated, can use Route53 weighted policies to route traffic. Once done swap the urls
## AWS CICD
### CodeCommit
- Version control
- No size limit on repositories
- Authentication
  - SSH keys, HTTP(with AWS CLI Credential Helper or Git Credentials for IAM user) 
- Authorization : IAM policies to manage uers/roles permissiont to repositories 
- Cross account access : Use AWS STS's assumeRole, do not share ssh keys or aws credentials  
### CodePipleine
- Build, Test, Deploy etc 
### CodeBuild
- Fully managed service, charged per minute for compute resources
- Runs instructions from buildspec.yml 
- buildspec.yml
  - Must be at the root of your code 
  - env 
   - Variables : Plaint text variables
   - parameter-store : variables stored in SSM Parameter Store
   - secrets-manager : Variable stored in AWS Secrets Manager 
  - phases
  - artifcats
  - cache
- Leverage CodeBuild Agent for running and troubleshooting locally 
- CodeBuild cannot access resources in a VPC
### CodeDeploy 
- Each EC2 instance/onpremises server must be running the CodeDeply Agent
- appspec.yml is pulled from S3
- EC2 instance will run the deployment instructions in appspec.yml 
- Configurations
  - One At a Time : Once EC2 instance at a time, if one instance fails then deployment stops
  - Half At a TIme
  - All at Once 
  - Custom 
- Failures: 
  - EC2 instances stay in FAILED state
  - New deployments will first be deployed on failed instances
  - To rollback, redeploy old deployment or enable automated rollback for failures 
  - If a rollback happens, CodeDeploy redeploys the last known good revision as a new deployment not a restored version 
### CodeStar
- An integrated solution that groups: GitHub, CodeCommit, CodeBuild, CodeDeploy, CloudFormation, CodePipleine, CloudWatch etc 
- Free service, limited customization
### CodeArtifact 
- Artifactory like service
### CodeGuru
- An ML powered service for automated code reviews and application performance recommendations 
- Provides two functionalities. CodeGuru Reviewer(development), Code Guru Profiler(Production)
## CloudFormation
TBD
## Security & Encryption 
- Encryption in flight
- Server side encryption at rest
- Client side encryption 
  - Data is encrypted by the client and never decrypted by the server and decrypted by a receiving client 
  - Could leverage Envelope Encryption 
 ### Key Management Service(KMS) 
 - Anytime you hear encryption for an AWS service, its most likely KMS 
 - AWS manages encrption keys for us
 - Fully integrated with IAM for authorization
 - Easy way to control access to your data
 - Able to audit KMS key usage using CloudTrail 
 - There are three types of KMS keys
   - AWS managed key : free, automatic key rotation every 1year
   - Customer Managed Keys(CMK) created in KMS: 1$/month, must be automatic  every 1 year
   - Customer Managed Keys imported(Must be 256-bit symmetric key) : 1$/month , only manual rotation possibel using alias
  - Pay for API call to KMS
  -  
 #### KMS Keys Types  
 KMS Keys is the new name for KMS Customer Master Key
 - Symmetric(AES-256 keys) 
   - Single encryption key that is used to Encrypt and Decrypt
   - AWS services that are integrated with KMS use Symmetric CMKs
   - YOu never get access to the KMS  key unencrypted, must call KMS API to use
 - Asymmetric(RSA & ECC key pairs) 
   - Public and private key pair 
   - Used for Encrypt/Decrypt or Sign/Verify operation
   - Used for encryption outside of AWS by users who can't call the KMS API 
#### Envelope Encryption
- KMS Encrypt API call has a limit of 4 KB, Client Side Encryption and Decryption using DEK 
- If you want to encrypt > 4KB, you need to use Envelope Encryption
- The main API that will help is GenerateDataKey API 
- Data key can be cached instead of creating new ones for each encryption  
- Use LocalCryptoMaterialsCache
#### KMS Request Quotas
- When you exceed a request quora, you get a ThrottlingException
- To respond, use exponential backoff
- This includes requests made by AWS on your bahalf(eg. SSE-KMS) 
- YOu can request a Request Quotas increase through API or AWS support 
#### Copying Snapshots accross regions  
- EBS Volumne Encrypted with KMS
- EBS Snapshot Encrypted with KMS 
- KMS ReEncrypt with KMS Key B 
- EBS Snapshot Encrypted with KMS Key B
- EBS Volum Encrypted with KMS Key B
#### Copying Snapshots accross accounts   
- Create a snapshot, encrypted with your own KMS key CMK
- Attach a KMS Key policy to authorize cross-account access
- Share the encrypted snapshot
- In Target, create a copy of the snapshot, encrypt it with CMD in your account 
- Create a volume from the snapshot 
#### S3 Encryption for Objects
- There are 4 mthods of encrypting Objects in S3
  - SSE-KMS : Leverage AWS Key Management Service to manage encryption keys : Max size 4kb 
  - SSE-S3 : Encrypts S3 objects using keys handled & managed by AWS : Needs permission on GetDAtaGenerateKey to encrypt and decrypt permissiont to decrypt, calls KMS API, charges apply for calling KMS api 
  - SSE-C: When you want to manage your own ecryption keys, upload keys 
  - Client Side Encryption : Only AWS SDK supports this, encryption and key management happens at client side
### SSM Parameter Store
- Secure storage for configuration and secrets
- Serverless, scalable, durable, easy SDK, version tracking of configuration/secrts
- Security through IAM, notifications with Amazon EventBridge
- Integration with CloudFormation 
- Standard is 10000 max, 4kb size, free
- Advanced 10,000, 8kb size, charges apply 
### AWS Secrets Manager
- Meant for storing secrets
- Capability to force rotation of secrets every X days 
- Automate generation of secrets on rotation(uses Labda) 
### CloudWatch Logs - Encryption
- Encryption is enabled at the log group level, by associating a CMK with a log group
- You must use the CloudWatch Logs API
  - associate-kms-ke : if the log group alredy exists
  - create-log-group : if the log group doesnt exist yet
## AWS Lambda
- No servers to manage, limited by time, run on-demand, scaling is automated
- Increasing RAM will also improve CPU and network
### Lambda - Synchronouse Invocations
- CLI, SDK, API Gateway, ALB
- Results are returned right away
- Error handling must happen at client side(retries, exponential backoff etc...)
- Lambda@Edge : Implement request filtering before reaching your application
### Lambda - Asynchronous Invocation
- S3, SNS, CloudWatch Events
- Events are placed in an Event Queue
- Lambda attempts to retry on errors
  - 3 tries total 
  - 1 min wait after st and then 2 min wait
 - Make sure the processing is idempotent 
 - If the function is retries, you can see duplicate log entries in CloudWatch Logs
 - DLQ(deal-letter queue) for failed processing
- By default if function returns an error, entire batch is reprocessed until the function succeeds, or the items in the batch expire 
- Grants the Lambda function permissions to AWS services, sample managed policies for Lambda: AWSLambdaBasicExecutionROle : Upload logs to CloudWAtch, AWSLambdaKinessExecutionROle : Read from Kinesis
- Best practice: Create one Lambda Execution role per function 
- BY default, lambda function is launched outside your own VPC, hence it cannot access resources in your VPC, you must define VPC ID, subnet and security groups. Role : AWSLambdaVPCAccessExecutionROle 
- Lambda FUnction Cofnigruation :RAM
  - 128MB to 10GB in 1MB increments
  - The more RAM, more vCPU you get
  - Default timeout is 3 seconds, max is 900 sec or 15 min 
### Lambda Execution Context
  - Temporary runtime environment, great for database connections, https clients, sdk clients
  - Execution context includes /tmp director 
  - Initialize outside the handler
- Lambda /tmp space
  - If Lambda needs to download a bigfilre to work, you can use /tmp 
  - Max size is 10GB 
  - FOr permanenet persistence of object, use s3
### Lambda Concurrentcy and Throttling
  - Limit 1000 concurrent executions
  - Can set a reserved concurrency, each invocation over the reserved limit, triggers a Throttle
  - If synchronouse invocation : throttle returns ThrottleError - 429 
  - If async : retry automatically and then go to DLQ
  - For higher limit, open a support ticket 
  - If you don't reserve, all 1000 executions might be consumed by one service for eg ALB, and other would get throggle error
 - Cold Starts & Provisioned Concurrency 
  - Cold Start : Code is loaded and code ouside the handler run, if the init is large, it can take some time, hence first request server by new instances have higher latency than the rest 
  - Provisioned Concurrency : Concurrency is allocated before the function is invocated, so the cold start never happens and all invocations have low latency 
### Lambda Function Dependencies
  - If lamdba depends on externa libraries, need to install packages alongside your code and zit it together
  - Upload the zip to lambda if less than 50MB else to S3 first
  - AWS SDK comes by default with every Lambda function 
### Lambda and CloudFormation - inline 
  - Use Code.ZipFIle property for inline code 
  - You cannot include function dependencies with inline functions 
### Lambda and CloudFormation through S3
  - Store Lambda zip in s3, must refer s3 zip location in CloudFormation code under code.S3Bucket, Code.S3Key, Code.S3ObjectVErsion
### Lambda Layers
  - An archive containing additional code, such as libraries, dependencies, or even custom runtimes
### Lambda Container Images
  - Deploy Lambda function as container images of up to 10GB from ECR
  - Can create your own image as long as it implements the Lambda RUntime API 
  - Test the containers locally using th Lambda RUntime Interface EMulator
### Lambda Versions
  - When yo work on a Lambda function, it will be on $LATEST
  - When we're ready to publish a Lambda funciton, we create a version
  - Versions are immutable
  - Versions have increasing version numbers and they get their own ARC(Amazon Resource Name) 
  - Version = Code+COnfiguration(Nothing can be changed- immutable) 
  - Each version of the Lambda function can be accessed
### Lambda Aliases
   - Aliases are points to Lambda function verions 
   - We can define dev, test, prod aliases have them point at different lambda versions
   - Aliases are mutable
   - Alisases enable Blue/Green deployment by assigning weghts to Lambda functions
   - Aliases cannot reference aliases 
### Lambda & CodeDeploy 
  - CodeDeploy can help automatic traffic shift for Lambda aliases 
  - Feature is integrated within the SAM framework 
  - Linear, Canary, AllAtOnce
### Best practices
   - Connec to databses or initialize SDK, pull dependencies outside of your function handler
   - Minized deployment package size, use layers where necessary
   - Avlid using recursive code, never have a Lambda function call itself 

## DynamoDB 
- Fully managed, highly available with replication accross multiple AZs
- Each table has primary key and can have infinite number of items(rows)
- Max size of an item is 400KB
- Primary Keys(partitione Key: HASH or Paritione Key + Sort Key(Hash + RANGE) eg. composite key impl 
### Read/Write Capacity Modes(RCU,WCU)
- Proviioned Mode(default)
  - Specific number of reads/writes per second
  - Need to plan capacity before hand
  - Throughput can be exceeded temporarily using Burst Capacity
  - If Burst Capacity has been consumed, you get a ProvisionedThroughputExceededException, then its advised to do an exponential backoff retry
- On-Demand Mode
  - Read/writes automatically scale up/down
  - No palnning requried but more expensive
 - You can switch between different modes once every 24 hours
 ### Write Capacity Units(WCU)
   - One WCU represents one write per second for an item up to 1KB 
   - Eg: We write 10 items per secon wth size 2kb then 10*(2kb/1kb) = 20 WCUs
 ### Read Capacity Units(RCU)
  Consistent Read
  - Strongly consistent : If we read just after a write, we get correct data, set consisten read to true, consumes twice RCU
  - Eventually Consistent Read(Default) : If we read just after write, we may get stale data because of replication
  - One RCU represents 1 strongly consistent or 2 eventually consistent reads per seconf for item utp 4KB 
  - Eg : 10 strongly consistent reads per secon with item size 4KB, 10*(4KB/4KB) = 10 RCUs
  - Eg : 10 Strongly Consistent Reads per second, with item size 6 KB  then 10* 1*(8kb 6kb is round off up to 8kb)/(4kb) = 20 RCUs
### Partiioneds Internal
- Data is stored in partiiones
- Paritione Kyes go through a hashing al to know which parition they go to 
- WCUs and RCUs are spread evenly across paritions
### DynamoDB Throttling
- If we exceed proviioned RCUs or WCUs we get ProvionedThroughputExceededException
- Reasons : Hot Keys(one parition key is being read too many times), Hot Paritiones, Very large items
- Solutions : Exponential backoff(already in SDK), Distribute paritione keys as much as possibel, if RCU issue, we can use DynamoDB Accelerator(DAX)
### Read/Write Capacity Modes - On-Demand
- Automatic read/write scal up/down 
- No capacity palnning needed
- No throttle, 2.5X more expensive
- Read Request Units,Write Requst Units
### DynamoDB - Writing Data
- PutItem : Creates a new item or fully replace old item : Consumes WCUs
- UpdateItem : Edits existing items attribute or adds new item if doesn't exist 
- ConditonaWrites : Accept a write/update/delete only if conditions are met
### DynamoDB - Reading Data
- Read based on Primary key
- ProjectionExpression can be specified to retrieve only certain attributes
- Query(based on filter expression), scan(Entire table) 
### DynamoDB - Deleting Data
 - DeleteItem : Deletes an indiviual item
 - DEleteTable : Deletes a whole table and its items, quicker than deleteItem on all items
### DynamoDB - Batch Operations
- BatchWriteItem : Up to 25 put or delete items, can t updateitem
- BatchGetItem : Returns items 
### Secondary Indices
- Local Secondary Index(LSI)
  - Alternative soft key for table
  - Up to 5 local secondary
  - Must be defined at table creation time
- Global Secondary Index(GSI)
  - Can be added after table creation
  - If the writes are throttles on the GSI, then main table will be throttled, even if the WCU on main tables are fine
### DynamoDB - PartiQL
- SQL like syntax to manipulate DYnamoDB Tables
### OPtimistic Locking
- DynamoDB has a feature called Contional Writes
- Each item has an attribute that acts as a version number 
### DynamoDB Accelerator(DAX)
- FUlly managed, highly available, in-memory cache for DynamoDB
- Solves the Hot Key problem(too many reads)
- 5 min TTL for cache
- Multi-AZ
### DAX vs ElastiCAche
- Store aggregation result in ElastiCache
- Indivicual object, query & scan cache in DAX
### DynamoDB Streams
- Ordered steam of itme-level modifications ina table
- Stream recofds can be
  - Send to Kinessis Data Streams
  - Read by AWS Lambda
  - Read by Kinesis Client Library applications
### DynamoDB - Time To Live(TTL)
- Automatically delete itmes after anexpirey timestamp
- Doesn't consume any WCU
- TTL attribute must be a number data type with Unix Epoch timestamp
- Expired item deleted within 48 hours of expiration
- Expired items, that haven't been deleted apperas in read/quries/scans, filter them out if not required
- Expired items are deleted from both LSIs and GSIs
### DynamoDB - Transactions
- ACID
- Consumes 2XWCUs & RCUs
- Eg : 3 Transaction writes per second with item size 5kb : 3*2*(5kb/1kb) = 30 WCUs
- Eg : 5 Transaction reads per second with item size 5kb : 5*2*(8kb(rounded)/4kb) = 20 RCU 
### Security & other features
- Security : Encryption at rest using AWS KMS and in-transit using SSL/TLS
- DynamoDB Local : Develp and test apps locally without accessing DynamoDB 
## AWS Cognito
### Cognito User Pools
- Sign in functionality for app suers
- Integrate with API Gateway & ALB
- User directory
- Manage User/pwd
### Cognito Identity Pools
- Federated identity
- Temporary access to AWS resource directly
- Access AWS services
### Cognito Sync
- Sync data from device to Cognitoy(Even offline and sync it once device is online)
- Deprecated and replaced by AppSync
### Cognito over IAM
- Hundres of users
- Mobile Users
- Authenticate with SAML

## Other Sections
### AWS Step Functions
- Workflows as state machines
- Written in JSON 
### APPSYnc
- Managed serve that uses GraphQL
### STS - Security TOken Service
- Allows to grant limited and temporary access to AWS resource(up to 1 hour) 
- AssumeRole : WIthing yor accour or cross account
### Redshift
- OLAP - Analytic Processing
- DAa warehousing, analytic queries
### Neptune
- Graphe Database
### DocumentDB 
- Managed MongoDB for AWS 
### AWS Certificate Manager(ACM)
- Lets you easily proviion, manage and deploy SSL/TLS certificates
- Used to provide in-flight encryption for websites
- Free of charge for public TLS certificates
- Automatic TLS certificate renewwal
### AWS Cloud Map
- Fully managed resource discovery service 
### AWS Fault Injection SImulator(FIS)
- Managed service for running fault injection experiments on AWS workloads

# Limits
## AWS Lambda
- Memory : 128 MB to 10,240 MB, in 1-MB increments
- Function timeout : 3sec default, 900(15 min)
- Environemnt variables : 4 KB, for all environment variables associated with the function, in aggregate
- Deployment Package Size : 50MB(Unzipped, direct upload), 250(unzipped), 3MB via console editor
- /tmp storage : 512MB to 10GB
- Invocation requests per function per Region : 10 req per sec(sync), unlimited(for async)
## Dynamo DB
- item size limit: 400KB
## SQS
- Max time msgs live : 14 days
- Max idle time : 30 consecutive days(queue might be deleted)
## RDS
- Total instance storage : 3TB
