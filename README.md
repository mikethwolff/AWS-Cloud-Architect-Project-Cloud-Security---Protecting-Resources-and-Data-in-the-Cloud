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

**Part 1: Review Architecture Diagram**

In this task, the objective is to familiarize yourself with the starting architecture diagram. An architecture diagram has been provided which reflects the resources that will be deployed in your AWS account.

Insecure Webservice:

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/AWS-WebServiceDiagram-v1-insecure-1.png)

Expected user flow:
- Clients will invoke a public-facing web service to pull free recipes.
- The web service is hosted by an HTTP load balancer listening on port 80.
- The web service is forwarding requests to the web application instance which listens on port 5000.
- The web application instance will, in turn, use the public-facing AWS API to pull recipe files from the S3 bucket hosting free recipes. An IAM role and policy will provide the web app instance permissions required to access objects in the S3 bucket.
- Another S3 bucket is used as a vault to store secret recipes; there are privileged users who would need access to this bucket. The web application server does not need access to this bucket.

Attack flow:
- Scripts simulating an attack will be run from a separate instance which is in an un-trusted subnet.
- The scripts will attempt to break into the web application instance using the public IP and attempt to access data in the secret recipe S3 bucket.

**Part 2: Review CloudFormation Template**

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

**Part 3: Deployment of Initial Infrastructure**

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

**Part 4: Identify Bad Practices**

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

**Task 2: Enable Security Monitoring**

Deliverables for Exercise 2:

- E2T2_config.png - Screenshot of AWS Config showing non-compliant rules.
- E2T2_inspector.png - Screenshot of AWS Inspector showing scan results.
- E2T2_securityhub.png - Screenshot of AWS Security Hub showing compliance standards for CIS foundations.
- E2T2.txt - Provide recommendations on how to remediate the vulnerabilities.
 
**Part 1: Enable Security Monitoring using AWS Native Tools**

First, we will set up security monitoring to ensure that the AWS account and environment configuration is in compliance with the CIS standards for cloud security.

**Part 2: Identify and Triage Vulnerabilities**

Please submit screenshots of:

- AWS Config - showing non-compliant rules
- AWS Inspector - showing scan results
- AWS Security Hub - showing compliance standards for CIS foundations.
- Name the files E2T2_config.png, E2T2_inspector.png, E2T2_securityhub.png respectively.
- Research and analyze which of the vulnerabilities appear to be related to the code that was deployed for the environment in this project. Provide recommendations on how to remediate the vulnerabilities. Submit your findings in E2T2.txt

**Solution**

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E2T2_config.png)

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E2T2_inspector.png)

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E2T2_securityhub.png)

E2T2.txt
```
Research and analyze which of the vulnerabilities appear to be related to the code that was deployed for the environment in this project.

Bonus - provide recommendations on how to remediate the vulnerabilities.

Vulnerabilities:

CIS.4.1     Ensure no security groups allow ingress from 0.0.0.0/0 to port 22
CIS.2.9	Ensure VPC flow logging is enabled in all VPCs

TCP port 20 which is associated with 'FTP' is reachable from the internet
TCP port 21 which is associated with 'FTP' is reachable from the internet
UDP port 23 which is associated with 'Telnet' is reachable from the internet
TCP port 23 which is associated with 'Telnet' is reachable from the internet
UDP port 21 which is associated with 'FTP' is reachable from the internet
UDP port 20 which is associated with 'FTP' is reachable from the internet ...

S3.4 S3 buckets should have server-side encryption enabled
S3.1 S3 Block Public Access setting should be enabled

EC2 EBS default encryption should be enabled
EC2 instances should not have a public IPv4 address

Recommendations:

Provide specific IP address range for security groups ingress on port 22.
Enable VPC flow logging.

You can edit the Security Groups to remove access from the internet on port ...

Enable default encryption on S3 buckets.

You can use the Amazon EC2 console to enable default encryption for Amazon EBS volumes.
Use a non-default VPC so that your instance is not assigned a public IP address by default.
```

**Task 3 - Attack Simulation**

Now you will run scripts that will simulate the following attack conditions: Making an SSH connection to the application server using brute force password cracking. Capturing secret recipe files from the s3 bucket using stolen API keys.

**Part 1: Brute force attack to exploit SSH ports facing the internet and an insecure configuration on the server**

1. Log into the attack simulation server using your SSH key-pair.
2. Start a brute force attack against the application server. You will need the application server hostname for this.

