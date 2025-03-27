# distributed-moodle
This repository will share a distributed deployment of the open source Moodle application on Amazon Web Services (AWS) alongside its system architecture.

Amazon Web Services components:
vpc
internet gateway
auto-scaling group
efs
mysql database
alb
az
security groups
subnets

editors notes
elastics file ssytem
efs offers two types of file ssytems. Regional which stores data across multiple az for high availability and onezone which stores data in a single az in an effort to optimize cost but reduces resilience.

To connect your Amazon EFS file system to your Amazon EC2 instance, you must create two security groups: one for your Amazon EC2 instance and another for your mount target.




