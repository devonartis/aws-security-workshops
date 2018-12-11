# Permission boundaries round

Hello fellow cloud consultant. Your customer has deployed a three tier web application in production. Different teams work on different aspects of the architecture but they don't always communicate well nor work well together. Just recently the team responsible for the web servers set up a Lambda function that inadvertently impacted the Application team's resources. The VP of Operations was furious. The VP has tasked you with setting up permissions for the web admins so that they can only impact they own resources while still being able to do their job. 

**AWS Service/Feature Coverage**: 

* AWS IAM [permission boundaries](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html) 
* AWS IAM [identifiers or resource restrictions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html)
* AWS IAM [users & roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)
* AWS [Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
 
## Agenda

The round is broken down into first a [**BUILD**](./build.md) phase followed by a [**VERIFY**](./verify.md) phase. 

**BUILD** (60 min): First each team will carry out the activities involved in the **BUILD** phase where they will set up access for the web admins and properly lock down the account. Then each team will hand credentials for an IAM user in their account to another team to act in the **VERIFY** phase. The **VERIFY** phase lasts about 30 min.

**VERIFY** (30 min): Each team will carry out the **VERIFY** activities as if they were part of the web admins team. The **VERIFY** activities will include validating that the requirements were set up correctly in the **BUILD** phase and also investigate if the web admins are able to take actions that they shouldn't be allowed to.

!!! info "Team or Individual Exercise"
	This workshop can be done as a team exercise or individually. The instructions are written with assumption that you are working as part of a team but you could just as easily do the steps below on your own. If done as part of an AWS sponsored event then you'll be split into teams of around 4-6 people. Each team will do the **BUILD** phase and then hand off their accounts to another team. Then each team will do the **VERIFY** phase. 

Using this workshop as an example, the three elements of a permission boundary are represented below. When your team does the **BUILD** tasks you will act as the admin. When your team does the **VERIFY** tasks you will act as the delegated admin. The delegated admins will create roles that can be considered **"bound"** since they will have permission boundaries attached.  

![mechanism](./images/permission-boundaries.png)

<!--### Point system
There is a point system for both the **BUILD** and **VERIFY**  activities. Each team also starts out with a number of points they can exchange for hints for various sections. 

Points earned during **VERIFY** Phase:

* 5 points for each requirement fulfilled by the team in the **BUILD** phase 
* 15 points for every major exploit found (components of an individual exploit cannot be combined, e.g. a public bucket that allows Read, Write and List is one exploit. 
-->

## Presentation

If you are doing this workshop as part of an AWS event then there will usually be a 30 minute presentation before the hands on work. Here is the [presentation deck](./presentation.pdf).

## Setup Instructions

To setup your environment please expand one of the following dropdowns (depending on how if you are doing this workshop at an **AWS event** or **individually**) and follow the instructions: 

??? info "AWS Sponsored Event"

	**Console Login:** if you are attending this workshop at an official AWS event then your team should have the URL and login credentials for your account. This will allow you to login to the account using AWS SSO. Browse to that URL and login. 

	After you login click **AWS Account** box, then click on the Account ID displayed below that (the red box in the image.) You should see a link below that for **Management console**. Click on that and you will be taken the AWS console. 

	![login-page](./images/login.png)

	Make sure the region is set to Ohio (us-east-2)

	**CloudFormation:** Launch the CloudFormation stack below to setup the environment:

	Region| Deploy
	------|-----
	US East 2 (Ohio) | [![Deploy permission boundary round in us-east-2](./images/deploy-to-aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=Perm-Bound&templateURL=https://s3-us-west-2.amazonaws.com/sa-security-specialist-workshops-us-west-2/identity-workshop/permissionboundary/identity-workshop-web-admins.yaml)

	1. Click the **Deploy to AWS** button above.  This will automatically take you to the console to run the template.  
	2. Click **Next** on the **Select Template** section.
	3. Click **Next** on the **Specify Details** section (the stack name will be prefilled - you can change it or leave it as is)
	4. Click **Next** on the **Options** section.
	5. Finally, acknowledge that the template will create IAM roles under **Capabilitie** and click **Create**.

	This will bring you back to the CloudFormation console. You can refresh the page to see the stack starting to create. Before moving on, make sure the stack is in a **CREATE_COMPLETE**.

??? info "Individual"

	Log in to your account however you would normally

	**CloudFormation:** Launch the CloudFormation stack below to setup the environment:

	Region| Deploy
	------|-----
	US East 2 (Ohio) | [![Deploy permission boundary round in us-east-2](./images/deploy-to-aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=Perm-Bound&templateURL=https://s3-us-west-2.amazonaws.com/sa-security-specialist-workshops-us-west-2/identity-workshop/permissionboundary/identity-workshop-web-admins.yaml)

	1. Click the **Deploy to AWS** button above.  This will automatically take you to the console to run the template.  
	2. Click **Next** on the **Select Template** section.
	3. Click **Next** on the **Specify Details** section (the stack name will be prefilled - you can change it or leave it as is)
	4. Click **Next** on the **Options** section.
	5. Finally, acknowledge that the template will create IAM roles under **Capabilitie** and click **Create**.

	This will bring you back to the CloudFormation console. You can refresh the page to see the stack starting to create. Before moving on, make sure the stack is in a **CREATE_COMPLETE**.

## Requirements

<!--
This is an old AWS account though and it is possible there are permission holes lurking around that need to be addressed. Many applications are already locked down via [Organization SCP's](http://) - you do not have visibility or access though into the SCP's in effect for this account. 
--->

??? info "Click here for the account architecture"

	Account architecture: ![architecture](./images/architecture.png)

There are many teams working in this AWS account, including the web admins and the application admins.  The ulimate goal of this workshop is to set up the web admins so they can create a Lambda function to read an S3 bucket while making sure they are not able to impact the resources of other teams. The web admins should only have access to the following resources:

1. IAM policies and roles created by the web admins 
2. Lambda functions created by the web admins
3. S3 buckets: The web admins are allowed to have read access to the bucket that starts with "identitywksp-web-admins-applicat-rs3elbaccesslogs-" and full access to the bucket that starts with "

The web admins should not be able to impact any resources in the account that they do not own including IAM users, groups, roles, S3 buckets, etc.

#### [Click here to go to the BUILD phase](./build.md)