+++
title = "Sử dụng Acccount Factory"
date = 2021-07-31T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>4.2. </b>"
+++

**Thêm Tài khoản mới với Acccount Factory**

{{% notice note %}}
AWS mặc định sẽ thiết lập AWS Control Tower **không sử dụng Root Account**. Nghĩa là bạn cần phải tạo **IAM User với quyền Administrator** để thiết lập Control Tower.
{{% /notice %}}

1. Đăng nhập vào **AWS Management Console** trên tài khoản đã thiết lập Control Tower. Sau đó truy cập vào [**AWS Control Tower Console**](https://console.aws.amazon.com/controltower/). 
2. Ở thanh điều hướng bên trai, chọn **Account factory**, sau đó chọn **Enroll account** 
![Account Factory](/images/4/add-acc-1.png?width=90pc)

3. Điền các thông tin mà bạn muốn tạo một Tài khoản AWS mới: **Account email, Display name, AWS SSO email, AWS SSO username, OU**. Sau đó chọn **Enroll account**.

{{% notice info %}}
Hãy chắc chắn **email** tài khoản mới **chưa được sử dụng** để đăng ký tài khoản AWS.
{{% /notice %}}

![Adding New AWS Account](/images/4/add-acc-2.png?width=90pc)

4. Email mới sẽ được sử dụng để tạo một **Tài khoản AWS** và **Đăng ký** vào **Control Tower** hiện tại của bạn.
![Enrolling account](/images/4/add-acc-3.png?width=90pc)

5. Sau bước này, tài khoản AWS mới sẽ được đăng ký vào AWS Control Tower.
![New account is enrolled](/images/4/add-acc-4.png?width=90pc)

{{% notice tip %}}
Đến đây, bạn đã thành công trog việc thêm tài khoản **MỚI** vào Control Tower sử dụng **Account Factory**.
{{% /notice %}}
