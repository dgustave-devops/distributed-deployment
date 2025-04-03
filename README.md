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

### Application Layer
**Application Security Group**
1. Open the EC2 dashboard and select **security groups** under the **Resources** section.
2. Click the orange **Create security group** button to the top right.
3. Under the **Basic details** section, set the **security group name** to **app-sec-group**, **description to **Allow ALB access to application**, and select the appropriate **VPC**. This security group will only allow access from the **Application Load Balancer** once configuration is complete.
4. Under the inbound section, click **Add rule**, and open the following ports, 80, 443, 22 for all addresses. This configuration is tempoary and will be tightened later to only allow access from the **Application Load Balancer**.
![aws-ec2-app-sec](https://github.com/user-attachments/assets/9516be28-a5d8-46f5-befd-fb29ff4f56b8)

5. Scroll to the bottm, and click the orange **Create security group** button to the bottom right corner.
![aws-ec2-app-sec-complete](https://github.com/user-attachments/assets/817ea415-de1f-4e13-9f2d-6c9c420332a1)















### File System Layer
**Elastics File System**
1. Efs offers two types of file sytems. Regional which stores data across multiple az for high availability and onezone which stores data in a single az in an effort to optimize cost but reduces resilience.
2. To connect your Amazon EFS file system to your Amazon EC2 instance, you must create two security groups: one for your Amazon EC2 instance and another for your mount target.


### Database Layer
