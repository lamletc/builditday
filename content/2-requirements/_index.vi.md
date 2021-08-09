+++
title = "Mô hình và Yêu cầu"
date = 2021-07-31T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2. </b>"
+++

**Mô hình bài thực hành và Tài nguyên cần thiết**

**Nội dung:**
- [Mô hình bài thực hành](#mô-hình-bài-thực-hành)
- [Các tài nguyên cần có](#các-tài-nguyên-cần-có)

#### Mô hình bài thực hành

![AWS Control Tower](/images/1/2.png?width=40pc)

Trong bài thực hành này, chúng ta sẽ sử dụng 3 Tài khoản AWS, mỗi tài khoản sẽ đóng vai trò tương tự như trong thực tế:
1. **AWS Master Account**: Dùng để thiết lập Control Tower, Mạng và Các dịch vụ.
2. **AWS DevTest Account**: Dùng để xây dựng Hạ tầng hệ thống ở môi trường Development.
3. **AWS Production Account**: Dùng để xây dựng Hạ tầng hệ thống ở môi trường Production.

Trong phần này, chúng ta sẽ triển khai dịch vụ AWS Control Tower. Những tài nguyên sau sẽ được cung cấp sau khi triển khai thành công:
- **Hai Organization Unit (OUs)** sẽ được tạo:
  - **Core**:   Được sử dụng để đặt các tài khoản chia sẻ tài nguyên của Control Tower.
  - **Custom**: Được sử dụng để đặt các tài khoản do người dùng thêm vào.
- **Hai Tài khoản AWS** đặt trong **Core OU**:
  - **Logging**: Tài khoản lưu trữ Log.
  - **Audit**:  Tài khoản sử dụng cho việc thực hiện Audit.
- **Preventive Guardrail** và **Detective Guardrail** được tạo mặc định:
  - **24 Preventive Guardrail**: Chứa các policy được áp mặc định.
  - **16 Detective Guardrail**: Nhằm phát hiện các vi phạm trong Organization.
- Dịch vụ Directory trên AWS với cấu hình mặc định phục vụ cho việc tích hợp với AWS Single Sign-On.

#### Các tài nguyên cần có

1. Chuẩn bị **3 Tài khoản AWS**: Master / DevTest / Production
2. Chuẩn bị **2 Email** để liên kết với hai tài khoản **Audit** và **Logging** được tạo mặc định bởi AWS Control Tower.
