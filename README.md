# Virtual Private Cloud Setup
## Objectives.
Designing and setting up a Virtual Private Cloud (VPC) with both public and private subnets. 
Implement routing, security groups, and network access control lists (NACLs) to ensure proper communication and security within the VPC. Work in the AWS EU-West-1 (Ireland) region.

# VPC Architecture.
![WhatsApp Image 2024-07-11 at 9 58 19 AM](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/1d68390c-2f7e-45cd-92e3-235682753751)

# Steps in Achieving the above objectives.

# Step 1:
  - Change the Region
  - Click on the region name or the dropdown arrow next to it.
  - A list of available regions will be displayed.
  - Scroll through the list and select the desired region (e.g., "EU (Ireland)" for the eu-west-1 region).
![Screenshot from 2024-07-07 05-35-53](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/fe25db8a-5094-4c5a-b003-98fc6a1cbe59)


# Step 2: Create a VPC
  - Open the AWS Management Console.
  - Navigate to the VPC Dashboard:
  - Go to the VPC Dashboard.
  - Click on "Create VPC."
      - VPC Name: KCVPC
      - IPv4 CIDR block: 10.0.0.0/16
  - Click "Create VPC."
  
![Screenshot from 2024-07-07 06-44-48](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/4b7da205-104c-4fed-a7cd-db3c8fb72030)
![Screenshot from 2024-07-07 06-45-42](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/1c050dde-ebaa-4b77-b69a-4eda16e0122f)

# Step 3: Create Public and Private Subnets
  - Name : Public Subnet
  - IPv4 CIDR block: 10.0.1.0/24
  - Availability Zone: Europe (ireland) / eu-west-1a (Note: Any Availability Zone can be choosen)
  - click create subnet
    
   ![Screenshot from 2024-07-07 06-48-03](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/f48e4724-a1f5-4231-aecf-fc3234053d54)
  - Name : Private Subnet
  - IPv4 CIDR block: 10.0.2.0/24
  - Availability Zone: Europe (ireland) / eu-west-1a (preferably the same as the Public Subnet for simplicity)
  - click create subnet
    
   ![Screenshot from 2024-07-07 06-49-08](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/edc332d9-5190-4707-a4d2-1af9c8a1ba29)

# Step 4: Configure an Internet Gateway (IGW)
  -  Name: KCVPC-IGW
  -  Create and attach the IGW to KCVPC
    
  -  ![Screenshot from 2024-07-07 06-52-23](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/650305e3-225a-4507-9d91-6cbd6e9b5c15)

# Configure Route Tables:
  - create a Public Route Table and attached it to the VPC created
      - Name: PublicRouteTable
      - click create route table
        
        ![Screenshot from 2024-07-07 06-58-28](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/ee3f3f80-5455-4a3a-b56f-5859de07aa2a)

  - create a private Route Table and attached it to the VPC created
      -  Name: PrivateRouteTable
      -  click create route table
        

![Screenshot from 2024-07-07 06-58-28](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/0f5b99c0-f519-4499-82ab-e23e6725dc30)
![Screenshot from 2024-07-07 07-00-00](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/3dec40c8-0c4a-445a-9c26-8360581d8123)

# Associate PublicSubnet with this route table.
  - Add a route to the IGW (0.0.0.0/0 -> IGW).
    
![Screenshot from 2024-07-07 07-02-08](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/b5f8807b-254a-413e-8249-aecd48f9d6ce)

# Configure NAT Gateway:
  -  Create a NAT Gateway in the PublicSubnet.
  -  Allocate an Elastic IP for the NAT Gateway.
    
  -  ![Screenshot from 2024-07-07 07-06-28](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/d5c69ca2-7e58-4b07-af1e-1c09c23d4f29)
  -  Update the PrivateRouteTable to route internet traffic (0.0.0.0/0) to the NAT Gateway.
    
![Screenshot from 2024-07-07 07-55-05](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/eb22b5c4-ec4a-4111-8e21-303de1c4a7a3)

    

# Set Up Security Groups:
  ## Create a Security Group for public instances.
  - Name: Public-SG
  - select the VPC created
  - Allow inbound HTTP (port 80) and HTTPS (port 443) traffic from anywhere (0.0.0.0/0).
  - Allow inbound SSH (port 22) traffic from a your local IP
  - Allow all outbound traffic.
    ![Screenshot from 2024-07-07 07-11-02](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/9833873a-4872-4d9c-b975-d33802dd5c9f)
    ![Screenshot from 2024-07-07 07-11-37](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/9ed5bb55-2716-4f64-8949-11e3bfa5e69c)

## Create a Security Group for private instances
  - Name: Private-SG
  - Allow inbound traffic from the PublicSubnet on required ports  MySQL port 3306.
  - Allow all outbound traffic.
