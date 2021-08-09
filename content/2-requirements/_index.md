+++
title = "Lab Diagram & Requirements"
date = 2021-07-31T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2. </b>"
+++

**Lab Diagram and Required Resources**

**Contents:**
- [Lab Diagram](#lab-diagram)
- [Resource Requirements](#resource-requirements)

#### Lab Diagram

![AWS Control Tower](/images/1/2.png?width=40pc)

For this lab, we have 3 AWS Accounts; each account has a separate mission:
1. **AWS Master Account**: Setting up Control Tower, Networking and Data Services
2. **AWS DevTest Account**: Setting up Infrastructure of DevTest Environment
3. **AWS Production Account**: Setting up Infrastructure of Production Environment

In this section, we will deploy the AWS Control Tower service. The following resources are provisioned on the successful launch of the service:
* **2 organizational units (OU)** are created: 
  * **Core** for the Control Tower shared accounts 
  * **Custom** as a default location for any user created AWS accounts.
* **2 AWS accounts** in the **Core OU**: 
  * **Log archive** is used for centralized logging.
  * **Audit** is used for auditing.
* **24 preventive guardrails** to enforce policies and **16 detective guardrails** to detect violations.
* A native cloud directory with preconfigured groups and single sign-on access using **AWS SSO**.

#### Resource Requirements

1. Prepare **3 AWS Accounts**: Master / DevTest / Production
2. Prepare **2 emails** to confirm **AWS Audit account** and **AWS Log Account** that is created by AWS Control Tower

