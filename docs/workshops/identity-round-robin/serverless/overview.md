# Serverless Round

Welcome to the world of serverless!  Now you may be asking yourself, *What is serverless*? Well, it is an architecture paradigm that allows you to create your applications without provisioning or managing any servers.  Sounds great, right?  Organizations look at building serverless applications as a way of improving their scalability and reducing their operational overhead.  The responsibility of the underlying infrastructure is shifted off your plate so you can spend more time focusing on building your applications.

So with less infrastructure to manage you are no longer responsible for patching  your operating systems and the attack surface you need to worry about has been significantly reduced.  But with the use of serverless technologies comes *great/other* responsibility.  When you hear the word serverless you may think specifically of [AWS Lambda](https://aws.amazon.com/lambda/) but it is important to remember that there are other services used within a serverless application and securing an application involves more than just securing your Lambda functions.  

In this round you will be focused on improving the identity controls of the WildRydes serverless application (which is borrowed from [aws-serverless-workshops](https://github.com/aws-samples/aws-serverless-workshops/tree/master/WebApplication) and retrofitted for the purposes of this round).  You will get exposed to different identity concepts through the use of a variety of services such as [Amazon S3](https://aws.amazon.com/s3/), [Amazon CloudFront](https://aws.amazon.com/cloudfront/), and [Amazon Cognito](https://aws.amazon.com/cognito/).  Upon completion you should have a better idea of how to use native AWS identity controls to improve the security posture of a serverless application.

**AWS Service/Feature Coverage**: 

* S3 Bucket Policies
* S3 ACLs
* CloudFront Origin Access Identities
* Cognito User Pools
* Cognito Hosted UI

## Agenda

This round is broken down into a BUILD & VERIFY phase. 

* **BUILD** (60 min): The Build phase involves evaluating, implementing, and enhancing the identity controls of the WildRydes application based on a set of business level functional and non-functional requirements.
* **VERIFY** (30 min):  The Verify phase involves putting on the hat of an end user and testing the controls you put in place to ensure the requirements were met. In addition you will also ensure that a systems administrator is still able to manage the resources.

> This workshop can be done as a team exercise or individually. The instructions are written with the assumption that you are working as part of a team but you could just as easily do the steps below individually. If done as part of an AWS sponsored event then you'll be split into teams of around 4-6 people. Each team will do the BUILD phase and then hand off their accounts to another team. Then each team will do the VERIFY phase.

## Environment Setup

To setup your environment please expand one of the following dropdowns (depending on how you're doing this workshop) and follow the instructions: 

<details>
<summary>AWS Sponsored Event</summary>

* Browse to the URL provided to you and login. 

* After you login click the **AWS Account** box, then click on the Account ID displayed below that (the red box in the image.) You should see a link below that for the **Management console**. Click on that and you will be taken to the AWS console. 

![login-page](./images/login.png)
</details>

<details>
<summary>Individual</summary>
Launch the CloudFormation stack below to setup the WildRydes application:

Region| Deploy
------|-----
US East 1 (N. Virginia) | [![Deploy in us-east-1](./images/deploy-to-aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=Identity-RR-Wksp-Serverless-Round&templateURL=https://s3-us-west-2.amazonaws.com/sa-security-specialist-workshops-us-west-2/identity-workshop/serverless/serverless-round.yml)

1. Click the **Deploy to AWS** button above (right click and open in a new tab).  This will automatically take you to the console to run the template.  

2. Click **Next** on the **Specify Template** section.

3. On the **Specify Details** step, add a **validation AWS Account** and then click **Next**. 
 
	> This will be the account that validates the controls put in place during the BUILD phase.  If you are doing both phases in a single AWS account just put the account number for the account you are currently using.
4. Click **Next** on the **Options** section.
4. Finally, acknowledge that the template will create IAM roles under **Capabilities and click **Create**.

This will bring you back to the CloudFormation console. You can refresh the page to see the stack starting to create. Before moving on, make sure the stack is in a **CREATE_COMPLETE**.
</details>

## Scenario - WildRydes Identity Overhaul

You just joined a new DevOps team who manages a suite of animal-based ride sharing applications.  Given your security background you've been embedded on the team to take the lead on security related tasks, evangelize security best practices, and represent your team when interacting with your security organization.  Recently, your team inherited a new application; WildRydes.

## View your new application
1. Open the [CloudFormation console](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filter=active) (us-east-1)
2. Click on the **Identity-RR-Wksp-Serverless-Round** stack.
3. Click on **Outputs** and click on **WebsiteCloudFrontURL**.

As part of the hand off to your team, the product team shared their vision for the application and stated that future iterations will include more dynamic features.  After doing an evaluation of the architecture you determined that the WildRydes application is a static website hosted in an S3 bucket.  There is a CloudFront Distribution setup to be used as a content delivery network and a Cognito User Pool for user management.

### Current Application Architecture

![Architecture](./images/architecture-start.png)

After thoroughly evaluating the architecture and doing a threat modeling exercise your team has identified a number of broken features and misconfigurations.  It looks as though someone started putting in place certain security controls but were not able to fully implement them. These reviews resulted in the creation of a couple tasks that were added to the backlog for your team and given a high priority.

***

### **[Build Phase](./serverless-round-build.md)**