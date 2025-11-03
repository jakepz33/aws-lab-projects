# Building a VPC

This Project I will:  
- Build a secure VPC architecture from scratch
- Connect resources across different network envrionments
- Implement network security and monitoring

It will touch on 4 main subjects:

🛜 Network Design - Decide IP address ranges to use in different parts of the nextwork. Create separate environmennts for different purposes and determine which resources need internet access and which should be kept private.     
🔐 Security - Create rules that control who can access specific resources. Set up security boundaries between different parts of the network and add multiple layers of protection.  
🤝 Connectivity - Configure internet access for resources that need it. Connect different VPCs together. Design networks so they continue working even if some other parts fail.  
👀 Monitoring - Review logs to find potential problems or security concerns. Make adjustments to improve network speed and reliability.  

### Components that will make up this project
1. Building a Virtual Private Cloud
2. VPC Traffic Flow and Security
3. Creating a Private Subnet
4. Launching VPC Resources
5. Testing VPC Connectivity
6. VPC Peering
7. VPC Monitoring with Flow Logs
8. Access S3 from a VPC
9. VPC Endpoints


## 1. Building a Virtual Private Cloud
Here I will:  
- Create a VPC
- Create a Public Subnet
- Create an Internet Gateway

<img width="989" height="811" alt="image" src="https://github.com/user-attachments/assets/aff2c861-e556-429d-85cf-3b3a4375a639" /><br/>  


**Why a VPC?**  
This is what keeps our resources private. Some resources like S3 and Lambda live outside of your VPC. But for services such as instances and DBs, these should go inside your VPC.

#### Create a VPC
- Here, I hit "Create new VPC" and input the following settings.
<img width="998" height="685" alt="image" src="https://github.com/user-attachments/assets/1b2f4508-04b8-4844-926e-05eb71a0e339" /><br/>

 > 10.0.0.0/16 means the first 16 bits of your IP address (10.0) are fixed, but the remaining 16 bits (i.e. the second half of the IP address) can be allocated however you like. Addresses within this CIDR block start at 10.0.0.0 and go up to 10.0.255.255. There are 2^16 (65,536) possible IP addresses within this subnet.

> A smaller CIDR block like 10.0.0.0/24 means only the last 8 bits can vary, while a larger CIDR block like 10.0.0.0/8 gives you 24 varying bits.

#### Create Subnets
- Now that I created my VPC, I am going to create subnets. A subnet is like a neighborhood in my city (VPC).
- I need to remember that subnets created in the same VPC cannot have overlapping CIDR blocks.


Creating a Public Subnet within my VPC with a subnet of 10.0.0.0/24 - giving me 256 available IPs  
<img width="731" height="778" alt="image" src="https://github.com/user-attachments/assets/69b1757b-97a4-4ca7-ab27-37c2746ad7c8" /><br/>


- Once that my subnet is created, I go into my subnet setttings and enable Auto-assign IP addresses.

#### Creating an Internet Gateway
My internet gateway will help link my VPC to the Internet. So that way I can give my required resources exposure to the outside world.

- I created an Internet Gateway "jakes-IGW" and attached it to my VPC.
  <img width="988" height="313" alt="image" src="https://github.com/user-attachments/assets/852f7966-e38d-4155-836e-3c0b3fc1db57" /><br/>

## 2. VPC Traffic Flow and Security

  In this step, its whats going to control traffic from the Internet to my VPC. Im going to create:
  1. A Route Table
  2. Security Group
  3. Network ACL

 <img width="1485" height="812" alt="image" src="https://github.com/user-attachments/assets/37906e76-c6e6-47f3-a525-78783ebcc5dd" /><br/>

 
#### Create a Route Table 
Even though I created an internet gateway and attached it to my VPC, I need to tell the resource in the public subnet how to get to the internet.  

- In my VPC's Route table 'jakes-routetable' I created a new route with the destination of 0.0.0.0/0 and the target being my VPCs internet gateway.
<img width="1007" height="525" alt="image" src="https://github.com/user-attachments/assets/b762828c-6234-4dc6-94ec-0037893b7b9f" /><br/>

#### Creating a Security Group
A Security group is a like a security guard at the entrance for each building (resource) in that neighborhood (subnet). Every resource must be associated with a security group. They attach to specific resources.

Security Groups are responsible for checking who comes in and out. And have strict rules about what kind of traffic can enter or leave the resource based on its IP address., protocols, and port numbers.

<img width="1017" height="673" alt="image" src="https://github.com/user-attachments/assets/734e390a-ed4d-46d0-8f64-2eddad0c95c3" /><br/>
> I created inbound rules of All IPv4 traffic and outbound of all traffic. This will let public users access the desired resource such as my EC2 instance.

#### Creating a Network ACL
**What is a Network ACL?**
- Network ACL can be thought of as Traffic cops stationed at every entry and exit point of your subnet, checking data against a tbale of ACL rules before allowing them through.

**What is the difference between security groups and network ACLs?**
- Network ACLs are used to set broad traffic rules that apply to an entire subnet. For example, blocking incoming traffic from a particular range of IP addresses or denying all outbound traffic to certain points
- SGs allow for more granular access control, managing access to individual resources. You can specify which ports and protocols are allowed for each connected resource.

> The inbound and outbound are the same for right now. Rule 100 will run, but if there are any rules less than 100 it will run those first. * is a catch all rule that will run if the others are met.
<img width="822" height="585" alt="image" src="https://github.com/user-attachments/assets/693cfdcd-760f-4a7d-859d-52da5937b990" /><br/>

## 3. Creating a Private Subnet
Here, I am creating a Private subnet to house my resources that should not have public access. I will create:
1. A private subnet
2. A private route table
3. A private network ACL

#### Creating a private subnet
> I created a private subnet with the IP range of 10.0.1.0/24 - I dont want to overlap with my public subnet 10.0.0.0/24
<img width="875" height="663" alt="image" src="https://github.com/user-attachments/assets/856f72d1-41f7-4aff-a4ee-d1e681ab4956" /><br/>

#### Set up a private route table
- In this step, I created a new Private Route table. It is set up just like the public route table. As of right now I just set up an association between this new private route table to my private subnet.



