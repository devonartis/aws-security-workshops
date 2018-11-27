# SEC 405: Scalable, Automated Anomaly Detection with Amazon GuardDuty and SageMaker

In this README you will find instructions and pointers to the resources used for the workshop. This workshop contains the following exercises:

1. Examining GuardDuty findings
2. IP-based anomaly detection in SageMaker

After the setup steps below, there are instructions provided for all of the hands-on exercises, instructions of how to delete the CloudFormation stack, and following that a full walkthrough guide on how to complete the exercises.

## What's in here?

This repository contains the following files that will be used for this workshop:

- templates/
  - cloudformation.yaml - The CloudFormation template to deploy the stack of resources for the workshop
- aws_lambda/
  - cloudtrail_ingest.zip - Lambda zip bundle for workshop CloudTrail log ingest
  - guardduty_ingest.zip - Lambda zip bundle for workshop GuardDuty finding ingest
- cleanup.sh - Shell script to delete the workshop CloudFormation stack at the end

# Initial setup

## Prerequisites

Before getting started, you will need the following:

- AWS account
- Modern, graphical web browser (sorry Lynx users!)
- IAM user with administrator access to the account

## Deploying the CloudFormation template

The CloudFormation template creates the following:

- 2 Lambda functions
  - CloudTrail log file ingester
  - GuardDuty finding ingester
- IAM role used by the Lambda functions
- S3 bucket used for outputting (principal ID, IP address) tuples

First, log in to your AWS account using the IAM user with administrator access.

For this workshop, we will be working within the Canada Central (ca-central-1) region. To switch regions, click the region dropdown in the top right of the window and select **Canada (Central)**.

To easily deploy the CloudFormation stack in the Canada (Central) region, please browse to the following stack launch URL:

https://console.aws.amazon.com/cloudformation/home?region=ca-central-1#/stacks/new?stackName=SEC405&templateURL=https://s3.ca-central-1.amazonaws.com/aws-reinvent2018-sec405-de42b9ca/cloudformation.yaml

The stack launch URL uses a copy of the CloudFormation template from *templates/cloudformation.yaml* that is contained in an S3 bucket and is the same as the one in this code repository.

1. On the **Select Template** page, note that the template location where it says "Specify an Amazon S3 template URL" is prepopulated with the S3 URL to the template. Click **Next**.
2. On the **Specify Details** page, the stack name is prepopulated as "SEC405", but you may change it if you wish. Click **Next**.
3. On the **Options** screen, click **Next**.
4. On the Review page, check the box for “I acknowledge that AWS CloudFormation might create IAM resources” since the template creates an IAM role.
5. Click **Create** to deploy the stack. While the CloudFormation stack is being created, you can view its status in the AWS CloudFormation console. You should see a green **Status** of **CREATE_COMPLETE** in just a few minutes.

While waiting for the CloudFormation stack to complete, you may proceed to Exercise 1.

# Exercise 1: Examining GuardDuty findings

In this exercise, you will generate and examine sample GuardDuty findings to understand what information they contain, and then also look at several "real" GuardDuty findings that were generated in advance from actual AWS account activity.

The goal of this exercise is to familiarize with the kinds of information contained in GuardDuty findings, and the structure of the findings themselves..

## 1.1 Generate sample findings

1. From the **Services** dropdown in the top left, browse to the GuardDuty console. Double check that you are in the **Canada (Central)** region via the region dropdown in the top right, and switch to it if you are not.
2. If GuardDuty is not yet enabled, click the button labelled **Enable GuardDuty** to turn it on with a single click.
3. In the left menu click **Settings**, scroll down to the section titled "Sample Findings", and then click on the button labelled **Generate sample findings** to generate a sample GuardDuty finding for every finding type.
4. Click on **Findings** in the left menu and then examine some of the findings shown in the GuardDuty console. What kinds of information do you see?
5. Examine some of the  findings with a threat purpose of "UnauthorizedAccess".

## 1.2 Examining real findings

