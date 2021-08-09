+++
title = "Đăng ký tài khoản sẵn có"
date = 2021-07-31T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>4.1. </b>"
+++

**Đăng ký Tài khoản đang sử dụng vào Control Tower**

{{% notice note %}}
AWS mặc định sẽ thiết lập AWS Control Tower **không sử dụng Root Account**. Nghĩa là bạn cần phải tạo **IAM User với quyền Administrator** để thiết lập Control Tower.
{{% /notice %}}

**Nội dung**
- [Khởi tạo IAM User](#khởi-tạo-iam-user)
- [Thêm IAM User vào AWS Service Catalog](#thêm-iam-user-vào-aws-service-catalog)
- [Cấp quyền Administrator cho AWS Control Tower](#cấp-quyền-administrator-cho-aws-control-tower)
- [Mời các Tài khoản sẵn có vào Control Tower](#mời-các-tài-khoản-sẵn-có-vào-control-tower)

#### Khởi tạo IAM User

1. Đăng nhập vào **AWS Management Console** mà bạn muốn thêm vào AWS Control Tower. Sau đó truy cập vào [**AWS IAM Console**](https://console.aws.amazon.com/iam/).
![IAM Dashboard](/images/4/iam-1.png?width=90pc)

2. Ở thanh điều hướng bên trái, chọn **Users**, sau đó chọn **Add user**.
![Users Dashboard](/images/4/iam-2.png?width=90pc)

3. Ở trang **Set user details**, điền các thông tin IAM User bao gồm: **User name, Access type, Console password**, sau đó chọn **Next: Permissions**.
![Set user details](/images/4/iam-3.png?width=90pc)
   
4. Tiếp theo, thiết lập **Permissions** cho IAM User bằng việc chọn **Attach existing policies directly** và chọn policy **AdministratorAccess**. Sau đó chọn **Next: Tags**.
![Attach Permissions](/images/4/iam-4.png?width=90pc)

5. Tiếp theo chọn vào **Next: Review**.

6. Sau khi kiểm tra **các thông tin IAM User** lần cuối, chọn **Create user**.
![Create user](/images/4/iam-5.png?width=90pc)

7. Hãy tải về tập tin **.csv** chứa **thông tin IAM User**.
![IAM Information](/images/4/iam-6.png?width=90pc)

#### Thêm IAM User vào AWS Service Catalog

1. Đăng nhập vào **AWS Management Console** mà bạn muốn thêm vào AWS Control Tower. Sau đó truy cập vào  [**AWS Service Catalog console**](https://console.aws.amazon.com/servicecatalog/).
![AWS Service Catalog Console](/images/4/service-catalog-1.png?width=90pc)

2. Ở thanh điều hướng bên trái, chọn **Portfolios** và chọn **AWS Control Tower Account Factory Portfolio**.
![Portfolios Dashboard](/images/4/service-catalog-2.png?width=90pc)

3. Ở tab **Group, roles and users**, chọn **Add groups, roles, users**.
![Add IAM User](/images/4/service-catalog-3.png?width=90pc)

4. Ở tab **User**, chọn **IAM User** được dùng để thiết lập Control Tower, sau đó chọn **Add access**. Cuối cùng, bạn sẽ thấy IAM User được thêm vào Access group.
![Add IAM User](/images/4/service-catalog-4.png?width=90pc)

#### Cấp quyền Administrator cho AWS Control Tower

1. Đăng nhập vào **AWS Management Console** mà bạn muốn thêm vào AWS Control Tower. Sau đó truy cập vào [**AWS IAM Console**](https://console.aws.amazon.com/iam/).
![AWS IAM Console](/images/4/right-access-1.png?width=90pc)

2. Ở thanh điều hướng bên trái, chọn **Roles**, sau đó chọn **Create role**.
![Create roles](/images/4/right-access-2.png?width=90pc)

3. Chọn **Another AWS account**, điền thông tin **Master Account ID** và chọn **Next: Permissions**.
![Attach Permissions](/images/4/right-access-3.png?width=90pc)

4. Chọn **AdministratorAccess**, sau đó chọn **Next: Tags**.
![Attach Permissions](/images/4/right-access-4.png?width=90pc)
5. Chọn **Next: Review**
6. Đặt tên cho Role, và chọn **Create role**.
![Create Roles](/images/4/right-access-5.png?width=90pc)

#### Mời các Tài khoản sẵn có vào Control Tower

1. Đăng nhập vào **AWS Management Console** trên tài khoản đã thiết lập Control Tower. Sau đó truy cập vào [**AWS Organizations Console**](https://console.aws.amazon.com/organizations/). Chọn vào **Add Account**.
![AWS Organizations](/images/4/invite-1.png?width=90pc)

2. Chọn **Invite account** và điền vào các thông tin **email** hoặc **account ID** của tài khoản muốn thêm. Sau đó chọn **Invite**.
![Invite Account](/images/4/invite-2.png?width=90pc)

3. Kiểm tra email của tài khoản được mời, bạn sẽ thấy một lời mời thêm vào Organization, Chọn vào **đường dẫn** trong email.
![Invitation Email](/images/4/invite-3.png?width=90pc)

4. Ở trong giao diện **AWS Organizations Console**, chọn **Accept**
![Accept Invitation](/images/4/invite-4.png?width=90pc)

{{% notice note %}}
Ở trong **AWS Organization Console** của tài khoản mà bạn thêm vào Control Tower, bạn sẽ thấy tài khoản này là thành viên của một Organization và được thêm vào OU. Mặc định, một tài khoản mới được thêm vào sẽ nằm trong **Root OU**. Khi đó, bạn cần di chuyển tài khoản này vào **OU đúng** theo quy hoạch của doanh nghiệp. Ở bài thực hành này, bạn sẽ di chuyển tài khoản này vào **Custom OU** như chúng ta đã thống nhất ở bước đầu.
{{% /notice %}}

5. Ở giao diện Console, chọn vào **tài khoản vừa thêm**, sau đó chọn **Move**.
![Moving OU](/images/4/invite-5.png?width=90pc)

6. Ở thanh điều hướng bên trái, chọn **Accounts**, bạn sẽ thấy tài khoản mới được thêm. Tuy nhiên, trạng hái của nó đang là **Not enrolled**.
![New Account on Control Tower](/images/4/invite-6.png?width=90pc)

7. Ở thanh điều hướng bên trái, chọn **Organization Units**, sau đó chọn vào **Custom OU**.
![Re-register OU](/images/4/invite-7.png?width=90pc)

8. Chọn **Re-Register OU** để đăng ký tài khoản mới vào Control Tower.
![Re-register OU](/images/4/invite-8.png?width=90pc)

9. Sau khi chọn **Re-Register OU**, bạn sẽ thấy trạng thái của tài khoản mới là **Enrolled**.
![Re-register OU](/images/4/invite-9.png?width=90pc)

{{% notice tip %}}
Đến đây, bạn đã thành công trog việc thêm tài khoản **SẴN CÓ** vào Control Tower.
{{% /notice %}}