Preface
----


This repository contains solutions for an AWS university assignment. The main task is to deploy a simple web app with an autoscaling group and load balancer and codify the solution using IAC. 

Please check out the updated branch of this repository to view an improved version of the Cloudformation template. 


Questions 
----
The job is to create a web infrastructure in AWS that deploys using AWS CloudFormation. The chosen solution should then be deployed with a CI/CD pipeline.

Requirements:
1. Web application has to serve up the simple "Hello world" Flask App (https://pythonspot.com/flask-web-app-with-python/)
2. Infrastructure must be load balanced and auto-scaled. Scaling can be on whatever metric you choose. (I need an explanation of the chosen metric)
3. Web page must be served over HTTPS and HTTP must be blocked. We will accept using a self-signed certificate for this, so you don't have to register domains, etc.
4. The infrastructure as code solution must support 2 "environments" (i.e., staging and production) with different properties for each environment (you can choose which properties are variable per environment). (I need an explanation of the chosen properties)
5. An AWS CloudWatch alarm must alert (via email) when CPU use goes over a threshold.

Tip
• All of this can be deployed for free using the AWS free tier

Delivery:
1. Provide clear instructions to deploy the solution along with a clear readme.
2. List any assumptions you have made.
3. Describe how you would automate deploying code updates to this environment. What would a CI/CD pipeline look like for this? How would you guarantee that no application downtime would occur during the update?

Answers
------
Below is a list of answers to this assignment. 

### Introduction
We are running the web app on AWS Fargate. AWS Fargate is a managed service that allows you to quickly run containerized workloads in the cloud. Developers only have to upload their container to the service and aws will take care of maintaining the underlying server. (security updates, patching, and so on).

  
### Instructions
 1. Create a default vpc    

 `aws ec2 create-default-vpc`

2. Create at least 2 subnets in different availability zones. AWS Fargate needs at least 2 subnets in different AZ's to run. 

`aws ec2 create-default-subnet --availability-zone us-east-1a --dry-run`
 

`aws ec2 create-default-subnet --availability-zone us-east-2a` 

4. You will need to validate your email address before deploying the Cloudformation template. 

`aws ses verify-email-identity --email-address sender@example.com
`
 5. Create a Self-signed certificate from this article and copy the ACM ARN. The ACM ARN should like this, "arn:aws:iam::746109777700:server-certificate/CSC" We need to use this self-signed certificate to attach it to our load balancer for SSL support. 
"
  https://medium.com/@francisyzy/create-aws-elb-with-self-signed-ssl-cert-cd1c352331f
 
.
       


#### Requirments Answers:
1. I chose CPU utilization as my metric for auto-scaling because a higher CPU load is indicative of a higher load. 
2. I chose to change the min and max containers to autoscale between Dev and prod environments.  You don't need many containers when you are running a dev/test environment. 


### Assumptions

1) That we are allowed to use ECR(Elastic Container Registry) to store our docker containers. We cannot define this resource in a cloud formation template. This is just infrastructure that must be created manually.
2) That we are allowed to manually install a self-signed certificate. This is the only way to create a self-signed certificate. We cannot define this resource in a cloudformation template.
3) That we can pre-configure our environment with networking BEFORE deploying the Cloudformation template
4) "The chosen solution should then be deployed with a CI/CD pipeline" Nowhere in the assignment does it mention that I must define a CI/CD and codify a CI/CD pipeline. ## Requirments
This is the reason why I chose to run the Flask App on Fargate instead of 


4. The infrastructure will support 2 "environments" 


#### Describe How I would automate deploying code updates to this environment? 

There are many ways to automate deploying code updates to this environment. For starters, I assume that code updates are meant for the Flask application, and code updated to the AWS Cloudformation template itself. 

I would use AWS Codepipeline to update the application, this is an AWS service that encompasses 3 services; (Code commit, code deploy, and code build). 

#### How would a ci/cd pipeline look for this? 
Because the code is packaged inside a Docker container, I would first:
1. Push the local code out to a Github repository
2. Have Github Webhooks enabled so that Codepipeline knows when new code has been checked into the Repository. 
3. Pull the code out from the Github Repo, into AWS. 
4. Run AWS Codebuild on that code, to build a docker image. 
5. And then push Docker image into an ECR repository and create a new AWS environment to test the newly built code. 

##### How would I guarantee no application downtime would occur during the update?
I would use a method called Blue/Green Deployments. Blue/green deployments are essentially 2 environments. The production environment that you don't ever want goes down, and the NewProduction environment, which contains updated code.

You want to slowly move people from your production environment over to your NewProduction environment. And then finally decommission the old product environment. 

There are a few ways to accomplish this, but mainly by manipulating DNS. 









