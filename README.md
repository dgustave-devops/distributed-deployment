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

**Please note:** AWS no longer offers free public IP addresses and instance assigned a public IP will be charged accordingly. In adddition to this change in policy, newly launched instance will no longer be automatically assigned a public IP address. Therefore after launching an EC2 instance, to remotely access through a machine outside of the VPC, a public IP address or an elastic IP address will have to be assigned to the EC2 instance. In this case, we will assign a regular public IP address for demonstration purposes. Please note regular public IP address may change when the instance is restarted.

11. On the EC2 instance details page, click **Actions** button, >**Networking** >**Manage IP Addresses**. Select the network interface represented by **eth0** and enable auto-assign public IP. Then click save and confirm the changes. The EC2 instance should now be assigned a public IP address.
![aws-ec2-distri-instance-details](https://github.com/user-attachments/assets/e9467eed-225e-4ff4-a320-46051e083850)

<br>

![aws-ec2-distri-instance-publicip](https://github.com/user-attachments/assets/bd745794-8695-4e24-bad1-c243ab481550)

12. Remotely log into the instance via ssh using the previously downloaded key pair. Please ensure the permissions on your private key is secure else there may be issue with remotely accessing the instance.
   ```bash
   ssh -i <private key location> username@serverIP
   ```
13. Update virutal machine and upgrade packages
      ```bash
      sudo apt update -y
      sudo apt upgrade -y
      ```
      
![aws-ec2-instance-update](https://github.com/user-attachments/assets/a5219580-4e8b-470b-a8a8-4558e76c489c)

14. Installing Webserver - Apache 2
   ```bash
   sudo apt install apache2 -y
   ```

15. Installing PHP and PHP modules
   ```bash
   sudo apt install php libapache2-mod-php php-mysql php-xml php-mbstring php-zip php-intl php-gd php-curl php-soap -y
   ```
**Download and Setup Moodle**
1. Download the latest version of Moodle
   ```bash
   sudo wget https://download.moodle.org/download.php/direct/stable405/moodle-latest-405.tgz
   ```
2. Extract the moodle archive
   ```bash
   sudo tar -xvzf moodle-latest-405.tgz
   ```
3. Move the moodle files to the webroot directory
   ```bash
   sudo mv moodle /var/www/
   ```
4. Create a Moodle data directory and set file permissions (where Moodle will store its files):
   ```bash
   sudo mkdir /var/www/moodledata
   sudo chown -R www-data:www-data /var/www/moodledata /var/www/moodle
   sudo chmod -R 755 /var/www/moodledata /var/www/moodle
   ```
   
**Configure Apache 2 for Moodle**
1. Create a new apache configuration file for moodle
   ```bash
   sudo nano /etc/apache2/sites-available/moodle.conf
   ```
**Important notice regarding Moodle routing on version 4.5+**
Since Moodle 4.5, a Routing Engine is included in Moodle. This needs to be configured to handle requests using the "FallbackResource" directive:
More could be found on this [here](https://docs.moodle.org/405/en/Apache) .

2. Replicate the following strucure:
   ```bash
   ServerAdmin admin@example.com

   DocumentRoot /var/www/moodle
   Servername demo.example.com
   <Directory /var/www/moodle>
    #Options Indexes FollowSymLinks MultiViews
    AllowOverride None
    Require all granted
    FallbackResource /moodle/r.php
   </Directory>

    ErrorLog ${APACHE_LOG_DIR}/moodle_error.log
    CustomLog ${APACHE_LOG_DIR}/moodle_access.log combined
   ```

3. Enable the moodle site and rewrite module, then restart
   ```bash
   sudo a2ensite moodle.conf
   sudo a2enmod rewrite
   ```
4. Remember to disable the default apache site
   ```bash
   sudo a2dissite 000-default.conf
   ```
5. Set PHP max_input_vars to 5000 and remove ";".
   ```bash
   sudo nano /etc/php/8.3/apache2/php.ini
   ```
6. Restart apache
   ```bash
   sudo ssytemctl restart apache2
   ```










### File System Layer
**Elastics File System**
1. Efs offers two types of file sytems. Regional which stores data across multiple az for high availability and onezone which stores data in a single az in an effort to optimize cost but reduces resilience.
2. To connect your Amazon EFS file system to your Amazon EC2 instance, you must create two security groups: one for your Amazon EC2 instance and another for your mount target.


### Database Layer
