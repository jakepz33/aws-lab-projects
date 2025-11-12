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
<img width="1092" height="815" alt="image" src="https://github.com/user-attachments/assets/8269d13f-0c89-4b6f-8d90-5348a97a4237" /><br/>


#### Creating a private subnet
> I created a private subnet with the IP range of 10.0.1.0/24 - I dont want to overlap with my public subnet 10.0.0.0/24
<img width="875" height="663" alt="image" src="https://github.com/user-attachments/assets/856f72d1-41f7-4aff-a4ee-d1e681ab4956" /><br/>

#### Set up a private route table
- In this step, I created a new Private Route table. It is set up just like the public route table. As of right now I just set up an association between this new private route table to my private subnet. With this private route table set up, it lets me make sure it can only direct traffic to another internal resource (instead of the public internet).

#### Set up a private Network ACL
<img width="949" height="831" alt="image" src="https://github.com/user-attachments/assets/bd0a7e4b-829b-4c7f-87d2-8c4f754a384f" /><br/>

- In this step, I created a new Network ACL and associated this with my private subnet "Jakes Private Subnet". When observing the Inbound and Outbound rules, these deny all traffic. I am leaving this for right now.
<img width="2518" height="850" alt="image" src="https://github.com/user-attachments/assets/e6264042-a87c-4c3d-92d8-8bde243c1960" /><br/>


## 4. Launching VPC Resources
In this step I am going to:
1. Launch an EC2 instance in my Public Subnet
2. Launch an EC2 instance in my Private Subnet

#### Launching a Public EC2 Instance
In this step im going to:  
- Create a new EC2 instance inside my public subnet
- Find a way for me to access my EC2 instance
- Put my instance in a VPC

Terms: AMI, instance type, Key pair
<img width="2678" height="1198" alt="image" src="https://github.com/user-attachments/assets/f33f201c-dba1-4bdd-8175-c7a29253c72f" /><br/>  
> From this instance menu, I chose the default configuration because we don't need a lot of compute for this project.  
> I chose the Amazon AMI - its like the blueprint used to create EC2 instances and contains the operating system along with the applications needed to launch an the instance.  
> The instance type is the hardware component such as CPU power, memory size, storage space, etc.
> I also created a new key pair, this is to help engineers directly access their virtual machines, like EC2 instances.

The server should now be launched from within the public subnet.
<img width="2534" height="1409" alt="image" src="https://github.com/user-attachments/assets/84152c32-32c4-4756-b9f9-b7f3e2aeefb9" /><br/>

#### Launch a Private EC2 Instance
In this step im going to:
- Launch another EC2 Instance
- Protect my EC2 instance
- Set up a way got my EC2 instances to speak to each other.
<img width="1101" height="806" alt="image" src="https://github.com/user-attachments/assets/59ddcc2d-6871-44ed-b08f-fbff75c80dff" /> <br/>

For the Private instance configuration I did a few things. I used the Amazon Default AMI and Instance Type. I also used the same key-pair as a my Public Instance. I made sure to select the correct VPC and my Private Subnet. I didn't have a security group already made, so I am creating a new one in this menu. This SG allows for SSH and Source-Type: My Public Subnet Security Group. Allowing for inbound access from that SG.
<img width="2524" height="1431" alt="image" src="https://github.com/user-attachments/assets/96697dca-f8a3-4262-833b-2e529ef3ccb9" /><br/>

## 5. Testing VPC Connectivity
In this step, I am going to:
1. Connect to my Public Server from the AWS Management Console.
2. Test connectivity between my EC2 instances
3. Test VPC connectivity with the internet

#### Connect to Jakes Public Server
In this step I am going to:
- Set up a connection to my Public Server

I am going to connect to my EC2 instance via EC2 Instance Connect. In order to be able to connect my instance, I need to ensure my Security Group allows for SSH from anywhere IPv4. Once connected, I should see this screen.
<img width="1634" height="736" alt="image" src="https://github.com/user-attachments/assets/d69ead70-8ebb-4adc-a14f-77d3e7ec87d0" /><br/> 
> There are a few configurations I want to make sure of so I am actually able to connect to my EC2 instance.
> My Route Table, Network ACLs and Security Groups. for my Route Table, I want to make sure that it allows traffic from my Internet Gateway. Network ACLs, I allow all Inbound and Outbound traffic. Security Groups, I allow SSH connectivity from Anywhere IPv4 in this case. (usually not best practice, but will do for this project.)

