+++
title = "AWS Single Sign-On"
date = 2020-04-18T00:38:32+07:00
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

**Nội dung**
- [Thiết lập AWS Single Sign-On](#thiết-lập-aws-single-sign-on)
- [Kết quả](#kết-quả)

#### Thiết lập AWS Single Sign-On

{{% notice note %}}
Thiết lập Single Sign-On cho phép bạn có thể đăng nhập vào các Tài khoản AWS một cách dễ dàng hơn thông qua việc chỉ sử dụng một tài khoản SSO để truy cập hết tất cả các tài khoản AWS với các chính sách quyền được cấp khác nhau. 
{{% /notice %}}

1. Đăng nhập vào **AWS Management Console** mà bạn muốn thêm vào AWS Control Tower. Sau đó truy cập vào [**AWS Single Sign-On console**](https://console.aws.amazon.com/singlesignon/).

![AWS SSO Console](/images/5/sso-1.PNG?width=90pc)

2. Ở thanh điều hướng bên trái, chọn **Users**. Sau đó chọn vào User đã được tạo ở bước tạo Control Tower.
![AWS SSO Console](/images/5/sso-2.PNG?width=90pc)

3. Sau đó chọn vào **Send email verification link**. Bạn cần truy cập vào email của tài khoản SSO User và chọn vào đường dẫn xác nhận quá trình khởi tạo SSO User.
![AWS SSO Console](/images/5/sso-3.PNG?width=90pc)

4. Sau khi khởi tạo SSO User, ở thanh điều hướng bên trái, chọn **AWS accounts**, chọn tài khoản AWS đã được gắn cho SSO User (nghĩa là SSO User đã được truy cập vào tài khoản AWS này), sau đó chọn  **Assign users**.
![Assign SSO User](/images/5/sso-4.PNG?width=90pc)

5. Chọn vào SSO User mà bạn muốn gán vào AWS Account, chọn **Next:Permission sets**
![Assign SSO User](/images/5/sso-5.PNG?width=90pc)

6. Ở cửa sổ **Assign User Console**, chọn **Create new permission set**.
![Assign SSO User](/images/5/sso-6.PNG?width=90pc)

7. Ở mục **Create new permission set**, chọn **Create a custom permission set**, sau đó chọn **Next:Details**.
![Assign SSO User](/images/5/sso-7.PNG?width=90pc)

8. Thiết lập các thông tin về **permission set**, sau đó chọn vào **Attach AWS managed policies** để gán các chính sách quyền cho SSO User (việc này cho phép SSO User có thể truy cập vào tài khoản AWS đó với các chính sách được lựa chọn).
9. Bạn có thể sử dụng các **AWS managed policies** hoặc sử dụng **custom permission only**, sau đó chọn vào **Next:Tags**
![Assign SSO User](/images/5/sso-8.PNG?width=90pc)

10. Chọn **Next:Review**
11. Chọn **Create** để hoàn thành bước tạo các **permission set** và gán **SSO User** vào tài khoản AWS.
![Assign SSO User](/images/5/sso-9.PNG?width=90pc)

#### Kết quả

Sau khi gán SSO User vào tài khoản AWS, bạn có thể đăng nhập vào trang portal SSO. Sau đó đăng nhập vào tài khoản SSO đã tạo. Bạn sẽ thấy được các tài khoản AWS đã được gán cho SSO User đó với các chính sách quyền khác nhau.

![Assign SSO Login Console](/images/5/sso-10.PNG?width=90pc)