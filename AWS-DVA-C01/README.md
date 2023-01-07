## AWS Regions
- AWS has the concept of a Region, which is a physical location around. It is a cluster data centers
- Sample region names can be us-east-1
- Each region has many availability zones(AZ), min 3, max 6
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


