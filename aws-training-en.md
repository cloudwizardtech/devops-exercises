1. Put tags on every resource you create: 
    - name: VPC, SG...
    - owner: your.name

2. Create a new VPC with a different CIDR from the default VPC:
    - 2 public subnets
      - first with 1024 hosts
      - second with 500 hosts
    - 2 private subnets
      - first with 1000 hosts
      - second with 200 hosts
    - 1 public subnet and 1 private subnet in a availability zone & 1 public subnet and 1 private subnet in another availability zone 
    - Create a Peering connection between the default VPC and the new VPC - so a instance in the default VPC can connect to a instance in the other new VPC

3. Create a EC2 instance:
    - install Jenkins, aws cli, curl/wget
    - after the setup is complete create a AMI of that instance

4. Host DNS zone in AWS in Route 53. If needed a domain/subdomain will be provided by us:
    - configure NS records
    - configure other needed DNS records

5. Use the AMI with Jenkis to create a Launch Template for an Auto Scaling Group with:
      - Security Group which permits ssh + http only from your location
      - Load Balancer with ACM (AWS Certificate Manager) certificate for the hosted zone made earlier

6. Copy the AMI with Jenkins in another account/region and start the instance from that copy:
    - share the AMI from your account to the other account and give permission to copy

7. Create: 
    - A public ALB (Application Load Balancer) in every AZ available
    - A private ASG (Auto Scaling Group) with NGINX which:
        - uses index.html to display the AZ, public IP, private IP of the instance
        - index.html is copied from an S3 Bucket at launch time
            - the S3 Bucket will be private; the files can not be accessed from a public host
            - AWS permissions will be granted by access and secret key

8. Create a Jenkins in ECS (Elastic Container Service) which updates the ASG:
    - to change the minimum and maximum number of instances
      - manual 
        - install aws cli
        - use aws ecs exec; aws autoscaling update-auto-scaling
      - pipeline 
        - install aws cli
        - use aws ecs exec; aws autoscaling update-auto-scaling
    - to change the AMI
      - pipeline - install aws cli
               - aws autoscaling update-auto-scaling - change the AMI from the launch template of ASG

9. Build all the above using CloudFormation using YAML (take the YAML course - https://kodekloud.com/courses/kubernetes-for-the-absolute-beginners-hands-on/)
    - there will be more stacks:
      - VPC
      - ECS
      - ASG

10. Build all the above using Terraform
    - there will be more modules:
      - VPC
      - ECS
      - ASG