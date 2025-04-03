# Distributed Application
This repository will share a distributed deployment of the open source Moodle application on Amazon Web Services.
![distributed-deployment](https://github.com/user-attachments/assets/90e0809c-6a41-4e4d-84e7-644b24502bf1)


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
**Elastics File System**
1. Efs offers two types of file sytems. Regional which stores data across multiple az for high availability and onezone which stores data in a single az in an effort to optimize cost but reduces resilience.
2. To connect your Amazon EFS file system to your Amazon EC2 instance, you must create two security groups: one for your Amazon EC2 instance and another for your mount target.

### Installation
Example of embedding code.

```bash
sudo apt update -y
```
