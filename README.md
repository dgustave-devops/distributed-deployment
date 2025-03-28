# Distributed Application
This repository will share a distributed deployment of the open source Moodle application on Amazon Web Services (AWS) alongside its system architecture.

### AWS Components:
- Virtual Private Cloud (VPC)
- Subnet
- Internet Gateway
- Auto-scaling Group
- Security Groups
- Elastic File System (EFS)
- Amazon Aurora (Serverless)
- Application Load Balancer (ALB)
- Availability Zones (AZs)



### Developer Notes:

#### Elastics file system
Efs offers two types of file sytems. Regional which stores data across multiple az for high availability and onezone which stores data in a single az in an effort to optimize cost but reduces resilience.

To connect your Amazon EFS file system to your Amazon EC2 instance, you must create two security groups: one for your Amazon EC2 instance and another for your mount target.



## Installation

Example of embedding code.

```bash
sudo apt update -y
```
