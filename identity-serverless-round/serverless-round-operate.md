# Serverless Round

At this point you should have received both a CloudFront and S3 URL from the DevOps team.  Now that the additional identity controls have been added to the application, you have been tasked with acting as an end user and manually testing to verify that the controls have been put in place correctly and the requirements have been met.

## Operate Phase

### Task 1

*Ensure the application serves content out through the CloudFront Distribution and that your end users can only access the application through CloudFront URLs and not Amazon S3 URLs. As part of this configuration your end users should not be able to affect the availability or integrity of the application.*

#### Verification Checklist:

* You can access the site through the CloudFront Distrubtion.

* You are restricted from accessing any of the application resources through S3 URLs.

* You can not delete or modify any of the application resources.

	> Try using cURL or Postman to make requests with different HTTP verbs (e.g. Delete)

<details open>
<summary>Security Levels</summary>

**Level 100**

  * End users should only be able to access index.html through the CloudFront URL
  * Integrity and Availability
  
**Level 200**

  * User should not be able to access any file through an S3 URL
  * Integrity and Availability

**Level 400**

  * User should not be able any file through an S3 URL
  * Administrators should still be able to make all API calls to S3
  * Integrity and Availability
  
</details>

> Which level did they achieve for this task?

### Task 2

*Set up user management for the application using Cognito User pools.  To reduce the operational overhead of creating and maintaining forms and custom logic for authentication, the decision has been made to use the Cognito hosted-UI to integrate the application with the User Pool.*

*As part of the user experience users should be able to sign themselves up, they should have to validate their email address, and be required to create a password that meets the password complexity requirements for applications set in your security standards.*

#### Verification Checklist: 

* You are taken to the hosted UI when clicking on **Giddy Up**.

* You are able to sign your self up for the site.

* You are required to create a password with the following complexity:
	* *Minimum length of 10 characters*
	* *Must include symbols*
	* *Must include numbers*
	* *Must include uppercase characters*
	
* You are required to verify your email address.

* After authentication, you are redirected to ride.html and are presented with your JWT IdToken.

<details open>
<summary>Security Levels</summary>

**Level 200**

  * Giddy Up Link takes you to the hosted UI
  
**Level 250**

  * Giddy Up Link takes you to the hosted UI
  * Self-service signup
  * Email Verification
  * Password Complexity

**Level 350**

  * Giddy Up Link takes you to the hosted UI
  * Self-service signup
  * Password Complexity
  * User directed to ride.html and sees a their JWT ID Token

**Level 550**

  * Giddy Up Link takes you to the hosted UI
  * Self-service signup
  * Password Complexity
  * User directed to ride.html and sees a their JWT ID Token
  * Sign-out deactivates and removes stored JWT tokens
  
</details>

> Which level did they achieve for this task?

## Final Architecture

![Architecture](./images/architecture-final.png)

## Cleanup

In order to prevent charges to your account we recommend cleaning up the infrastructure that was created, especially if you are doing other Identity rounds. 

> You will need to manually delete some resources before you delete the CloudFormation stacks so please do the following steps in order.

1.	Delete the Amazon Cognito Domain for the hosted-UI.
	* Go to the [Amazon Cognito](https://console.aws.amazon.com/cognito/users/?region=us-east-1) console.
	* On the left navigation under **App Integration**, click on **Domain Name**.
	* Click **Delete**
	* Click the acknowledgement checkbox and click **Delete**

2.	Delete the CloudFormation stack (**Identity-RR-Wksp-Serverless-Round**).
	* Go to the [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filter=active) console.
	* Select the appropiate stack.
	* Select **Action**.
	* Click **Delete Stack**.

***

Congratulations on completeing the Serverless Round!



