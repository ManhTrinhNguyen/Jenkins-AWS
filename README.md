# Deploy Application to EC2 
## Creat EC2 instances
### Step to launch Instance
  1. Add Tags
  2. Choose OS Image 
  3. Choose Capacity
  4. Network Configuration
  5. Configure Security Group 
  6. Add storage 
### Connect to instance
  1. Move .pem (key pair) to more secure location ~./ssh/
  2. Change persion of .pem to make permission stricter with only me as a user can read that (chmod 400 instancefile)
  3. Connect to instance : `ssh -i ~/.ssh/docker-server.pem ec2-user@Ipv4`
     - **-i**: This flag will take the pem file as a parameter
     - **ec2-user**: I need to ssh to instance as a ec2 user not as a root user 
    
## Install Docker
  1. **Update the Package Managet**: `sudo yum update`
  2. **Install Docker** : `sudo yum install docker`
  3. **When docker installed I have to start docker Daemon** : `sudo service docker start`
  4. **I want to run docker command without using sudo . I will add user to Docker group**: `sudo usermod -aG docker $USER` . After running this command user may not add to docker group yet . I need to exit and login again 

## Jenkins and AWS
### Connect to EC2 server instance from Jenkins server via ssh (ssh agent)
1. **Install SSH agent plugin from Jenkins** : `Go to Manage -> Plugin -> SSH agent` This plugin allow me to use SSH credentials of the EC2 server to SSH into my instance 