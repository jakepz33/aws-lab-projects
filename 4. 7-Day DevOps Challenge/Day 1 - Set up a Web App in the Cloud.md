# Set Up a Web App in the Cloud

In this project I will:
- Launch an EC2 instance
- Use VS Code to set up a remote SSH connection to my EC2 instance
- Install Maven and Javan and generate a basic web app
- Edit code without VS Code


#### Step 1 Set up an IAM User
- I already have a dedicated IAM user. If I didn't I would set up an IAM user with Admin rights.

#### Step 2 Launch an EC2 Instance
Here I will:
- Launch a new EC2 instance.
- Set up a key pair for secure access.
- Set up network settings for your instance

I launched an instance and picked AWS Free tier resources such as t3.micro Amazon AMI and for networking, I allowed SSH policy from my local IPv4 address.


#### Step 3 Install VS Code
I am going to use VS Code to connect to my instance so I can create and edit my Web App's code.
In this step I am going to:  
- Set up a terminal in VS Code, so I can communicate with my EC2 instance
- Update my key pair's permission settings, so I can use it to log into my EC2 instance later.


I opened my terminal and navigated to my directy on my Laptop: OneDrive\Desktop\DevOps.  

##### Change the permissions of my .pem file
I ran this in my terminal to alllow access to my .pem file.  
icacls "jakes-key-pair.pem" /reset
icacls "jakes-key-pair.pem" /grant:r "jpere:R"
icacls "jakes-key-pair.pem" /inheritance:r


#### Step 4 Connect to my EC2 Instance
- Head back to your AWS Management Console.
- Click on Instances from the left hand navigation panel.
- Click on the checkbox next to your EC2 instance to view its details.
- Under the Details tab, look for Public IPv4 DNS. You'll need this in the next instruction!
- Note your DNS here: ec2-44-245-205-213.us-west-2.compute.amazonaws.com

Now Ill connect to my instance via SSH using the following command:  

ssh -i C:\Users\jpere\OneDrive\Desktop\DevOps\jakes-key-pair.pem ec2-user@ec2-44-245-205-213.us-west-2.compute.amazonaws.com  

**What does 'fingerprint' in the terminal message mean?**  
- it is a unique code that helps verify that the device or connection you are using is secure and hasn't been tampered with. Since it was the first time connecting to this EC2 instance, SSH doesn't have the server's fingerprint stored on my machine yet.

<img width="516" height="61" alt="image" src="https://github.com/user-attachments/assets/8000d0fa-f280-41dd-b397-c2efd2a31ca6" /><br/>  

#### Step 5 Install Apache Maven and Amazon Corretto 8
My terminal has now entered into my EC2 instance and I can use it like a computer thats right in front of me.  
In this step I will:  
- Install Apache Maven on my EC2 instance.
- Install Amazon Corretto 8, a version of Java
- Verify the installations

**What is Apache Maven?**
It is a tool that helps devs build and organize Java Software projects. It's also a package manager which means it automatically downloads any external pieces of code your project depends on to work.  

Run this command first: sudo yum install wget -y

then,  

Install Apache Maven using these commands:  
wget https://archive.apache.org/dist/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz

sudo tar -xzf apache-maven-3.5.2-bin.tar.gz -C /opt

echo "export PATH=/opt/apache-maven-3.5.2/bin:$PATH" >> ~/.bashrc

source ~/.bashrc


##### Now install Java 8  
sudo dnf install -y java-1.8.0-amazon-corretto-devel

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64

export PATH=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64/jre/bin/:$PATH


#### Step 6 Create the Application
- Run Maven commands in your terminal to generate a Java Web App

mvn archetype:generate \
   -DgroupId=com.nextwork.app \
   -DartifactId=nextwork-web-project \
   -DarchetypeArtifactId=maven-archetype-webapp \
   -DinteractiveMode=false


#### Step 7 Connect VS Code with your EC2 Instance
In this step I will:
- Install extension in VS Code
- Use the extension to set up a connection between VS Code and my EC2 Instance.
- Explore and edit Java Web app's files using VS Code.

In the VS Code search bar, type Remote - SSH and click Install for the extension.

💡 Why are we installing Remote - SSH?  
The Remote - SSH extension in VS Code lets you connect directly via SSH to another computer securely over the internet. This lets you use VS Code to work on files or run programs on that server as if you were doing it on your own computer, which will come in handy when we edit the web app in your EC2 instance!  

Click on the double arrow icon at the bottom left corner of your VS Code window. This button is a shortcut to use Remote - SSH.  


Select Connect to Host...



Select + Add New SSH Host...  

Enter the SSH command you used to connect to your EC2 instance:


ssh -i C:\Users\jpere\OneDrive\Desktop\DevOps\jakes-key-pair.pem ec2-user@ec2-44-245-205-213.us-west-2.compute.amazonaws.com

Select the configuration file at the top of your window. It should look similar to /Users/jpere/.ssh/config  


Host was added and modified my config file:  
<img width="1424" height="394" alt="image" src="https://github.com/user-attachments/assets/21c39d9d-3b2f-4f9c-8794-a1461796afea" /><br/>  
Click on the double arrow button on the bottom left corner and select Connect to Host again.

You should now see your EC2 instance listed at the top.
<img width="2832" height="862" alt="image" src="https://github.com/user-attachments/assets/ecdc6d3c-6c98-4926-8e9a-e46c2517d209" /><br/>  
<img width="596" height="120" alt="image" src="https://github.com/user-attachments/assets/88afc652-6869-4820-bb3e-0262b3a74cba" /><br/>  

**IMPORTANT**  
<img width="1220" height="659" alt="image" src="https://github.com/user-attachments/assets/020559e1-d3e3-4848-a4d0-0c6a0c2d3491" /><br/>  

Now I can open up my webapp's files.
- from VS Code's left hand navigation bar, select Explorer icon.
- Select Open Folder
- At the top of VS Code window, I see a drop down of different folder names. I enter /home/ec2-user/nextwork-web-project and press OK
- I can now see my folder structure in my directory.
<img width="564" height="509" alt="image" src="https://github.com/user-attachments/assets/50ce5f44-bd69-48dd-b5de-db4d7155bdc2" /><br/>

That'll do it for Day 1!



