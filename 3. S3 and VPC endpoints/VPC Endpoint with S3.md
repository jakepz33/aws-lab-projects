## VPC Endpoints

VPC endpoints gives your VPC private, direct access to other AWS services like S3, so traffic doesn't need to go through the internet.  
Just like how internet gateways are like your VPC's door to the internet, you can think of VPC endpoints as private doors to specific AWS services.

In this project I am going to:  
1. Use VPC endpoints to directly access my S3 bucket from my VPC
2. Test my endpoint setup using an S3 security tool called bucket policies
<img width="2150" height="1258" alt="image" src="https://github.com/user-attachments/assets/e7a88625-553d-42f3-a4ec-8c6f6c4198aa" /><br/>

I am starting off with my VPC and EC2 instances already provisioned.  
But I created an S3 bucket with the following two objects uploaded:  
<img width="2416" height="603" alt="image" src="https://github.com/user-attachments/assets/3eabbd5f-41af-4832-946a-a06277867c32" /><br/>

#### Testing connection to my S3 bucket
- From within the EC2 instance connect terminal, I ran the command "aws s3 ls" to list the items in my S3 bucket. I got an error that I need to configure credentials by running "aws configure".

**do I have credentials?**  
- Think of EC2 instance as another user that needs its own username and password (i.e credentials) to get access to your AWS account. By default, my account doesn't have crednetials set up for running commands from an EC2 instances. I'll need to manually set these up to securely access and manage AWS services from my instance.

**Things Ill need for aws configure:**  
- AWS Access Key: credentials for my apps and other servers to log into AWS to talk to my AWS services
- 

#### Creating Access Keys
In this step, I am going:   
1. Give Access to my EC2 instance to my AWS environment

- I am setting up access keys for my IAM admin account, selecting Command Line Interface (CLI)
<img width="717" height="774" alt="image" src="https://github.com/user-attachments/assets/c93b381b-916c-4b8e-9935-408fcff23097" /><br/>

Now that I created access keys, Im going to connect to my S3 bucket.

#### Connect to my S3 bucket
Now, in my S3 terminal I can connect using the access keys credentials downloaded from the previous step.  
- When I run "aws s3 ls" I can see my S3 buckets.
<img width="709" height="100" alt="image" src="https://github.com/user-attachments/assets/1a361341-0682-4b29-b1d8-204e4b4073cd" /><br/>

- Now that this is confirmed, Im going to create an endpoint.

#### Create an endpoint
I was able to access my S3 bucket, but this was through the public internet. This isn't the most secure way to communicate - external threats and attacks can easily intercept my commands and get access to my AWS environment or sensitive data.

In this step I am going to:  
1. Set up a way for my VPC and S3 communicate directly

<img width="2054" height="1204" alt="image" src="https://github.com/user-attachments/assets/c0f125d6-cace-46fa-b03f-54b80d689bb1" /><br/>

- In my VPC console, create an endpoint
<img width="2513" height="1394" alt="image" src="https://github.com/user-attachments/assets/19c0505f-8e55-4fc3-8d64-504df6c3da1e" /><br/>
> A Gateway endpoint is used specifically for Amazon S3 and DynamoDB

#### Creating a super secure bucket policy
In this step, I am going to:
1. Limit my S3 bucket's access to only traffic from my endpoint

- Set up the bucket policy with the following info:
<img width="426" height="426" alt="image" src="https://github.com/user-attachments/assets/c28fafde-01b4-48d8-8ef8-ef35e5b56c04" /><br/>
> once I save these changes, my admin account is blocked from viewing my S3 bucket's information. This will set us up in the next step to confirm that only through my VPC, I can access the S3 resources.




#### Accessing my S3 bucket
In this step I am going to:
1. Test my VPC endpoint set up.

When I run the command "aws s3 ls s3://nextwork-vpc-endpoints-yourname" I am still able to view the items in my bucket. This is because our Route Table added the VPC endpoint to its routes.


Project Complete!











