
- Master (root) accounts
- Organizations
- Member Accounts
- Organizational Units (OU's)
- SCP's

- IAM
- Policies
- ARN's

All these terms are essential to know when testing an AWS Environment.


# AWS Organizations:

AWS offers what's called ***Organizations*** - which act as a centralized way for businesses to effectively manage AWS accounts and the allocations of resources. Only the ***Master account*** (or root account) can create an Organization, and there is only one Master account in an organization. AWS Organizations allow multiple AWS ***Member*** accounts grouped into ***Organizational Units*** (OU's), creating a hierarchical structure. OU's can then be controlled by ***Server Control Policies*** (SCP's), establishing *broad* access control at the organizational level.

The following visual will help understand this better:


![AWS-Orgs](https://github.com/johnchd/johnchd.github.io/raw/main/_posts/1.png)



# Accounts, OU's, and SCP's:

AWS consists of 2 kinds of accounts: 
- Master accounts 
- Member accounts

**Master account:**
- Also known as the Management account or root account, responsible for creating an Organization
- Only ONE of these accounts
- Can invite other AWS accounts and create OU's
- SCP's are applied by the Master account to define access-control policies for OU's ***and*** member accounts 

**Member account:**
- Typical AWS accounts that join an AWS organization, created by the Master
- Subject to access controls and policies defined by the Master
- Can be grouped into OU's for management
- Can create and manage their own AWS resources, but ultimately operate under the constraints of the SCP's applied by the Master

To represent an organization's structure, ***Member*** accounts are grouped into ***Organizational Units*** controlled by ***SCP's***. OU's and SCP's are ***are designed to consistently implement access control policies across an entire organization.***

**SCP's:**
- Designed for controlling access control at scale across multiple OU's or AWS accounts
- Can be attached at different levels of organizational hierarchy, including the root level and other OU's. NOTE: SCP's are carried from the top down, so OU's or members below will inherit the SCP
- Key component for access control and compliance within large businesses

Overall the Master account is responsible for the creation of an Organization, along with the defining of access controls via SCP's.  Member accounts join the organization and operate within the constraints of the access controls defined by the Master. 


# ARN's, IAM Users, IAM Policies, and IAM Roles

When enumerating a cloud environment in AWS, you will come across what's called an ***ARN***. ***ARN*** is an acronym for ***Amazon Resource Name*** and is used as a unique identifier for AWS resources. Below is the breakdown of an ARN:

```
arn:aws:service:region:account-id:resource
```

The values **arn** and **aws** are fixed and will always remain the same.
- **service:** indicates the AWS service, for example s3, ec2, iam, etc.
- **region:** indicates what AWS region the resource is located
- **account-id:** represents the AWS account ID that owns the resource
- **resource**: indicates the resource itself

Attached below is an example **ARN** for an EC2 instance:
```
arn:aws:ec2:us-east-1:123456789:instance/i-123456789abcdef
```

**ARN's** are important to know because ***each service within AWS will be represented by an ARN*** and **IAM Policies** will reference these ARN's to specify which AWS resources users/roles are permitted access.

Note: both IAM policies and SCP's are responsible for access control; however IAM policies are focused on users/groups, while SCP's are more broad organizational-level control within an organization.

To further understand a scenario implementing the use of **IAM Users** and **IAM Policies**, consider the following scenario:

Your manager has asked you to administer a specific EC2 instance within AWS. As a result, your manager has created you an **IAM User**. In order to follow the principle of least privilege, your manager has assigned **IAM Permissions** (to your IAM User) granting you full permissions to the specified EC2 instance by using the associated ARN.

The associated **IAM Permission** assigned for the **IAM user** would look like this:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:*",
      "Resource": "arn:aws:ec2:us-east-1:123456789:instance/i-123456789abcdef"
    }
  ]
}
```

Lastly, there are **IAM Roles**. To understand IAM Roles, consider the following scenario:

Your manager needs help with regularly backing up EC2 instance data to an S3 bucket. As a result, you create an IAM Role named `ec2-to-s3-role`. Within the `ec2-to-s3-role`, you attach a policy specifying permissions to read/write data to the s3 bucket. You then assign this IAM Role (`ec2-to-s3-role`) to the associated EC2 instance.

The above example would have the following IAM Role attached to the EC2 instance:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::ec2-to-s3-bucket"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::ec2-to-s3-bucket/*"
        }
    ]
}

```


Thanks for reading and I hope you found this blog useful. Please reach out if I have a misunderstanding on anything.

Next up will be enumerating AWS cloud environments.





