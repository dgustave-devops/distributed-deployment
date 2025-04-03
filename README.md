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

**EC2 Instance**
1. Open up your AWS Console and select the EC2 Service.
2. Select **Instances** from the drop down on the left.
3. Select **Launch instances** in the top right orange button.
4. Set the instance **name** to **app** in the name field, under the **Name and tags** section.
5. Under the **OS and images** section, we are going to select a linux **Ubuntu image** (24.04 LTS).
6. Under the **Instance type** section, we are going to select a **t2.micro** instancee for demonstration purposes. Details on the types of AWS instances are available [here](https://aws.amazon.com/ec2/instance-types/) .
7. Under the **Key pair** section, select create a new key pair. Select **RSA** for type and **.pem** for format. Provide an appropriate name, and click create key pair. Your private keypair will automatically be downloaded to your machine.
8. Under the **Network settings** section, Click **edit** on the corner to the right of the section heading, and enable auto-assign public IP. This will provide the EC2 instance with a publicly accessible IP address (Please not this is not a static Ip and may change when the instance is shutdown and start, or restarted. For a static IP, please look into aws elastic IPs. we'll use the default VPC and subnet for demonstration purposes, and select the **app-sec-group** security group created above. \
**Please note:** The public IP for the application servers in this instance (Yes, pun intended) will be removed once configuration is complete. This is because the application servers will utimately only be accessed by the application load balancer internally within the VPC. All resources are provided private IPs for free which can be access internally within the AWS network.
9. Under the **Configure storage** section, configure an 8GB GP3 EBS volume.
10. Scroll down to the bottom of the page and select **launch instance**.
11. On the EC2 instance details page, click **Actions** button, >**Networking** >**Manage IP Addresses**. Select the network interface represented by **eth0** and enable auto-assign public IP. Then click save and confirm the changes. The EC2 instance should now be assigned a public IP address.
![aws-ec2-mono-instance-details](https://github.com/user-attachments/assets/e275cfb2-4945-42f4-b927-1efc0bdeb00f)













### File System Layer
**Elastics File System**
1. Efs offers two types of file sytems. Regional which stores data across multiple az for high availability and onezone which stores data in a single az in an effort to optimize cost but reduces resilience.
2. To connect your Amazon EFS file system to your Amazon EC2 instance, you must create two security groups: one for your Amazon EC2 instance and another for your mount target.


### Database Layer