#### Test Connectivity Between my EC2 Instances
In this step I am going to:
- Get my Public Server to talk to my Private Server

<img width="912" height="802" alt="image" src="https://github.com/user-attachments/assets/ff3dc0ff-9770-46b1-ae9d-cee438417867" /><br/>

If I try to ping my Private server from within my Public Server shell, there is no response. Why is this happening?
- could be because the target machine or its network might be blocking the type of messages used in ping which are ICMP traffic.

In this case, when looking at the Private Subnets NACL, there is an explicity Deny for all traffic. To fix it, I enabled ICMP IPv4 for inbound and outbound rules.  
I am doing the same for the Private Security Group.  

<img width="2486" height="1099" alt="image" src="https://github.com/user-attachments/assets/a467a977-1b7c-45b8-962f-3db929570b14" /><br/>

#### Test VPC Connectivity with the Internet
In this step I am going to:
- Get my Public Server to talk to the internet

For this step, I ran the simple command curl example.com  
This will return HTML that was rendered for that web URL. This is possible because of the IGW, Route Table, NACL, Security Groups all being configured to allow for Internet access of my Public EC2 Instance.

## 6. VPC Peering
In this part of the project, I am going to level up this architecture up by setting up VPC Peering - resulting in TWO VPCs instead of one.

In this step I am going to:
1. Set up multiple VPCs
2. Create a VPC peering connection
3. Test VPC peering with connectivity tests.

<img width="1338" height="1462" alt="image" src="https://github.com/user-attachments/assets/cbe022fd-671c-4fc6-9ae5-86c967518b1c" /><br/>
 

I am leaving the initial VPC I created "Jakes-VPC-1". But in this VPC, there is only going to be 1 Public Subnet and 0 Private Subnets. I am deleting the private configs in the private subnet. The items I deleted to delete my Private Subnet:  
- Private Subnet
- Private Route Table
- Private Server
- Private Security Group
- Private NACL

##### Creating my Second VPC
- creating a VPC called "Jakes Private VPC" (second VPC) with a CIDR block of 10.1.0.0/16.
- The Public VPC (first VPC) is currently 10.0.0.0/16
 Each VPC must have a unique IPv4 CIDR block so the IP addresses of their resources don't overlap.

Creating the Second VPC is just like creating the first, essentially the same configuration except for the CIDR block.


#### Create a Peering Connection
In this step I am:
1. Setting up a connection link between my VPCs

<img width="1408" height="1504" alt="image" src="https://github.com/user-attachments/assets/7056a31b-1d08-460b-9156-4482c668a791" /><br/>  

The VPC peering connection allows for direct connection between two VPCs using their private IP addresses. This means data can now be transferred between VPCs without going through the public internet.

In public route table I'm creating a new route that directs traffic bound for VPC2 to my peering connection.  
Here is my VPC Peering connection, with VPC1 and the requestor, and VPC2 as the acceptor. 

<img width="2520" height="1188" alt="image" src="https://github.com/user-attachments/assets/fa14e1dd-fe28-4c70-969e-3e86347d5dd0" /><br/>


#### Update Route Tables
In this step Im going to:
1. Set up a way for traffic coming from VPC1 to get to VPC2
2. Set up a way for traffic coming from VPC2 to get to VPC1

In VPC1's route table, I add a new route which has the following Destinaton and Target:  
- Destination: 10.1.0.0/16 (VPC2's CIDR)
- Target: my VPC Peering resource.

Repeat for VPC2 and adjust the destination CIDR 

#### Test Connection between Instances
To use instance connect, my Instance needs an assigned IPv4 address.  
- for VPC1 I assigned an Elastic IP address to my instance, this allows me to use Instance connect to test connectivity.
- To connect to instance 2 in VPC2, I need to adjust my Security Group's inbound rules. To account for the traffic type, I enabled ICMP IPv4 for Pings. In the security group I stated that VPC1's CIDR was the source of the traffic, even though its going through the VPC peering connection.
- Now, though the VPC1's Instance Connect, I can ping the VPC2 server by using the command: ping "instance2's Private IP address".



<img width="938" height="522" alt="image" src="https://github.com/user-attachments/assets/8cd47a5e-0cbc-4594-ae62-d3bc3d876ad7" />












