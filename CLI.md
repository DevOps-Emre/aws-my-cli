## AWS CLI: 

- This hands on explains how to install and configure AWS CLI. We'll also see how to create and manipulate the resources in AWS via AWS CLI

### References
- https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html
- https://awscli.amazonaws.com/v2/documentation/api/latest/index.html
- https://docs.aws.amazon.com/linux/al2023/ug/get-started.html


## Learning Outcomes

At the end of the this hands-on training, attendees will be able to;

- installing CLI on Windows, Linux or MAC O/S

- configuring CLI

- creating a resources with CLI

- working with Amazon Linux 2023 AMI

## Outline

- Part 1 - Installation

- Part 2 - Configuration

- Part 3 - Examples of the CLI commands

- Part 4 - Working with the latest Amazon Linux 2023 AMI


## Part 1 - Installation

### Step-1  Installation CLI on your "Local" 

- You can use the link below to install AWS CLI V2 according to your O/S.

- General page:
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html


- Windows:
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html


- Mac:
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
https://graspingtech.com/install-and-configure-aws-cli/


- Linux:
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip  #install "unzip" if not installed
unzip awscliv2.zip
sudo ./aws/install
```

### Step-2 Installation CLI on Linux (Ubuntu EC2)

#### Section-1 Creating an Ubuntu EC2

- Since  "Amazon Linux 2023" image is installed with "CLI Version 2" by default we create an EC2 instance with  "Ubuntu EC2" image so that we can practice how to install CLI V2. 

```text
-AMI             : Ubuntu 22.04
-Instance Type   : t2.micro
-Security Group  : SSH-22
```

#### Section-2 Install CLI Version 2 

- Check and Install AWS CLI Version 2
```
aws --version 

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

- install "unzip" if not installed than unzip 
```
sudo apt install unzip
unzip awscliv2.zip 
sudo ./aws/install
```

- Update the path accordingly if needed (AWS CLI Version 1 uses>>>> /usr/bin/aws)
```
export PATH=$PATH:/usr/local/bin/aws
```
or you may type >>> "bash"


## Part-2  Configuration - Local

### Step-1 Creating Access Key ID and Secret Access Key

- Go to the IAM service

- From the left hand menu 
```
  Click Users ---->> Select user---->>Security Credential--->> Access keys --->>>Create Access key 
```
### Step-2 Configuring (!!!!!!We keep going with Local)

- Configure your terminal with AWS Access Key ID and Secret Access Key for CLI
```
aws configure
```

- than fill the information and hit the ENTER 
```
AWS Access Key ID [None]: ****************
AWS Secret Access Key [None]: ****************
Default region name [None]: us-east-1
Default output format [None]: yaml
```

- Show the file inside the ./aws folder
```
cat .aws/config
cat .aws/credentials
```
- Check the existing profiles:
```
aws configure list-profiles (list of the profiles )
aws sts get-caller-identity (Who am I)
```

- Show how to use CLI on single terminal with multiple user:
```
aws configure --profile user1 (Configure the terminal for additional user )
aws iam list-users --profile user1
aws s3 ls --profile user1

```
- Check the existing profiles again:
```
aws configure list-profiles 
```

- Switch the current profile to "custom user" profiles
```
export AWS_PROFILE=user1 
```

- Switch the current profile to "default" user profile again.
```
export AWS_PROFILE=default 
```

- try to use CLI after deactivate KEYs from IAM. 


## Part 3 Examples of the CLI commands: 

- talk about the anatomy of CLI commands 
```
aws 
aws help
aws s3 help
aws s3 cp help
```

### Step 1- IAM

- List and Create IAM user 
```
aws iam list-users

aws iam list-users --output table
aws iam list-users --output text # list all user's usernames
# list current user's info
aws iam get-user

# list current user's access keys
aws iam list-access-keys 


aws iam create-user --user-name aws-cli-user # crate new user

# create multiple new users, from a file
allUsers=$(cat ./user-name.txt)
for userName in $allUsers; do
    aws iam create-user \
        --user-name $userName
done

# list all users
aws iam list-users --no-paginate

# get a specific user's info
aws iam get-user \
    --user-name aws-admin2

# delete one user
aws iam delete-user \
    --user-name aws-admin2

# delete all users
# allUsers=$(aws iam list-users --output text | cut -f 6);
allUsers=$(cat ./user-name.txt)
for userName in $allUsers; do
    aws iam delete-user \
        --user-name $userName
done

aws iam list-users | grep aws-cli-user
aws iam list-policies
```


### Step 2 - EC2
- check the available commands for ec2 
```
aws ec2 help
```

- list the EC2 instances
```
aws ec2 describe-instances
```