The "real" GuardDuty findings that were generated for this workshop are contained in an S3 bucket. Rather than read them directly, we're going to run the AWS Lambda ingester function for the GuardDuty findings that will read in the findings from the S3 bucket and print them out.

1. Browse to the AWS Lambda console, again ensuring you're in the **Canada (Central)** region, and then click on the Lambda function whose name starts with "SEC405-GuardDutyIngestLambda".
2. Scroll down to view the code for the Lambda function in the inline, browser-based editor. Skim through the code to familiarize with what it does.
3. Click the **Test** button to run the function. You will need to create a test event to do this, but the event actually does not matter in this case, so just use the "Hello World" event template and give it a name "SEC405", then click **Create**. You then need to click the **Test** button once more.
4. Examine the output, where you'll see the JSON for each GuardDuty finding being printed by the function `print_full_finding`. Look over the findings to see what information they contain.
5. A function called `print_short_finding` is also defined to print out a shortened, one-line version of each GuardDuty finding. Replace the call to the function `print_full_finding` with `print_short_finding` (hint: Search for "TODO" around line 135. You will see multiple TODOs, but only the first one applies here.).
6. Click the **Save** button at the top of the screen to save your changes to the function, then click **Test** to run it again. Observe the new output, where you will now see a summarized verison of each finding being printed.

# Exercise 2: IP-based anomaly detection in SageMaker

In this exercise, you will use the IP Insights SageMaker machine learning algorithm to learn how unusual GuardDuty findings are for given principals (i.e., IAM users or roles) based on IP address.

First, you will generate training data consisting of `<principal ID, IP address>` tuples to train the model by using CloudTrail logs that have been generated for this workshop. You will also generate scoring data tuples by using the GuardDuty findings from Exercise 1 that are based on the same set of activity as the CloudTrail logs.

## 2.1 Generate training data using CloudTrail logs

In order to use the IP Insights model, we need some training data. We will train the model by passing `<principal ID, IP address>` tuples extracted from CloudTrail logs.
    
An AWS Lambda function has been created to do this, but you'll need to make a small change to the function and then run it to generate the tuples.

1. Browse to the AWS Lambda console and click on the Lambda function whose name starts with "SEC405-CloudTrailIngestLambda".
2. Scroll down to view the code for the Lambda function in the inline, browser-based editor. Skim through the code to familiarize with what it does.
3. Click the **Test** button to run the function. You will need to create a test event to do this, but the event actually does not matter in this case, so just use the "Hello World" event template and give it a name "SEC405", then click **Create**. You then need to click the **Test** button once more.
4. Look at the output of the function, where you'll see a short version of each CloudTrail record returned by the function `print_short_record` being printed.
5. A function `get_tuple` has been provided to take a CloudTrail record as input and return a `<principal ID, IP address>` tuple for each record. A call to this function has already been set up in the `handler` function, but it is commented out (hint: search for the string "TODO"). Uncomment it.
6. Click the **Save** button at the top to save your function changes.
7. Click the **Test** button to run the function again. This time it will write the tuples to the S3 bucket where they can be loaded into the IP Insights algorithm for training the model.

In the S3 console, if you find the bucket whose name starts with "sec405-tuplesbucket", you should now see a file cloudtrail_tuples.txt inside that contains some `<principal ID, IP address>` tuples.

## 2.2 Generate scoring data using GuardDuty findings

To make use of the trained model, we will pass `<principal ID, IP address>` tuples extracted from the GuardDuty findings to it for scoring. The activity contained in these GuardDuty findings directly corresponds to the activity contained in the CloudTrail logs.
    
An AWS Lambda function has been created to do this, but you'll need to make a small change to the function and then run it to generate the tuples.

1. Browse to the AWS Lambda console and click on the Lambda function whose name starts with "SEC405-GuardDutyIngestLambda".
2. A function `extract_tuples` has been provided to take GuardDuty findings as input and return `<principal ID, IP address>` tuples for each finding. A call to this function has already been set up in the `handler` function (search for the string "TODO"), but it is commented out. Uncomment it.
3. Click the **Save** button at the top to save your function changes.
4. Click the **Test** button to run the function again. This time it write the tuples to the S3 bucket where they can be loaded into the IP Insights algorithm for scoring.

