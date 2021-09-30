# AWS Cloud Architect Projects Udacity

*The listed projects were part of my AWS Cloud Architect Nanodegree and are displayed in derivated form due to possible copyright restrictions.*

*Form and requirements of my projects may differ from present projects. Udacity may vary projects and this repo is representing my findings at this point in time and may not be representative for future  AWS Cloud Architect projects. If you are currently enrolled into Udacity's AWS Cloud Architect Nanodegree please keep in mind that my findings can help you as a guide but copying these findings will go against Udacity's Honor Code.*

# Project 3: AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud

In this project, you will:

- Deploy and assess a simple web application environment’s security posture
- Test the security of the environment by simulating attack scenarios and exploiting cloud configuration vulnerabilities
- Implement monitoring to identify insecure configurations and malicious activity
- Apply methods learned in the course to harden and secure the environment
- Design a DevSecOps pipeline

**Task 1 - Deploy Project Environment**

Deliverables for Exercise 1: E1T4.txt - Text file identifying 2 poor security practices with justification.

Part 1: Review Architecture Diagram
In this task, the objective is to familiarize yourself with the starting architecture diagram. An architecture diagram has been provided which reflects the resources that will be deployed in your AWS account.

Insecure Webservice:

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/AWS-WebServiceDiagram-v1-insecure-1.png)

Expected user flow:
- Clients will invoke a public-facing web service to pull free recipes.
- The web service is hosted by an HTTP load balancer listening on port 80.
- The web service is forwarding requests to the web application instance which listens on port 5000.
- The web application instance will, in turn, use the public-facing AWS API to pull recipe files from the S3 bucket hosting free recipes. An IAM role and policy will provide the web app instance permissions required to access objects in the S3 bucket.
- Another S3 bucket is used as a vault to store secret recipes; there are privileged users who would need access to this bucket. The web application server does not need access to this bucket.
- 
Attack flow:
- Scripts simulating an attack will be run from a separate instance which is in an un-trusted subnet.
- The scripts will attempt to break into the web application instance using the public IP and attempt to access data in the secret recipe S3 bucket.

**Task 2: Review CloudFormation Template**

In this task, the objective is to familiarize yourself with the starter code and to get you up and running quickly. Spend a few minutes going through the .yml files in the starter folder to get a feel for how parts of the code will map to the components in the architecture diagram.

Additionally, we have provided a CloudFormation template which will deploy the following resources in AWS:

VPC Stack for the underlying network:
- A VPC with 2 public subnets, one private subnet, and internet gateways etc for internet access.
S3 bucket stack:
- 2 S3 buckets that will contain data objects for the application.
Application stack:
- An EC2 instance that will act as an external attacker from which we will test the ability of our environment to handle threats
- An EC2 instance that will be running a simple web service.
- Application LoadBalancer
- Security groups
- IAM role

**Task 3: Deployment of Initial Infrastructure**

In this task, the objective is to deploy the CloudFormation stacks that will create the below environment.

1. We will utilize the AWS CLI in this guide, however, you are welcome to use the AWS console to deploy the CloudFormation templates.

- Make sure that your IDE (preferably VS Code) is integrated with AWS.
- From the root directory of the repository
- Deploy the S3 buckets
- Deploy the VPC and Subnets
- Deploy the Application Stack
- You will need to specify a pre-existing key-pair name. If you don't have a pre-existing key-pair, create a new one in the AWS Console. 

Note: Your key's region must exist in the same region that you plan to deploy your stack.

2. Once you see Status is CREATE_COMPLETE for all 3 stacks, obtain the required parameters needed for the project.

- Obtain the name of the S3 bucket by navigating to the Outputs section of the stack

Note down the names of the two other buckets that have been created, one for free recipes and one for secret recipes. You will need the bucket names to upload example recipe data to the buckets and to run the attack scripts.

- You will need the Application Load Balancer endpoint to test the web service - ApplicationURL
- You will need the web application EC2 instance public IP address to simulate the attack - ApplicationInstanceIP
- You will need the public IP address of the attack instance from which to run the attack scripts - AttackInstanceIP
- You can get these from the Outputs section of the c3-app stack.

3. Upload data to S3 buckets
- Upload the free recipes to the free recipe S3 bucket from step 2. Do this by typing this command into the console (you will replace "BucketNameRecipesFree" with your bucket name)
- Upload the secret recipes to the secret recipe S3 bucket from step 2. Do this by typing this command into the console (you will replace "BucketNameRecipesSecret" with your bucket name)

4. Test the application
Invoke the web service using the application load balancer URL:

```
http://<ApplicationURL>/free_recipe
```
You should receive a recipe for banana bread.

The AMIs specified in the cloud formation template exist in the us-east-1 (N. Virginia) region. You will need to set this as your default region when deploying resources for this project.

**Task 4: Identify Bad Practices**
Based on the architecture diagram, and the steps you have taken so far to upload data and access the application web service, identify at least 2 obvious poor practices as it relates to security. List these 2 practices, and a justification for your choices, in the text file named E1T4.txt.

Deliverables:

E1T4.txt - Text file identifying 2 poor security practices with justification.

**Solution:**
  
E1T4.txt
```
Based on the architecture diagram, and the steps you have taken so far to upload data and access the application web service, identify at least 2 obvious poor practices as it relates to security.  Include justification.

# Poor practice 1

The WebAppSG security group inboud rules are unecessary:

TCP port 80
SSH port 22
All traffic at all ports

We can delete these inbound rules and apply the principle of least privilege. 


# Poor practice 2

The current instance role policy is allowing access from the application server to all S3 buckets, this should be restricted to specific defined buckets only (free receipe).

#Poor practice 2

The security groups' inbound and outbound rules set CIDR to 0.0.0.0/0. which allow unrestricted access to any TCP/UDP ports and should have been restricted access to only those IP addresses that require it in order to implement the principle of least privilege and reduce the possibility of a breach.
```

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Design-Provision-and-Monitor-AWS-Infrastructure-at-Scale/blob/main/Design%2C%20Provision%20and%20Monito%20AWS%20Infrastructure%20at%20Scale/Udacity_Diagram_1.jpg)
