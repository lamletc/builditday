+++
title = "Thiết lập Control Tower"
date = 2021-07-31T00:38:32+07:00
weight = 3
chapter = false
pre = "<b>3. </b>"
+++

**Xây dựng AWS Control Tower**

1. Đăng nhập vào [AWS Management Console](https://console.aws.amazon.com/controltower/) trên tài khoản được chọn làm **Tài khoản Master**.

![Control Tower Dashboard](/images/3/1.png?width=90pc)

2. Tại trang **Setting up Control Tower**, hãy chắc chắn bạn đang sử dụng ở **10 Vùng được hỗ trợ (N. Virginia, Ohio, Oregon, Central, Sydney, Singapore, Frankfurt, Ireland, London, hay Stockholm)**.

![Choose right region](/images/3/2.png?width=90pc)

3. Ở mục **Shared accounts**, **Management account** sẽ hiển thị mặc định tài khoản AWS bạn sẽ sử dụng để thiết lập Control Tower. Bạn sẽ cần thêm vào hai địa chỉ email cho 2 Tài khoản AWS **Log Archive** và **Audit**

![Provide email for 2 AWS Accounts](/images/3/3.png?width=90pc)

4. Kiểm tra các thông tin ở mục **Service permissions**. Chọn vào lựa chọn **I understand the permissions AWS Control Tower will use to administer AWS resource and enforce rules on my behalf. I also understand the guidance on the use of  AWS Control Tower and the underlying AWS resources**. Sau đó chọn **Set up landing zone**.

![Set up Landing Zone](/images/3/4.png?width=90pc)

5. Việc thiết lập Landing Zone sẽ mất khoảng 60 phút. 

![Set up Landing Zone](/images/3/5.png?width=90pc)

6. Trong quá trình thiết lập Landing Zone, bạn sẽ nhận được email gửi đến hai địa chỉ email đã được thiết lập ở bước bên trên cho hai tài khoản mặc định. Hãy chọn vào **Confirm subscription** trong email nhận được để chấp nhận lời mời.

![Email confirmation](/images/3/6.png?width=90pc)

7. Sau khi khởi tạo AWS Control Tower, bạn có thể kiểm tra trong Control Tower Console để thấy được **3 tài khoản (Master, Log Archive, Audit)** ở trạng thái **Enrolled**

![Enrolled AWS Account](/images/3/7.png?width=90pc)
