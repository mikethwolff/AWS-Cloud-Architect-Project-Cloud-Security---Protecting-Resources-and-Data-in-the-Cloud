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

AWS-WebServiceDiagram-v1-insecure.png

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
