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
## EC2