**Part 2: Accessing Secret Recipe Data File from S3**

Imagine a scenario where API keys used by the application server to read data from S3 were discovered and stolen by the brute force attack. This provides the attack instance the same API privileges as the application instance. We can test this scenario by attempting to use the API to read data from the secrets S3 bucket.

1. Make sure you're still logged into the attack instance and run the following API calls to view and download files from the secret recipes S3 bucket. You will need the name of the S3 bucket for this. Take a screenshot showing the breach: E3T2_s3breach.png

**Solution**
![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E3T2_s3breach.png)

**Task 4 - Implement Security Hardening**

**Part 1 - Remediation plan**

As a Cloud Architect, you have been asked to apply security best practices to the environment so that it can withstand attacks and be more secure.

Identify 2-3 changes that can be made to our environment to prevent an SSH brute force attack from the internet.
Neither instance should have had access to the secret recipes bucket; even in the instance that API credentials were compromised how could we have prevented access to sensitive data?

**Solution**
E4T1.txt
```
# Identify 2-3 changes that can be made to our environment to prevent an ssh brute force attack from the internet.

Use GuardDuty to identify brute force attacks.
Use AWS WAF to protect resources from common attacks.
Remove ssh login with password - set ssh config PasswordAuthentication to "no"
Take the least-privilege approach and only open the ports needed for communication. Ensure that the operating system is hardened and that unnecessary tools and permissive configurations are disabled.

# Neither instance should have had access to the secret recipes bucket, in the even that instance API credentials were compromised how could we have prevented access to sensitive data.

Specify the users that can access specific buckets and objects (user policies).
Monitor S3 resources (CloudTrail logs).
Use encryption on S3 bucket objects.
```

**Task 2 - Hardening**

Remove SSH Vulnerability on the Application Instance
1. To disable SSH password login on the application server instance.
2. Test that this made a difference. Run the brute force attack again from Exercise 3, Task 1.
3. Take a screenshot of the terminal window where you ran the attack highlighting the remediation and name it E4T2_sshbruteforce.png.

**Solution**

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E4T2_sshbruteforce.png)

Apply Network Controls to Restrict Application Server Traffic
1. Update the security group which is assigned to the web application instance. Any method you use to do this is acceptable. The requirement is that we only allow connections to port 5000 from the public subnet where the application load balancer resides.
2. Test that the change worked by attempting to make an SSH connection to the web application instance using its public URL.
3. Submit a screenshot of the security group change and your SSH attempt.

**Solution**

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E4T2_networksg.png)

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E4T2_sshattempt.png)

Least Privilege Access to S3
1. Update the IAM policy for the instance profile role used by the web application instance to only allow read access to the free recipes S3 bucket.
2. Test the change by using the attack instance to attempt to copy the secret recipes.
3. Submit a screenshot of the updated IAM policy and the attempt to copy the files.

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E4T2_s3iampolicy.png)

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E4T2_s3copy.png)

Apply Default Server-side Encryption to the S3 Bucket
This will cause the S3 service to encrypt any objects that are stored going forward by default. Use the below guide to enable this on both S3 buckets.
Amazon S3 Default Encryption for S3 Buckets

Capture the screenshot of the secret recipes bucket showing that default encryption has been enabled.

**Solution**

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E4T2_s3encryption.png)

**Part 3: Check Monitoring Tools to see if the Changes that were made have Reduced the Number of Findings**

1. Go to AWS inspector and run the inspector scan that was run in Exercise 2.
2. After 20-30 mins - check Security Hub to see if the finding count reduced.
3. Check AWS Config rules to see if any of the rules are now in compliance.
4. Submit screenshots of Inspector, Security Hub, and AWS Config titled E4T3_inspector.png, E4T3_securityhub.png, and E4T3_config.png respectively.


**Solution**

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E4T3_securityhub.png)

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E4T3_config.png)

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/E4T3_inspector.png)

**Part 4: Questions and Analysis**

1. What additional architectural change can be made to reduce the internet-facing attack surface of the web application instance.
2. Assuming the IAM permissions for the S3 bucket are still insecure, would creating VPC private endpoints for S3 prevent the unauthorized access to the secrets bucket.
3. Will applying default encryption setting to the s3 buckets encrypt the data that already exists?
4. The changes you made above were done through the console or CLI; describe the outcome if the original cloud formation templates are applied to this environment?
5. Submit your answers in E4T4.txt.

