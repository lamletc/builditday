+++
title = "AWS Single Sign-On"
date = 2020-04-18T00:38:32+07:00
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

**Contents**
- [Create AWS Single Sign-On](#create-aws-single-sign-on)
- [Result](#result)

#### Create AWS Single Sign-On

{{% notice note %}}
Implementing Single Sign-On give you a hand to log in to each AWS Account easier that mean 1 SSO User can access to multi-AWS Account with different policies. 
{{% /notice %}}

1. Log in to the AWS Management Console of the account where you plan to deploy AWS Control Tower. Access to **AWS Single Sign-On console** at https://console.aws.amazon.com/singlesignon/

![AWS SSO Console](/images/5/sso-1.PNG?width=90pc)

2. On the left panel, click **Users**; choose the User which have created on the Control Tower step. 
![AWS SSO Console](/images/5/sso-2.PNG?width=90pc)

3. Then, click to **Send email verification link**. You need to access to email which you choose for this SSO User, you will see the email confirmation SSO User Creation
![AWS SSO Console](/images/5/sso-3.PNG?width=90pc)

4. After creating SSO User, on the left panel, click **AWS accounts**, choose AWS Account which is assigned SSO User (that mean SSO User can access to this AWS Account), then choose **Assign users**.
![Assign SSO User](/images/5/sso-4.PNG?width=90pc)

5. Choose SSO User which you want to assign to this AWS Account, click **Next:Permission sets**
![Assign SSO User](/images/5/sso-5.PNG?width=90pc)

6. On the **Assign User Console**, choose **Create new permission set**.
![Assign SSO User](/images/5/sso-6.PNG?width=90pc)

7.  On the **Create new permission set**, choose **Create a custom permission set**, then click **Next:Details**
![Assign SSO User](/images/5/sso-7.PNG?width=90pc)

8.  Fill the information of **permission set**, after that choose **Attach AWS managed policies** for attaching the policies to SSO User (that mean SSO user can access to this AWS Account with these policies).
9.  You can attach **AWS managed policies** or create **custom permission only**, then choose **Next:Tags**
![Assign SSO User](/images/5/sso-8.PNG?width=90pc)

10. Choose **Next:Review**
11. Choose **Create** to finish **creating permission sets** and **Assign SSO User** to AWS Account
![Assign SSO User](/images/5/sso-9.PNG?width=90pc)

#### Result

After assigning SSO User, you can log in to User portal URL of Single Sign-On. You can see the AWS Account which is assigned by this SSO User with different policies.

![Assign SSO Login Console](/images/5/sso-10.PNG?width=90pc)