- list the EC2 instances in specific region
```
aws ec2 describe-instances --region us-east-2 --output table 
```
we can list key pairs
```
aws ec2 describe-key-pairs --key-name id_rsa --region eu-central-1
```
we can set the default region in your AWS CLI configuration
```
aws configure
```

-  Launch an EC2 instance (Note that this command may not work in PowerShell because of the different meaning of "\" )
```
aws ec2 run-instances \
   --image-id ami-053b0d53c279acc90 \
   --count 1 \
   --instance-type t2.micro \
   --key-name KEY_NAME_HERE #put your key name without ".pem"
```

- List the instances filtering with key name and image id.
```
aws ec2 describe-instances \
   --filters "Name = key-name, Values = KEY_NAME_HERE" # put your key name

aws ec2 describe-instances \
   --filters "Name = image-id, Values = ami-053b0d53c279acc90" # image id of EC2
```

- List the instances only with specific attribute. This command list the Public IP addresses of all EC2
```
aws ec2 describe-instances --query "Reservations[].Instances[].PublicIpAddress[]"
```


- You can combine "filtering and query"  !!!! #put your key name without .pem
```
aws ec2 describe-instances \
   --filters "Name = key-name, Values = KEY_NAME_HERE" --query "Reservations[].Instances[].PublicIpAddress[]" 
```
and 


```
aws ec2 describe-instances \
   --filters "Name = instance-type, Values = t2.micro" --query "Reservations[].Instances[].InstanceId[]"
```

- You may want to call multiple values under the single attribute. For example under the "Instances" attribute I need both "InstanceId and PublicIpAddress":

!!!! #put your key name without .pem

```
aws ec2 describe-instances  \
  --filters "Name = instance-type, Values = t2.micro" "Name = key-name, Values = KEY_NAME_HERE" \
  --query "Reservations[].Instances[].{Instance:InstanceId,PublicIp:PublicIpAddress}" 
```
```
!!!! #put your keypair name with MyKeyPair.pem and show on cli. remainder for connecting ec2 chmod 400 MyKeyPair.pem

aws ec2 create-key-pair --key-name MyKeyPair --query 'KeyMaterial' --output text > MyKeyPair.pem

aws ec2 delete-key-pair --key-name MyKeyPair --query 'KeyMaterial' --output text > MyKeyPair.pem
```
Here is a way to list all your instances across all regions with the AWS CLI:
```
In CLI

Add this for example to .bashrc. Reload it source ~/.bashrc, and run it

Note: Except for aws CLI you need to have jq installed

function aws.print-all-instances() {
  REGIONS=`aws ec2 describe-regions --region us-east-1 --output text --query Regions[*].[RegionName]`
  for REGION in $REGIONS
  do
    echo -e "\nInstances in '$REGION'..";
    aws ec2 describe-instances --region $REGION | \
      jq '.Reservations[].Instances[] | "EC2: \(.InstanceId): \(.State.Name)"'
  done
}
```
```
Security Group Creation/Deletion

aws ec2 create-security-group --group-name my-sg --description "My security group" --vpc-id vpc-1a2b3c4d

aws ec2 describe-security-groups --group-ids $(aws ec2 describe-instances --instance-id $id --query "Reservations[].Instances[].SecurityGroups[].GroupId[]" --output text) --output text

aws ec2 delete-security-group --group-id sg-903004f8 (sg id??)
```
- Let's stop and terminate the instance
```
aws ec2 stop-instances --instance-ids INSTANCE_ID_HERE 

aws ec2 terminate-instances --instance-ids INSTANCE_ID_HERE 
```

### Step 3 - S3

- check the existing S3 buckets and create a new bucket named "emre-cli-bucket"
```
aws s3 ls
aws s3 mb s3://emre-cli-bucket-emre
```

- create a file in the existing directory and copy this file to newly created bucket 
```
touch in-class.yaml
aws s3 cp in-class.yaml s3://emre-cli-bucket-emre
```

- copy this file to new folder inside bucket 
```
aws s3 cp in-class.yaml s3://emre-cli-bucket-emre/new/in-class.yaml
aws s3 ls s3://emre-cli-bucket-emre/new/
```

- show the example of the user data that we used in IAM session while covering ROLES. Since we don't have any AWS CLI configuration while launching an EC2, the CLI command inside the EC2 fetch the data via ROLES rather than CLI credentials. 
```
#!/bin/bash

yum update -y
yum install -y httpd
cd /var/www/html
aws s3 cp s3://emre-pipeline-production/index.html .
aws s3 cp s3://emre-pipeline-production/cat.jpg .
systemctl enable httpd
systemctl start httpd 
```

- Check inside of the newly created bucket.
```
aws s3 ls s3://emre-cli-bucket-emre
```

- delete the object inside the bucket 
```
aws s3 rm s3://emre-cli-bucket-emre/new/in-class.yaml
```

- remove the bucket
```
aws s3 rb s3://emre-cli-bucket-emre --force
```
## Part-4  VPC
Create Security Group
```
aws ec2 create-security-group \
    --group-name roman_numbers_sec_grp \
    --description "This Sec Group is to allow ssh and http from anywhere"
```
Check Security Group
```
aws ec2 describe-security-groups --group-names roman_numbers_sec_grp
```
Create rules od Security Group
```
aws ec2 authorize-security-group-ingress \
    --group-name roman_numbers_sec_grp \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0

aws ec2 authorize-security-group-ingress \
    --group-name roman_numbers_sec_grp \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0
```
To delete Security Group
```
aws ec2 delete-security-group --group-name roman_numbers_sec_grp
```
To list NAT Gateways in a specific VPC:
```
aws ec2 describe-nat-gateways --profile alice-poc
```
To list route tables in a specific VPC:
```
aws ec2 describe-route-tables --filters "Name=vpc-id,Values=<vpc-id>"
```
To list network ACLs in a specific VPC:
```
aws ec2 describe-network-acls --filters "Name=vpc-id,Values=<vpc-id>"
```
## Part-5  Working with the latest Amazon Linux 2023 AMI

- Call the latest version of AL2023
```
aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64 --region us-east-1
```

- Filter the image ID of latest AL2023
```
aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64 --query 'Parameters[0].[Value]' --output text
```

- Launching EC2 instance with latest AL2023 AMI. 
```
aws ec2 run-instances \
   --image-id $(aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64 --query \
               'Parameters[0].[Value]' --output text) \
   --count 1 \
   --instance-type t2.micro
```
## Part-6 AWS RDS and AWS DynamoDB
IAM Permission
If you want to manage the RDS resources using AWS CLI, first check that the IAM user or role has the following permissions:

rds:CreateDBInstance
rds:ModifyDBInstance
rds:DeleteDBInstance
rds:CreateDBSnapshot
rds:RestoreDBInstanceFromDBSnapshot
```
aws iam create-policy \
    --policy-name RDSManagementPolicy \
    --policy-document '{
        "Version": "2023-05-25",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "rds:CreateDBInstance",
                    "rds:ModifyDBInstance",
                    "rds:DeleteDBInstance",
                    "rds:CreateDBSnapshot",
                    "rds:RestoreDBInstanceFromDBSnapshot",
                    "rds:DescribeDBInstances",
                    "rds:RebootDBInstance"
                ],
                "Resource": "*"
            }
        ]
    }'
```
Attach the policy to your IAM user or role:
```
aws iam attach-user-policy     
--policy-arn arn:aws:iam::aws:policy/RDSManagementPolicy     
--user-name your-iam-username
```
To create a new DB instance, use the following command:
```
aws rds create-db-instance     
        --db-instance-identifier mydbinstance     
        --db-instance-class db.t2.micro     
        --engine mysql     
        --allocated-storage 20     
        --master-username admin     
        --master-user-password mypassword
```
![image](https://github.com/user-attachments/assets/8e717190-a016-45be-8eec-3b35cba9f1f6)
![image](https://github.com/user-attachments/assets/51c00432-60d6-4856-8c49-028d7e03e8b9)

To modify an existing DB instance, such as changing the allocated storage, use the following command:
```
aws rds modify-db-instance     
        --db-instance-identifier mydbinstance     
        --allocated-storage 30     
        --apply-immediately
```
![image](https://github.com/user-attachments/assets/3e1f06d0-2d90-4e4d-9874-a5fa365bb518)
![image](https://github.com/user-attachments/assets/9de6d036-b751-41c6-9556-9f8904974db2)

To create a snapshot of your DB instance, use the following command:
```
aws rds create-db-snapshot 
    --db-instance-identifier mydbinstance 
    --db-snapshot-identifier mydbsnapshot
```
We can restore DB instance from a snapshot, use the following command
```
aws rds restore-db-instance-from-db-snapshot 
    --db-instance-identifier mynewdbinstance 
    --db-snapshot-identifier mydbsnapshot
```
To delete a DB instance, use the following command:
```
aws rds delete-db-instance 
    --db-instance-identifier mydbinstance 
    --skip-final-snapshot
```
![image](https://github.com/user-attachments/assets/c18c5165-02e3-4a71-9333-c4515e4f3377)
![image](https://github.com/user-attachments/assets/7a042b18-cfa9-41d1-955f-0f0d5f91c864)

**Use Amazon DynamoDB with the AWS CLI
https://docs.aws.amazon.com/cli/v1/userguide/cli-services-dynamodb.html
!!!!