E4T4.txt
```

# What additional architectural change can be made to reduce the internet facing attack surface of the web application instance.

An AWS web application firewall can block possible attack vectors to instances.
Remove external access to instances and login to web application instances via AWS Systems Manager.


# Assuming the IAM permissions for the S3 bucket are still insecure, would creating VPC private endpoints for S3 prevent the unauthorized access to the secrets bucket.

No, creating VPC private endpoint would not prevent the unauthorized access to the secrets bucket if the IAM permissions are still insecure. An optional layer of security for your VPC would be an ACL, that acts as a firewall for controlling traffic 


# Will applying default encryption setting to the s3 buckets encrypt the data that already exists?

No, AWS encrypts an object during the upload, using server-side encryption and encrypts the object before it is saved to disk. Already existing objects will not be affected by this modification.
You can encrypt existing Amazon S3 objects with the AWS CLI. To encrypt existing objects in place, you can use the Copy Object or Copy Part API. This copies the objects with the same name and encrypts the object data using server-side encryption.


# What would happen if the original cloud formation templates are applied to this environment.

If we apply the original cloud formation templates, we would go back to were we started. Usually it is not best practise to make changes to stack resources outside of CloudFormation which create a mismatch between your stack's template and the current state of your stack resources, as we have it now, which will cause errors if you update or delete the stack. Also you want to be able to reproduce the stack. If you want to try changes before adding it to the template, these changes should be added to the template afterwards, organized by lifecycle and ownership.
```

**Task 5 - Designing a DevSecOps Pipeline**

Take a look at a very common deployment pipeline diagrammed below:

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/devopspipeline.png)

The high-level steps are as follows:

1. The user makes a change to the application code or OS configuration for a service.
2. Once the change is committed to source, a build is kicked off resulting in an AMI or a container image.
3. The infrastructure as code is updated with the new AMI or container image to use.
4. Changes to cloud configuration or infrastructure as code may have also been committed.
5. A deployment to the environment ensues applying the changes

**Part 1: Design a DevSecOps pipeline**
Update the starter DevOpsPipeline.ppt (or create your own diagram using a different tool) At minimum you will include steps for:

- Infrastructure as code compliance scanning.
- AMI or container image scanning.
- Post-deployment compliance scanning.

Submit your design as a ppt or png image named DevSecOpsPipeline.[ppt or png].

**Solution**

![alt text](https://github.com/mikethwolff/AWS-Cloud-Architect-Project-Cloud-Security-Protecting-Resources-and-Data-in-the-Cloud/blob/main/Cloud%20Security%20-%20Protecting%20Resources%20and%20Data%20in%20the%20Cloud/DevSecOpsPipline.png)

**Part 2 - Tools and Documentation**

You will need to determine appropriate tools to incorporate into the pipeline to ensure that security vulnerabilities are found.

1. Identify tools that will allow you to do the following: a. Scan infrastructure as code templates. b. Scan AMI’s or containers for OS vulnerabilities. c. Scan an AWS environment for cloud configuration vulnerabilities.
2. For each tool - identify an example compliance violation or vulnerability which it might expose.

Submit your answers in E5T2.txt

E5T2.txt
```
# Identify tools that will allow you to do the following:

## Scan infrastructure as code templates

Chef InSpec is an open-source framework for testing and auditing your applications and infrastructure.

Terrascan is a static code analyzer for Infrastructure as Code. Key features: 500+ Policies for security best practices.

### Example vulnerability

Chef InSpec: MFA should be enabled.

Terrascan: Ingress open to 0.0.0.0/0


##Scan AMI’s or containers for OS vulnerabilities

Trivy is a simple and comprehensive scanner for vulnerabilities in container images, file systems, and Git repositories, as well as for configuration issues. Trivy detects vulnerabilities of OS packages.

### Example vulnerability

Trivy: https://nvd.nist.gov/


##Scan an AWS environment for cloud configuration vulnerabilities

Amazon GuardDuty is a threat detection service that continuously monitors for malicious activity and unauthorized behavior to protect your AWS accounts.

Amazon Inspector is an automated security assessment service that helps improve the security and compliance of applications deployed on AWS.

### Example vulnerability

Amazon GuardDuty: SSH brute force attack on S3 bucket.

Amazon Inspector: Instance i-06af4f55a1ea4f137 is configured to allow users to log in with root credentials over SSH, without having to use a command authenticated by a public key. This increases the likelihood of a successful brute-force attack.
```

