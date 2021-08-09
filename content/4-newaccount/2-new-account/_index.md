+++
title = "Using Acccount Factory"
date = 2021-07-31T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>4.2. </b>"
+++

**Enroll new account using Acccount Factory**

{{% notice note %}}
AWS force you to configure AWS Control Tower **without Root Account** that mean you need to **create IAM User with Administrator Policies** in order to configure Control Tower
{{% /notice %}}

1. Log in to the **AWS Management Console** of the account where you plan to deploy Control Tower. Access to [**AWS Control Tower Console**](https://console.aws.amazon.com/controltower/). 
2. On the left panel, choose **Account factory**, then click **Enroll account**.
![Account Factory](/images/4/add-acc-1.png?width=90pc)

3. Fill the information which you want to create AWS Account: **Account email, Display name, AWS SSO email, AWS SSO username, OU**. Then click **Enroll account**.

{{% notice info %}}
Make sure the **Account email** is **not already registered** as an AWS account.
{{% /notice %}}

![Adding New AWS Account](/images/4/add-acc-2.png?width=90pc)

4. This email will be used to create **AWS Account** and **Enroll** to your **current Control Tower**.
![Enrolling account](/images/4/add-acc-3.png?width=90pc)

5. After this task, your new AWS Account will be enrolled to AWS Control Tower.
![New account is enrolled](/images/4/add-acc-4.png?width=90pc)

{{% notice tip %}}
We successfully add a **NEW** AWS account to Contol Tower using **Account Factory**.
{{% /notice %}}