![Screenshot from 2024-07-07 07-17-27](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/d272e06f-42bb-4d8b-a3f9-2b1d5fbb4d4a)
![Screenshot from 2024-07-07 07-18-27](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/7b42d85b-682a-4354-9905-4bf620021adc)

# Configuring Network ACLs:
## Configure NACLs for additional security on both subnets.
### Public Subnet NACL: Allow inbound HTTP, HTTPS, and SSH traffic. Allow outbound traffic.
### Private Subnet NACL: Allow inbound traffic from the public subnet. Allow outbound traffic to the public subnet and internet.

![Screenshot from 2024-07-07 07-20-24](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/22808190-e7c6-45ec-a247-596f5f35214d)

# Deploying Instances for Public server (web-server).
  - Launch an EC2 instance in the PublicSubnet:
      - Name: Web-server(public instance)
      - AMI: Amazon Linux
        
   ![Screenshot from 2024-07-07 07-29-12](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/14e155bb-d393-4a14-927f-ea75f689d2d4)

## ------------------------------------------------------------------------------------
  - create a key pair for ssh connect
  - select the VPC craeted (KCPVC)
  - Auto-assign public IP: Enable
  - Use the public security group created 
  - click lunch instance.
    
![Screenshot from 2024-07-07 07-29-24](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/46ad9c46-6261-469e-bd46-077c33ff3e6b)


# Verifying: Verify that the public instance can be accessed via the internet
  - lunch you terminal
  - cd into the directory where the key pair created was saved.
  - enter the ssh-key to connect to the instance and verify if the instance can reach the internet ( by pinging google.com)
    
   ![Screenshot from 2024-07-07 08-25-47](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/cfc04afa-9827-4483-b53f-c79b2ef35ff5)
   ![Screenshot from 2024-07-07 09-38-59](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/580e4217-9520-4879-9272-838c3be22c13)

# Deploying Instances for Private server (data-bases server).
  - Launch an EC2 instance in the PrivateSubnet:
     - Name:data-base server(privateinstance)
     - AMI: Amazon Linux
       
  ![Screenshot from 2024-07-07 07-30-37](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/43f13374-fab3-4935-a137-761ed5c89bf4)

## ----------------------------------------------------------------------------------------
  - create a key pair for ssh connect
  - select the VPC craeted (KCPVC)
  - Auto-assign public IP: Enable
  - Use the public security group created 
  - click lunch instance.
    
 ![Screenshot from 2024-07-07 07-31-14](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/bc7b5a19-f058-4822-9188-cb42b62468d0)

# Verifying: Verify that the private instance can access the internet through the NAT Gateway and can communicate with the public instance.
  - on the public instance lunched, try pinging the private instance ipv4 local address on 10.0.2.13.
    
    
    ![Screenshot from 2024-07-07 09-19-42](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/e76998c8-4cf9-4917-8295-94f7f78bc64f)

  - on my local machine where the ssh-key is stored, copied the ssh-key into the public instance for ssh connection into the private instance.
    
    ![Screenshot from 2024-07-07 09-50-00](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/c8595915-9ef3-4b24-9dc6-5995fed7666e)
    
## -------------------------------------------------------------------------------------------
  - In the public instance, cd to the directory where the ssh-key is stored.
  - enter the ssh-key to connect to the instance and verify if the instance can reach the internet ( by pinging google.com)
    
![Screenshot from 2024-07-07 09-22-59](https://github.com/lahteeph/Virtual-private-cloud-VPC-/assets/167543895/c9ffd5ea-a683-42f7-84e3-8ada2e2a9257)


# Function of components
  - VPC (virtual private cloud): VPC helps create a secure and isolated environment for resources and applications in the cloud, with complete control over network configuration and security. which is also used in this project.
  - Subnets:
      - public Subnet was configured with a route 0.0.0.0/0 to be accessabile over the internet
      - private Subnet has no route to internet from public domain for security purposes. its can onlt be accessed from the Public Subnet created in the KCVPC.
  - IGW (Internet Gateways): IGW is Acting as a route for internet-bound traffic from your VPC and routing incoming traffic to the appropriate instances within your VPC which is the Public Subnet.
  - NAT (Network Address Translation): Allow resources in a private subnet to connect to services outside your VPC, but external services cannot initiate a connection with those instances.
  - Route Table:
      - public routing table was routed to the internet through IGW which make it accessible over the internet
      - private routing table was routed through the NAT Gateway, which makes it not accessible by other instances execpt the instances within the same VPC.
  - Security Groups: this functions as a firewall that controls the inbound and outbound traffic the the instances. in addition, Security Groups are a fundamental component of security and are used to protect resources from unauthorized access and attacks.
  - NACLs (Network Access Control Lists): Network Access Control Lists (NACLs) are used to control traffic at the subnet level, providing an additional layer of security.

    
