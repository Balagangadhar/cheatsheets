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
- Consists of Version, Id, Statement
- Each statement has 
  - Sid : Optional
  - Effect 
  - Principal 
  - Action
  - Resource
  - Condition
 - Sample policy
```javascript
 {
  "Statement":[{
    "Effect":"effect",
    "Action":"action",
    "Resource":"arn",
    "Condition":{
      "condition":{
        "key":"value"
        }
      }
    }
  ]
}
```


