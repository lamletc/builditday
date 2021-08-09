+++
title = "Create AWS Control Tower"
date = 2020-04-18T00:38:32+07:00
weight = 3
chapter = true
pre = "<b>3. </b>"
+++

### Create AWS Control Tower

1. Log in to the AWS Management Console of the account where you plan to deploy AWS Control Tower. Access to [**AWS Control Tower console**](https://console.aws.amazon.com/controltower/).

![Control Tower Dashboard](/images/3/1.png?width=90pc)

2. On the Setting up Control Tower Console, make sure you are in one of the **10 supported regions (N. Virginia, Ohio, Oregon, Central, Sydney, Singapore, Frankfurt, Ireland, London, or Stockholm)**.

![Choose right region](/images/3/2.png?width=90pc)

3. On **Shared accounts**, **Management account** is the account which you configure Control Tower by default. You need to provide 2 real emails for 2 AWS Accounts **Log Archive** and **Audit**

![Provide email for 2 AWS Accounts](/images/3/3.png?width=90pc)

4. Reading **Service permissions**, click to the box **I understand the permissions AWS Control Tower will use to administer AWS resource and enforce rules on my behalf. I also understand the guidance on the use of  AWS Control Tower and the underlying AWS resources**. Then choose **Set up landing zone** 

![Set up Landing Zone](/images/3/4.png?width=90pc)

5. Setting up Landing Zone will take nearly 60 minutes.

![Set up Landing Zone](/images/3/5.png?width=90pc)

6. During setting up Landing Zone, you wil be received email from AWS to 2 emails which is configured above. You need to click to **Confirm subscription** to accept this invite.

![Email confirmation](/images/3/6.png?width=90pc)

7. After creating AWS Control Tower, you can see on Control Tower Console that you have **3 accounts (Master, Log Archive, Audit)** which is **enrolled**

![Enrolled AWS Account](/images/3/7.png?width=90pc)
