## AWS CMD structure 
  - aws <command> <subcommand> [options and parameters]
  - **command** : AWS services (EC2 or etc ...)
  - **subcommand**: which operations ? (run-instances, configure key-pair, create-security-group)

# Create IAM User
```
  - We can give user 2 types of access:
  - From the Console (UI): Login to the UI. Configure stuff
  - Programatic Access which i can assign to IAM account after i have created Account . This is when I execute different task from the command line Or could be for Services like Jenkins
  - Create access key for a User.
  - Configure Access key and Secret Access key on Laptop
  - Program matic access : Allow me to connect to the AWS Account using AWS CLI
```

## Create EC2 Instance
```
aws ec2 run-instances
  --image-id ami-0fca1aacaa1ed9168
  --count 1
  --instance-type t2.micro
  --key-name MyKpCli
  --security-group-ids sg-0bed0e6f3f4ebfc3c
  --subnet-id subnet-0e7ebb47044f8cd7f
```

**Create VPC**
```
  - Create VPC with specific cidr block : aws ec2 create-vpc --cidr-block 172.32.0.0/24 --query Vpc.VpcId --output text
  vpc-0d434a4d2b1901c4b

  - Create Subnet with VPC id : aws ec2 create-subnet --vpc-id vpc-0d434a4d2b1901c4b --availability-zone us-west-1a --cidr-block 172.32.0.0/25 --query Subnet.SubnetId --output text
subnet-0d220d84c63cad99e

  IP Calculator: https://mxtoolbox.com/subnetcalculator.aspx
  IP Calculator with binary values: http://jodies.de/ipcalc
  Calculate sub-CIDR blocks: http://www.davidc.net/sites/default/subnets/subnets.html 
```

**Create Security Group**: 
```
Create Security Group: aws ec2 create-security-group --group-name my-sg --description "My SG" --vpc-id vpc-00f5f8e457eb61189
Create Security Groups rules: aws ec2 authorize-security-group-ingress --group-id sg-0bed0e6f3f4ebfc3c --protocol tcp --port 22 --cidr 198.27.191.24/32
Create-keypair: aws ec2 create-key-pair --key-name MyKpCli --query 'KeyMaterial' --output text > MyKpCli.pem`
Get subnet ID: aws ec2 describe-subnets
```

## Filter and Query
**aws <command> describe-**: Filter and Query 
  - List certain component
  - Add filter : `aws ec2 describe-instances --filters <Name, Values>` Pick the components
  - Query : Pick specific attribute of those component
  - For example I want to get InstanceId value : `aws ec2 describe-instances --filters "Name=tag:Type, Values=web-server-with-docker" --query "Reservations[].Instances[].InstanceId"`

## IAM Command
**Using IAM command** : Create User, Group, and assign permissions
  - **Create group** `aws iam create-group --group-name MyGroupCli`
  - **Create user** `aws iam create-user --user-name MyUserCli`
  - **Add user to Group**: `aws iam add-user-to-group --user-name MyUserCli --group-name MyGroupCli`
  - **Get Group** : `aws iam get-group --group-name MyGroupCli`
  - **Give User Permisstion for EC2 service**: `aws iam attach-group-policy`
    1. **Get a Arn policy**: `aws iam list-policies --query 'Policies[?PolicyName==`AmazonEC2FullAccess`].Arn' --output text`
    2. **Attach Policy to a Group**: `aws iam attach-group-policy --group-name MyGroupCli --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess`
    3. **Show list attached group**: `aws iam list-attached-group-policies --group-name MyGroupCli`

## Create Credentials for User
**Create Crenditals for User**: 
  1. **Access to a Managment Console** : User need a password `aws iam create-login-profile --user-name MyUserCli --password MyPassword! --password-reset-required`
  2. **Also want User able to execute AWS CLI** : User need assign access key-pair
### Create Policy and Assign to Group
**Create Policy and Assign to a Group** : Create Json file that define a set of permission in that policy
*NOTE: This is a AWS policy Json file Reference*
```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": ["iam:ChangePassword"],
        "Resource": ["arn:aws:iam::565393037799:user/${aws:username}"]
      },
      {
        "Effect": "Allow",
        "Action": ["iam:GetAccountPasswordPolicy"],
        "Resource": "*"
      }		
    ]
}
```
  1. **To create policy to change passowrd**: `aws iam create-policy --policy-name changePwd --policy-document file://changePasswordPolicy.json`
  2. **Attach the polocy to user group**: `aws iam attach-group-policy --group-name MyGroupCli --policy-arn arn:aws:iam::565393037799:policy/changePwd`

### Create Access Key for new User 
  - **Create access key**: `aws iam create-access-key --user-name MyUserCli` 
### Switch AWS user for AWS CLI 
  - **Over write the default User aws configure command**: `aws configure set aws_access_key_id <Access Key>`
  - **No Overwrite leave admin as default**: By setting Environment Variable `export AWS_ACCESS_KEY_ID=<Access Key> && export AWS_SECRET_ACCESS_KEY=<SecretAccessKey>`

