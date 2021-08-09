+++
title = "Enroll existing account"
date = 2021-07-31T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>4.1. </b>"
+++

**Enroll existing account to AWS Control Tower**

{{% notice note %}}
AWS force you to configure AWS Control Tower **without Root Account** that mean you need to **create IAM User with Administrator Policies** in order to configure Control Tower
{{% /notice %}}

**Contents**
- [Create IAM User](#create-iam-user)
- [Add IAM User to AWS Service Catalog](#add-iam-user-to-aws-service-catalog)
- [Give AWS Control Tower Administrator Rights](#give-aws-control-tower-administrator-rights)
- [Invite Existing Account to Control Tower](#invite-existing-account-to-control-tower)

#### Create IAM User

1. Log in to the AWS Management Console of the account where you plan to deploy AWS Control Tower. Access to **AWS IAM console** at https://console.aws.amazon.com/iam/
![IAM Dashboard](/images/4/iam-1.png?width=90pc)

2. On the left panel, choose **Users**, then click **Add user**
![Users Dashboard](/images/4/iam-2.png?width=90pc)

3. On the **Set user details** console, fill the IAM User information: **User name, Access type, Console password**, then choose **Next: Permissions**
![Set user details](/images/4/iam-3.png?width=90pc)
   
4. Setting up **Permissions** for IAM User by **Attach existing policies directly** with **AdministratorAccess** policy. Then choose **Next: Tags**
![Attach Permissions](/images/4/iam-4.png?width=90pc)

5. Choose **Next: Review**

6. After reviewing IAM User configuration, choose **Create user**
![Create user](/images/4/iam-5.png?width=90pc)

7. You need to download **.csv file** which is included **IAM User information**
![IAM Information](/images/4/iam-6.png?width=90pc)

#### Add IAM User to AWS Service Catalog

1. Log in to the AWS Management Console of the account where you plan to deploy AWS Control Tower. Access to **AWS Service Catalog console** at https://console.aws.amazon.com/servicecatalog/
![AWS Service Catalog Console](/images/4/service-catalog-1.png?width=90pc)

2. On the left panel, choose **Portfolios** and click **AWS Control Tower Account Factory Portfolio**
![Portfolios Dashboard](/images/4/service-catalog-2.png?width=90pc)

3. On the tab of **Group, roles and users**, choose **Add groups, roles, users**
![Add IAM User](/images/4/service-catalog-3.png?width=90pc)

4. On the tab of **User**, choose the **IAM User** which use to configure Control Tower, then click **Add access**. Then you can see your IAM User is on the access group.
![Add IAM User](/images/4/service-catalog-4.png?width=90pc)

#### Give AWS Control Tower Administrator Rights

1. Log in to the AWS Management Console of the account which is invited to Control Tower. Access to **AWS IAM Console** at https://console.aws.amazon.com/iam/
![AWS IAM Console](/images/4/right-access-1.png?width=90pc)

2. On the left panel, choose **Roles**, then click **Create role**
![Create roles](/images/4/right-access-2.png?width=90pc)

3. Choose **Another AWS account**, fill **Master Account ID** then click **Next: Permissions**
![Attach Permissions](/images/4/right-access-3.png?width=90pc)

4. Choose **AdministratorAccess**, then click **Next: Tags**
![Attach Permissions](/images/4/right-access-4.png?width=90pc)
5. Choose **Next: Review**
6. Naming for Role, then click **Create role**
![Create Roles](/images/4/right-access-5.png?width=90pc)

#### Invite Existing Account to Control Tower

1. Log in to the AWS Management Console of the account where you plan to deploy Control Tower. Access to **AWS Organizations Console** at https://console.aws.amazon.com/organizations/
   Then choose Add Account
![AWS Organizations](/images/4/invite-1.png?width=90pc)

2. Choose **Invite account** and fill the **email** or **account ID** that you want to invite, then click **Invite**
![Invite Account](/images/4/invite-2.png?width=90pc)

3. On the email browser of this account, you can see the invitation email to Organization, click the **URL link**
![Invitation Email](/images/4/invite-3.png?width=90pc)

4. On the **AWS Organizations Console**, click **Accept**
![Accept Invitation](/images/4/invite-4.png?width=90pc)

{{% notice note %}}
On the AWS Organization Console of the account where you plan to deloy Control Tower, you can see new AWS account is a member of OU. By default, new AWS Account will be assign to **Root OU**; we need to move this new member to **a right OU**. On this case, we will move it to **Custom OU**.
{{% /notice %}}

5. In the console, choose the **new member account**, then click **Move**.
![Moving OU](/images/4/invite-5.png?width=90pc)

6. On the left panel of AWS Control Tower Console, choose **Accounts**, you can see new account which have added; however, this account is **Not enrolled**.
![New Account on Control Tower](/images/4/invite-6.png?width=90pc)

7. On the left panel, choose **Organization Units**, then click to **Custom OU**.
![Re-register OU](/images/4/invite-7.png?width=90pc)

8. Choose **Re-Register OU** in order to enroll new account to Control Tower.
![Re-register OU](/images/4/invite-8.png?width=90pc)

9. After **Re-Register OU**, you can see all new account is **enrolled**.
![Re-register OU](/images/4/invite-9.png?width=90pc)

{{% notice tip %}}
We successfully add an **EXISTING** AWS account to Contol Tower.
{{% /notice %}}