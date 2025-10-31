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
* Create an S3 bucket and name it uniquely.
<img width="2808" height="1309" alt="image" src="https://github.com/user-attachments/assets/be73308f-a6a3-498c-8b12-51e505094bb4" />

  * Upload website files. I am uploading an Index.html, style.css and script.js file.  
<img width="2740" height="904" alt="image" src="https://github.com/user-attachments/assets/69aa8618-cc27-44a2-aca7-0fb2233f73d3" />






