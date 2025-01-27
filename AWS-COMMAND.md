aws ec2 run-instances
  --image-id ami-0fca1aacaa1ed9168
  --count 1
  --instance-type t2.micro
  --key-name MyKpCli
  --security-group-ids sg-0bed0e6f3f4ebfc3c
  --subnet-id subnet-0e7ebb47044f8cd7f

**Create Security Group**: `aws ec2 create-security-group --group-name my-sg --description "My SG" --vpc-id vpc-00f5f8e457eb61189`
**Create Security Groups rules**: `aws ec2 authorize-security-group-ingress --group-id sg-0bed0e6f3f4ebfc3c --protocol tcp --port 22 --cidr 198.27.191.24/32`
**Create-keypair**: `aws ec2 create-key-pair --key-name MyKpCli --query 'KeyMaterial' --output text > MyKpCli.pem`
**Get subnet ID**: `aws ec2 describe-subnets`