# Building a Three-Tier Web App
This project is showcasing building a web app in AWS, using Three-tier architecture. Three-tier architecture is a way to organize web applications. Making it easier to manage and scale.

The three layers consist of the:
1. Presentation Tier: what the user sees and interacts with
2. Logic Tier: the brains of your app that processes data
3. Data Tier: the layer where data is stored in a database.

Services used in this build:

**Presentation**: S3 bucket, CloudFront Distribution  
**Logic Tier**: API Gateway, Lambda Function  
**Data Tier**: DyanmoDB database

✅ Create a storage bucket for your website's files with S3.  
✅ Distribute content globally with CloudFront.  
✅ Build Brains of your application using serverless functions with Lamdba.  
✅ Store and retrieve user data with DynamoDB.  
✅ Connect all these services together for three-tier architecture.  
<img width="509" height="374" alt="image" src="https://github.com/user-attachments/assets/65448b81-1ce3-49ff-af20-7b33a421388b" />

## 1. Setting Up the Presentation Layer
This layer is responsible for displaying the website to our users.
Here is where we:  
- Create an S3 bucket to store our website's files.
- Upload a simple index.html file to your bucket.
- Set up CloudFront to deliver your website's content globally.

#### Create an S3 bucket
S3 is like a massive scalable hard drive in the cloud.
* Log in to the AWS Management Console as your Admin User. (use admin user for security best practices. You never want to use your Root account if you don't have to)
* Create an S3 bucket and name it uniquely. Im creating a general purpose bucket, in us-west-2 AZ
<img width="2808" height="1309" alt="image" src="https://github.com/user-attachments/assets/be73308f-a6a3-498c-8b12-51e505094bb4" />

  * Upload website files. I am uploading an Index.html, style.css and script.js file.  
<img width="2740" height="904" alt="image" src="https://github.com/user-attachments/assets/69aa8618-cc27-44a2-aca7-0fb2233f73d3" />

#### Create a CloudFront Distribution
* What is Amazon CloudFront?  
  - CloudFront is a Content Delivery Network (CDN), which means it will spee up the distribution of our static and dynamic web content such as html, css, js and image files
* How does CloudFront speed up distributions?
   - By using caching. Caching is the process of storing copies of files in a cache, i.e. a temporary storage location so they can be accessed more quickly.
   - CloudFront has servers in many locations around the world. When a user requests content from your website, CloudFront checks if it has a copy of the content in a server near the user. If it does, it delivers the content from that server.
 
 * Create a CloudFront distribution. Here I am going to set specify the origin as my S3 bucket.
<img width="2286" height="1231" alt="image" src="https://github.com/user-attachments/assets/ff211cbe-5875-46ab-8848-08f26efca653" />

> The S3 bucket is where my CloudFront distribution is pointing to, to get the content we want to cache. The Origin Access Control (OAC) we created makes sure only CloudFront can access the files stored in the S3 bucket. But by default, the S3 bucket keeps our objects private to everyone including the OAC. We still need to update the S3 bucket to give OAC permissions to our bucket's contents. Otherwise, CloudFront doesn't have access to our website files.  

> I went back into my CloudFront distribution went *Edit Orgin*.
> 1. I copied the Origin Access Control Policy that was generated in CloudFront to allow access to my Origin Access Control in the S3 bucket. I attached this policy to my S3 bucket policy.
<img width="2235" height="1335" alt="image" src="https://github.com/user-attachments/assets/f121b972-700a-4bf6-8ea8-47a31973e3be" />


* Update our S3 bucket's bucket policy. Here is the updated JSON policy.  
<img width="1409" height="870" alt="image" src="https://github.com/user-attachments/assets/b9d1e0c3-e7aa-4b7a-9430-8aa1d110856b" />


✅ I tested access to my S3 bucket by searching my CloudFront domain in a new tab. An important step to make sure you are pointing to your home index.html file is to make is the Default Root Object: See Distribution Domain Name, and Default root object fields below

<img width="2302" height="1101" alt="image" src="https://github.com/user-attachments/assets/3da02076-ce53-4b1f-810e-dc767109b4c3" />
<br/>
<br/>
This webpage loaded:
<img width="933" height="315" alt="image" src="https://github.com/user-attachments/assets/cbe1b3b2-698f-4c72-bd3c-3c4b83af640e" />

📃 Summary:  
- Accessed my management console using an IAM Admin account.
- In my desired region, us-west-2, I created an S3 bucket and uploaded my front-end files: html, css and js.
- I created a CloudFront distribution -- with my Origin being the S3 bucket I created. I created an OAC (origin access control) which allows my S3 bucket to remain private and only allow access to my CloudFront.
- I copied the policy generated by CloudFront, then pasted/updated that policy into my S3 bucket policy.
- I edited the Default root object in my CloudFront to be my index.html file, so when the URL loads it automically loads my html file.

## 2. Set up the Logic Tier
This tier is responsible for handling the brains of the application, such as fetching data from a database and performing calculations.

In this project, our logic will be a Lambda Function that retrieves user data from a DynamoDB table. We need to expose that funtionality to the outside world, so we'll use API Gateway to handle requests and route them to the right place.

In this step i'm going to:
💻Create a Lambda function to fetch data from a DynamoDB table.  
0️⃣ Write the code for our Lambda function.  
📲 Create an API Gateway REST API.  
🤝 Create a resource and method to handle GET requests.  
📩 Deploy the API to make it accessible.  

#### Create a Lambda function
This function will fetch data from a datbase an return it to the user. 

**What is AWS Lambda?**  
it is a service that lets you run code without needing to manage any computers/servers - Lambda will manage them for you. Lambda only runs code when you need it to, so no paying for idle time. It also scales automatically.

- creating a Lambda function call RetrieveUserData and writing the Lambda code:
<img width="689" height="766" alt="image" src="https://github.com/user-attachments/assets/530bab08-015e-4de1-b277-7fa89019ce1e" />


**What does this code do?**  
This code sets up a Lambd function that retrieves data from a DynamoDB table. It looks for specific user data based on a *userID* and returns that data. There is code inclided for error handling. Its also using AWS SDK to access libraries within AWS with our code, in this case we're accessing the DynamoDB library.

#### Configure API Gateway
Now that I have my Lambda function ready, I need a way to access it. This is where API Gateway comes in.

**What is the relationship between APIs and Lambda? Why do we need an API Gateway?**  
API Gateway acts as the "front door" to our Lambda function. It receives requests and then forwards them to Lambda functions for processing.  
We need an API Gateway because directly exposing Lambda functions to user requests isn't best practice, Lambda doesn't have built in security or API management features. API GW bring authentication and authorization features, and advanced API management capabilities that make our app more efficient.


- Im creating a new REST API called *UserRequestAPI* in the API Gateway console.
   - A REST API uses HTTP methods to interact with resources. Its popular because it's simple and can be used with virtually any programming language.
 <img width="2772" height="1386" alt="image" src="https://github.com/user-attachments/assets/87a47d49-7d1b-4a27-a84f-69882d375478" />
 <br/>

 #### Create resource for our API Gateway
 **What are API resources?**  
 - they are individual endpoints within my API that handle different parts of its functionality. A resource path is the URL path used to access that resource, e.g. /messages for retrieving messages and /users for retrieving user profiles.

 <img width="2850" height="738" alt="image" src="https://github.com/user-attachments/assets/6b4dacce-8238-41cd-bd0a-bebac6a1dbc0" />
 <br/>

#### Set up an API Method
- Im Creating a GET method that will reference our Lambda function with "Lambda Proxy Integration" on:
<img width="2811" height="1398" alt="image" src="https://github.com/user-attachments/assets/189c382e-8e2b-4975-8aa4-cef5187a17b4" />

- Set up is done and now I create my API Method:
<img width="2248" height="1181" alt="image" src="https://github.com/user-attachments/assets/86bc651d-e44d-45b0-bd4d-cb023498153d" />

#### Deploy your API
Deploying the API makes it accessible through a public endpoint, so I can actually start using it. I deployed to my Prod stage.
<img width="2278" height="994" alt="image" src="https://github.com/user-attachments/assets/fd9f494f-be64-46e4-ab00-8c39a3d2c2ce" />

- When I try visting my API by using the Invoke URL I get the following error message, this is because I don't have dynamoDB configured yet:
<img width="2438" height="383" alt="image" src="https://github.com/user-attachments/assets/d459c76e-506f-4ec7-81fc-8d3f8e000a7f" />

##3. Set Up the Data Tier
Now I'll have a DynamoDB that I need to create and the Lambda function I created earlier to retrieve data. I will need a way to expose that functionality to the outside world. That's where API Gateway comes in.

API Gateway acts as a front door for our Lambda function, handling requests and routing them to the right place.

#### Create a DynamoDB table
- Select **Create Table**
- For Table Name, enter *UserData*
- For Partition Key, enter userId - then create Table
<img width="2270" height="491" alt="image" src="https://github.com/user-attachments/assets/444ec11d-70d5-47ea-a2eb-38ca9b440f9d" />

#### Adding a Table Item
Here I am going to add a sample user to my UserData table.  

- Selecting my UserData Table, I then click on "Explore Table items" button.
- At the "Items returned" panel, I select "Create item".
- I go the JSON view and toggle off "View DynamoDB JSON". And paste in my desired JSON data. Then I create the item.
<img width="2639" height="823" alt="image" src="https://github.com/user-attachments/assets/1cc30f3e-c125-4ea4-9bd2-7ab7572d8650" />
<br/>
- I now see it in the **Items returned** tab
<img width="1656" height="604" alt="image" src="https://github.com/user-attachments/assets/eb34a6f6-df1c-46f9-a38e-dd241009e673" />
<b/>

#### Grant DynamoDB access to Lambda
- I'm going to update my Lambda function's permission policies to add AmazonDynamoDBReadOnlyAccess
- In the Configuration Tab of my Lambda function, Ill select Permissions --> Select Role name hyperlink
<img width="1847" height="948" alt="image" src="https://github.com/user-attachments/assets/a53cbbc3-2fef-4763-ae46-0761b923d4c3" />
<br/>

- This will take me to the IAM console with my Lambda function open.
- I select **Add permissions** --> **Attach policies** --> Select AmazonDynamoDBReadOnlyAccess
<img width="2787" height="954" alt="image" src="https://github.com/user-attachments/assets/ad4d24fb-5aa2-41cc-94b8-b59a681c10d1" />
<br/>












 

 
