In the S3 console, if you find the bucket whose name starts with "sec405-tuplesbucket", you should now see a file guardduty_tuples.txt inside that contains some `<principal ID, IP address>` tuples.

## 2.3 Set up the SageMaker notebook

1. First, go to the S3 console and look for the bucket whose name starts with "sec405-tuplesbucket" (e.g., sec405-tuplesbucket-1fnqifqbmsfxy). Copy the name of this bucket; you will need it in a moment.
2. Browse to the Amazon SageMaker console and click on the button called **Create notebook instance**.
3. On the next screen, give the notebook a name "SEC405".
4. For Notebook instance type, we recommend selecting ml.m4.xlarge.
5. For IAM role, choose "Create a new role" in the dropdown. On the next dialog, ensure "S3 buckets you specify" is selected, in the text field for "Specific S3 buckets" paste the name of the S3 bucket from step 1, and click **Create role**.
6. All other notebook options can be left at defaults. Click **Create notebook instance**.
7. Once the notebook is running, click **Open Jupyter** to open the notebook.
7. In the notebook, choose the **SageMaker Examples** tab to see a list of all the Amazon SageMaker examples, expand the **Introduction to Amazon Algorithms** section, look for **ipinsights-tutorial.ipynb**, click its **Use** button and then **Create copy** in the dialog.

## 2.4 Learn about the IP Insights algorithm

IP Insights is an unsupervised learning algorithm for detecting anomalous behavior and usage patterns of IP addresses, that helps users identifying fraudulent behavior using IP addresses, describe the Amazon SageMaker IP Insights algorithm, demonstrate how you can use it in a real-world application, and share some of our results using it internally.

For more information about the IP Insights algorithm, please read the following AWS blog post:

https://aws.amazon.com/blogs/machine-learning/detect-suspicious-ip-addresses-with-the-amazon-sagemaker-ip-insights-algorithm/].

You can also view the IP Insghts documentation here:

https://docs.aws.amazon.com/sagemaker/latest/dg/ip-insights.html

Work through the IP Insights tutorial notebook to understand how it works. Once ready, train the model using the cloudtrail\_tuples.txt file from the "sec405-tuplesbucket", then score the GuardDuty findings by using the file guardduty\_tuples.txt from the same S3 bucket.

What does the output of the algorithm look like? How can we interpret the score?

# Cleaning up

In order to prevent charges to your account from the resources created during this workshop, we recommend cleaning up the infrastructure that was created by deleting the CloudFormation stack. You can leave things running if you want to examine the lab a bit more, and can do the clean-up steps below at any time.

### Automated clean-up

To delete the CloudFormation stack, a Bash script, `cleanup.sh`, is provided in this repository. Download it and then run as follows:

```
chmod +x cleanup.sh
./cleanup.sh
```

- *Q: I'm using a different AWS CLI profile than the default.*
- A: The script supports a flag to specify a CLI profile that is configured in your `~/.aws/config` file. Do `./cleanup.sh -p PROFILE_NAME`. To see all supported options for the clean-up script, do `./cleanup.sh -h`.

To delete the SageMaker notebook, on the **Notebook instances** page in the SageMaker console, click the circle to select the notebook then under **Actions** choose **Stop**. Once the notebook is stopped, under **Actions** choose **Delete**.

### Manual clean-up

1. First you will need to delete the S3 bucket whose name starts with "sec405-tuplesbucket" or else deleting the CloudFormation stack will fail since the bucket has objects in it.
2. Delete the CloudFormation stack by going to the CloudFormation console, selecting the stack called **SEC405**, and from the top menu choosing action **Delete Stack**.
3. To delete the SageMaker notebook, on the **Notebook instances** page in the SageMaker console, click the circle to select the notebook then under **Actions** choose **Stop**. Once the notebook is stopped, under **Actions** choose **Delete**.
