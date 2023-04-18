# Amazon SNS

This guide contains information to set up a SNS Source in Vanus Cloud.

## Introduction

Amazon Simple Notification Service (Amazon SNS) is a fully-managed messaging service provided by Amazon Web Services (AWS).

It enables you to send and receive messages between distributed systems or microservices, mobile devices, and other AWS services.

Vanus Cloud provides the Amazon SNS Source connector, which retrieves SNS events and converts them into CloudEvents.

## Prerequisites

Before obtaining SNS events, you must have:

- A [Vanus Cloud account](https://cloud.vanus.ai)
- Have an AWS SNS Topic.
- AWS IAM Access Key.
- AWS permissions for the IAM user:
  - sns:Subscribe
  - sns:ConfirmSubscription
  - sns:Unsubscribe

## Getting Started

### Step 1: Create a New AWS User

1. Log in to the AWS [Management Console](https://aws.amazon.com) using your root account credentials.
2. Navigate to the [IAM](https://console.aws.amazon.com/iam/) service by clicking on the Services menu and selecting IAM.
   ![](images/findIAM.png)
3. Click on the **Users tab** in the left navigation menu, and then click the **Add user** button.
   ![](images/AddUser.png)
4. Write the name for your user and click **next**.
5. Select **attach policy directly**, and **Create policy**.
   ![](images/permissionoption.png)
6. Select the Service `Cost Explore Service` and search for the following policy.
    - "PutObject",
    - "GetObject",
    - "GetObjectVersion",
    - "DeleteObject",
    - "DeleteObjectVersion"
   
7. Press **next** and proceed to the next page, name your policy and click **create policy**.
8. Return back to your previous `TAB`.
9. Search for your custom policy and add it to your account, and press **next**.
   ![img.png](images/policy.png)
10. Review and press **create user**.

### Step 2: Create an Create Access Key

1. Now click on the user you just created.
   ![img.png](images/user.png)
2. Under **Security and credential** scroll down the page to `Access Key`, and Click **Create access key**.
   ![](images/createAccesskey.png)
3. Select Command line interface CLI, and press **next**.
   ![img.png](images/CLI.png)
4. Save your access key and secret key safely.
   ![](images/img.png)

### Step 3: AWS SNS 
1. Go to the [Amazon SNS Console](https://console.aws.amazon.com/sns/)
2. 

### Step 4: Config your Connection

1. Log in to your [Vanus Dashboard](https://cloud.vanus.ai/dashboard).
2. Click on the **create connection** button under connections.
3. From the list of sources, choose **Amazon SNS**
4. Enter the following credentials:

   - Port
   - Access Key ID
   - Secret Access Key
   - SNS ARN
   - Endpoint
   - Protocol

     ![img.png](images/vanus-sns.png)

5. To get **SNS ARN**, **Endpoint** and **Protocol**, let's go back to our Amazon Web Services Account
6. Search for SQS, Click on it and Create a Queue.
7. Select type as FIFO and provide a name. Scroll down and click the **Create queue** Button. This will give you the Endpoint. The Protocol is **SQS**.
   ![img.png](images/sqs-fifo.png)
8. To obtain the **SNS ARN**, Search for **SNS** and Click on it to open the SNS Console.
9. Click on the **Create Topic** Button, Select the type as **FIFO** and name the Topic. Scroll down and click the **Create topic** button to create a Topic.
10. Copy the **SNS ARN** and paste on the field in Vanus Cloud.
11. Click next to continue.

Learn more about Vanus and Vanus Cloud in our [documentation](https://docs.vanus.ai).
