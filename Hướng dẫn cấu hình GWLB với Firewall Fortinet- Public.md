

# **Bài thực hành:**

# **Sử dụng Gateway LoadBalancer kết hợp với Fortinet Firewall trên AWS**

# **I. LÝ THUYẾT**

Gateway Load Balancers cho phép bạn triển khai, mở rộng quy mô và quản lý các thiết bị ảo chẳng hạn như tường lửa, IDS/IPS và hệ thống deep-packet IPS. Nó kết hợp transparent network gateway (nghĩa là một điểm nút mạng áp dụng duy nhất cho tất cả lưu lượng in và out) và điều phối lưu lượng trong khi đảm bảo mở rộng các thiết bị Security của bạn theo nhu cầu.

Bộ cân bằng tải Gateway hoạt động ở lớp 3 của mô hình OSI lớp mạng. Nó lắng nghe tất cả các gói IP trên tất cả các cổng và chuyển tiếp lưu lượng đến nhóm các thiết bị bảo mật. Nó duy trì sự một phiên cụ thể bằng cách sử dụng 5 tuple (đối với luồng TCP / UDP) hoặc 3 tuple (đối với luồng không phải TCP / UDP). Gateway Load Balancer và các thiết bị bảo mật (được đăng kí trong trong Target Group) trao đổi lưu lượng ứng dụng bằng giao thức GENEVE trên cổng 6081. Nó hỗ trợ kích thước đơn vị truyền tải (MTU) tối đa là 8500 byte.

Gateway Load Balancers sử dụng Endpoint của nó (đóng vai trò như các card mạng ảo của GWLB) để trao đổi lưu lượng một cách an toàn giữa  các VPC. Các endpoints này nằm trong VPC, cung cấp kết nối private-link giữa các thiết bị bảo mật ảo trong VPC của nhà cung cấp dịch vụ (Service Provider) và các máy chủ ứng dụng trong VPC của khách hàng. 

Lưu lượng truy cập được điều hướng đến và đi từ Gateway Load Balancer Endpoint  được định cấu hình bằng cách sử dụng bảng định tuyến. Lưu lượng truy cập từ VPC của khách hàng sẽ qua Endpoint đến Gateway Load Balancer nằm trong VPC của nhà cung cấp dịch vụ, đi tới các Security appliances, sau đó quay trở lại VPC của khách hàng sau khi.  Danh sách các security appliance kết hợp được với GWLB được liệt kê trong liên kết sau : https://aws.amazon.com/elasticloadbalancing/partners/. 

**Phương thức hoạt động**

Dưới dây là một ví dụ cơ bản mô tả hoạt động của Gateway Load Balancer trong việc kiểm soát traffic in và out internet của Application Server tại Service consumer VPC.

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.001.png)

Lưu lượng truy cập từ internet đến ứng dụng (mũi tên màu xanh lam):

- Lưu lượng truy cập vào VPC của người tiêu dùng dịch vụ thông qua internet gateway.
- Lưu lượng truy cập được gửi đến Gateway Load Balancer Endpoint (1)
- Lưu lượng truy cập được gửi đến Gateway Load Balancer để kiểm tra lưu lượng thông qua thiết bị bảo mật.
- Lưu lượng truy cập được gửi trở lại  Gateway Load Balancer Endpoints sau khi kiểm tra.
- Lưu lượng được gửi đến các máy chủ ứng dụng (mạng subnet đích).

Lưu lượng truy cập từ ứng dụng đến internet (mũi tên màu cam):

- Lưu lượng được gửi đến Gateway Load Balancer Endpoints (2)
- Lưu lượng truy cập được gửi đến Gateway Load Balancer để kiểm tra thông qua thiết bị bảo mật.
- Lưu lượng truy cập được gửi trở lại Gateway Load Balancer Endpoints sau khi kiểm tra.
- Lưu lượng truy cập được gửi đến internet gateway dựa trên cấu hình bảng định tuyến.
- Lưu lượng truy cập được chuyển trở lại internet.

Trong đó

- Bảng định tuyến của IGW

  | **Destination** |    **Target**     |
  | :-------------: | :---------------: |
  |   10.0.0.0/16   |       Local       |
  |   10.0.1.0/24   | *vpc-endpoint-id* |

- Bảng định tuyến của ứng dụng subnet

  | **Destination** |    **Target**     |
  | :-------------: | :---------------: |
  |   10.0.0.0/16   |       Local       |
  |    0.0.0.0/0    | *vpc-endpoint-id* |

- Bảng định tuyến của Gateway Load Balancer Endpoints subnet

  | **Destination** |    **Target**     |
  | :-------------: | :---------------: |
  |   10.0.0.0/16   |       Local       |
  |    0.0.0.0/0    | *vpc-endpoint-id* |

Dựa trên phương thức hoạt động của GWLB mà nó có thể được ứng dụng trong nhiều mô hình triển khai khác nhau

- Kiểm soát north/south traffic của nhiều Vpc cùng lúc bằng cách kết hợp với Transit-Gateway

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.002.png)

- Kiểm soát east/west traffic giữa nhiều VPC cùng lúc bằng cách kết hợp với Transit-Gateway

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.003.png)

- Kết hợp cả hai mô hình trên

# **II. LABS GUIDE**
Mục đích của bài labs đó là kiểm soát truy cập in/out Internet của Application EC2 bằng Firewall FortiGate. Đồng thời thì bài labs sử dụng Gateway LoadBalancer để thực hiện mục đích trên. Bắt đầu luôn nào.

Chúng ta hãy bắt đầu bài labs bằng mô hình tổng quan. Mô hình bài labs này bao gồm các thành phần chính sau:

- Custom VPC: 
  - Application EC2 Instance: nằm trong một subnet (10.1.2.0/24) của Custom Policy. Đây chính là EC2 sẽ được bảo vệ và thực hiện kiểm soát traffic bởi firewall FortiGate
  - Gateway Loadbalancer Endpoint: là một card mạng ảo có địa chỉ nằm trong một subnet khác của Custom VPC (10.1.1.0/24)
  - IGW: cửa ngõ truy cập internet của Custom VPC. 
- Security VPC: 
  - Một thiết bị FortiGate có 2 interface. Một interface thuộc Public subnet (MGMT interface) và một interface thuộc private subnet (geneve)
  - Gateway Loadbalancer: sẽ kết nối với Fortigate thông qua Private interface
  - IGW: cửa ngõ truy cập internet của Security VPC, trong bài lab dung để truy cập quản trị FortiGate từ internet. 

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.004.png)

## **1. Đăng kí sử dụng Fortigate Firewall trên market place**

Việc đầu tiên khi cần sử dụng Firewall Fortigate trên AWS là bạn phải đăng kí để sử  dụng nó. Việc này tương tự nếu như muốn sử dụng các sản phẩm của các hãng khác trên AWS.

- Tại phần tìm kiếm dịch vụ, bạn thực hiện tìm kiếm “**AWS Marketplace Subscriptions**” và chọn vào chỉ mục như hình vẽ phía dưới

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.005.png)

Tại phần “**Disvover product**” bạn gõ từ khóa “**FortiGate**” để tìm kiếm

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.006.png)


- Sau khi các sản phẩm Fortinet được thể hiện ra, bạn chọn vào sản phẩm “**Fortinet FortiGate Next-Generation Firewall**” và chú ý sản phẩm này có “**Free Trial**” nên là cứ sử dụng bản dùng thử miễn phí nhé, không mất tiền license đâu

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.007.png)

- Bạn có thể xem các thông tin về sản phẩm, các khuyến khích của AWS và phương thức tính tiền của việc sử dụng FortiGate trên AWS. Chọn “**Continue to Subcribe**” để tiến hành đăng kí sử dụng sản phẩm trên.

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.008.png)

- Sau khi đăng kí, vào phần “**Manage subcriptions**” để kiểm tra đã đăng kí thành công hay chưa. Tất cả các sản phẩm mà bạn đăng kí thành công đều nằm ở đây. 

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.009.png)

## **2. Triển khai Fortinet Firewall bước đầu trên AWS**

###  **2.1. Khởi tại FortiGate Instance**

Sau khi đã đăng kí thành công Fortinet Firewall, chúng ta hãy bắt đầu deploy luôn một FortiGate Instance. 

- Step1: Chọn AMI là ForiGate Firewall mà bạn đã đăng kí, sau đó chọn **Select**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.010.png)

- Có một cửa sổ bật lên thông báo giá của FortiGate (vì đang sử dụng bản free trail còn thời hạn nên không cần lo lắng nhé). Bấm **Continue**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.011.png)


- Step2: Chọn **instance-type t3.small** (bạn có thể chọn instance-type khác mà FortiGate hỗ trợ)

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.012.png)

- Step 3: Config instance details
  - Tạo Fortigate tại **Security VPC**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.013.png)




- Tạo 2 interface cho FortiGate, một tại public subnet và một tại private subnet

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.014.png)



- Step 4: Giữ giá trị mặc định. Chọn **Next: Add Tags**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.015.png)

- Step 5: **Add Tags**:  { Key: **Name**, Value: **FortiGate** }


- Step 6: Chọn SG allow any allow traffic (bởi vì thực tế các giao thực truy cập tới interface của firewall sẽ được cấu hình bởi chính sách truy cập của Firewall Fortigate)

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.016.png)

- Step 7: chọn aws key trên môi trường của bạn. Sau đó chọn **Launch**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.017.png)

### **2.2. Truy cập vào giao diện quản trị của FortiGate**

#### **2.2.1. Gán EIP cho FortiGate**

Sau khi FortiGate instance khởi tạo xong. Bạn thực hiện chọn vào instance và copy lại hai thông tin sau:

- ENI id của Primary Network Interface (đây chính là MGMT interface cho FortiGate)
- Instance-id: passwork mặc định để truy cập giao diện quản trị web của FortiGate

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.007.png)





- Tại phần **Elastic IP Address**, chọn **Allocate Elastic IP Address** để xin cấp phát một Public EIP từ AWS 

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.018.png)



- Sau đó, gán EIP này cho Primary Network Interface ID của FortiGate (EIP được cấp ở đây là 34.205.224.21, trong môi trường lab của bạn thì sẽ được cấp EIP khác)

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.019.png)
#### 2.2.2. **Truy cập giao diện quản trị FortiGate**

- Thực hiện truy cập FortiGate bằng địa chỉ : [https://<EIP_đã]() được cấp phát>. Chọn **Advanced** và **Accept Risk** để tiếp tục

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.020.png)

- Thực hiện đăng nhập bằng account mặc định. FortiGate sẽ yều cầu đổi lại mật khẩu này sau lần đăng nhập đầu tiên
  - Username: **admin**
  - Password: <instance-id>

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.021.png)



- Thiết lập hostname và một số tham số ban đầu cho Fortigate

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.022.png)



Cuối bước này, bạn đã đăng nhập thành công

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.020.png)

#### 2.2.3.**Cấu hình interface bước đầu**

Tiến hành chọn mục **Network**, bạn sẽ thấy FortiGate có hai interface mà bạn đã thiết lập. 

- Port1: là port mà bạn đang dung để truy cập quản trị (là primary interface được gán EIP ở bước trên). 
- Port2: đây sẽ là port được cấu hình kết hợp với GWLB ở bước sau. Mặc định port này sẽ không cho phép ping, https hay bất kỳ giao thức quản trị nào khác.

Trước khi cấu hình GWLB, chọn edit cấu hình **port2** rồi tiến hành bật cho phép PING, HTTPS, HTTP và SSH trên port này

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.023.png)

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.cfdcece1-ab61-4631-b0ce-5e0ccbcef755.024.png)

## 3.**Triển khai Gateway Load Balancer**

### 3.1.**Tạo Gateway Load Balancer**

Để tạo GWLB, tại EC2 Service, bạn chọn **Load Balancer** và chọn **Create Load Balancer**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.001.png)

` `Chọn **Gateway Load Balancer** rồi bấm **Create**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.002.png)

Điền các thông số sau để tạo GWLB

- Name: FirewallGwlb
- Network Mapping: Chọn “Security VPC” và chọn Private-Subnet (trong thực tế bạn sẽ chọn các subnet trùng với subnet của Fortigate private interface)
- Tại phần IP listener routing, chọn Create Target Group:

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.003.png)

- Sau đó cửa sổ tạo Target Group sẽ bật lên
  - Giá trị health-check đặt là HTTP port 80
  - Register targets với type là IP, nội dung chinh là IP của Private-Interface (port2) của Firewall FortiGate, sau đó chọn **Include as pending below** và **Register Pending targets**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.004.png)

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.005.png)


(Chú ý, phải đợi một lúc để kiểm tra health-check đối với FortiGate cần phải báo healthy sau bước này. Nếu bạn không bật HTTP tại port2 của Fortigate như bước 2.2.3 thì health-check sẽ báo Fail)

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.006.png)

- Giữ các thông số khác mặc định rồi chọn Create để tạo Gateway LoadBalancer

Sau khi khởi tạo thành công, GWLB sẽ khởi tạo các Network interface tại các subnet mà bạn đã chọn, bạn cần lưu lại địa chỉ IP của Network-interface có chung subnet với Private-interface (port2) của FortiGate (ở đây là subnet 10.2.11.0/24 và IP là 10.2.11.236)

### 3.2.**Tạo Service Endpoint**

Tại VPC Service, bạn chọn **Endpoint Services** sau đó chọn **Create Endpoint Services**.

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.007.png)

- **Name**:GWLBServiceEndpoint
- Tại mục **Load balancer type** chọn **Gateway** 
- Tại mục **Available load balancers** chọn **FirewallGwlb** (đây là GWLB đã tạo ở bước 3.1)
- Các thông số khác để mặc định, sau đó chọn **Create**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.008.png)

Sau khi tạo thành công GWLB service endpoint, bạn lưu lại **service name** của nó để dùng cho bước tiếp theo



![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.009.png)



### 3.3.**Tạo Endpoint**

Tại mục **Endpoints**, chọn **Create Endpoints**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.009.png)

- Tại **Service category,** chọn **Find service by name** sau đó paste phần service name của service endpoint tại bước 3.2

  

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.010.png)

- Tại phần VPC và Subnet thì bạn chọn lần lượt VPC-Id của Custom VPC và Subnet-ID của Private subnet  trong cùng VPC này

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.011.png)

**Chú ý** sau khi tạo xong thì Endpoint sẽ ở trạng thái **pending acceptance**



![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.012.png)

Bạn vào Endpoint service đã tạo ở bước 3.2. Sau đó chọn **Endpoint Connection** -> chọn endpoint -> chọn **Action** -> chọn **Accept endpoint connection request** -> gõ **accept** rồi Enter



![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.013.png)

## 4.**Cấu hình VDOM và Geneve trên Fortigate**

### 4.1.**Cấu hình Vdom**

Vdom gọi nôm na là tính năng cho phép chia tách một thiết bị FortiGate lớn thành các thiết bị Fortigate nhỏ hơn. Các Vdom sẽ có cấu hình, policy, định tuyến và interface độc lập với nhau. Mục đích của việc chia VDOM đó là tách riêng một VDOM để xử lý traffic cho các Custom VPC và một VPC còn lại chỉ nhằm mục đích management.

- Để sử dụng Vdom, bạn chọn **System**, tại phần System Operation Setting, chọn enable phần **Virtual Domain**.

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.014.png)



Sau đó hệ thống sẽ bắt bạn login lại. Kiểm tra tại phần **System** >> **VDOM** đã có hai vdom : **FG-traffic** vdom và **root** vdom. Chú ý hiện vdom FG-traffic vẫn **chưa được gán interface nào**



![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.014.png)



Thực hiện truy cập cấu hình CLI, chọn ICON ![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.015.png), cấu hình các lệnh sau trên interface port2:

> *config system interface*
>
>   *edit "port2"*
>
> ​    *set vdom "FG-traffic"* 
>
> ​    *set mode dhcp*
>
> ​    *set allowaccess ping https ssh http probe-response*
>
> ​    *set type physical*
>
> ​    *set alias "GWLB-Connect-Interface"*
>
> ​    *set snmp-index 2*
>
> ​    *set defaultgw disable*
>
>   *next*
>
> *end*



Sau khi cấu hình, kiểm tra thấy vdom FG-traffic đã được gán thêm **port2**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.016.png)

### 4.2. **Cấu hình geneve**

Cấu hình geneve-interface nhằm muc đích Fortigate có thể kết nối và hoạt động với Gateway LoadBaLancer. Trong đó 10.2.11.236 là IP của GWLB Network interface mà bạn đã lưu tại bước 3.1

> *config vdom*
>
> *edit FG-traffic*
>
> *config system geneve*
>
>   *edit "awsgeneve"*
>
> ​    *set interface "port2"*
>
> ​    *set type ppp*
>
> ​    *set remote-ip 10.2.11.236*
>
>   *next*
>
> *end*



Kiểm tra tại phần network interface awsgeneve device và geneve interface  đã được tạo thành công

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.018.png)

### 4.3.**Cấu hình routing cho VDOM**

Để đảm bảo traffic nhận được tại interface geneve (tức là nhận từ Gateway Load Balancer) sẽ được xử lý nhận vào và gửi ra đều tại awsgeneve device (logic device chứa geneve interface)

Cấu hình static route trên geneve interface với cú pháp sau bằng CLI

> *config router static*
>
>   *edit 2*
>
> ​    *set priority 100*
>
> ​    *set device "awsgeneve"*
>
>   *next*
>
> *end*


![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.019.png)



Sau đó kiểm tra thấy static route đã được tạo thành công.

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.020.png)

### 4.4.**Cấu hình router policy cho VDOM**

Trong trường hợp vdom FG-traffic của bạn chứa nhiều interface thì cấu hình bước này dung để đảm bảo traffic nhận và gửi tại cùng Geneve interface. Trong trường hợp bài labs này do vdom FG-traffic chỉ có 1 interface thì bạn có thể bỏ qua bước này. Tuy nhiên trong thực tế rất có thể bạn sẽ cần đến nó 😊

Để cấu hình routing policy cho VDOM ta thực hiện router policy với cấu hình traffic source 0.0.0.0/0 và destination 10.1.0.0/16 (Custom VPC CIDR) với input-device và output-device đều là awsgeneve

> *config router policy*
>
>   *edit 1*
>
> ​    *set input-device "awsgeneve"*
>
> ​    *set src "0.0.0.0/0.0.0.0"*
>
> ​    *set dst "10.1.0.0/255.255.0.0"*
>
> ​    *set output-device "awsgeneve"*
>
>   *next*
>
> *end*

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.021.png)



Sau đó kiểm tra thấy router policy đã được tạo thành công.

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.022.png)

### 4.5.**Cấu hình policy cho VDOM**

Trước khi thực hiện các bài kiểm tra thì ta tạo hai policy như sau

**Policy 01:** 

- Name: Permit\_Test\_01
- In và out interface: awsgeneve
- Source và destination: All
- Service: All
- Action: Accept
- Nat: Disable
- Status: Enable

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.023.png)


**Policy 02**

- Name: Permit\_Test\_02
- In và out interface: Port2
- Source và destination: All
- Service: All
- Action: Accept
- Nat: Disable
- Status: Enable

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.024.png)



Kết quả hiển thị danh mục Firewall Policy như sau:

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.9188918a-83f9-4034-a809-ba4671e49390.025.png)




## 5. **Cấu hình định tuyến trên Custom VPC**

Để đảm bảo traffic từ Application server kết nối in/out ra internet đều đi qua GWLB rồi qua FortiGate Firewall thì bạn cần cấu hình các routing table sau

- Route table cho **Application Server subnet**: cấu hình default-route trỏ tới GWLB endpoint đã tạo ở bước 3.3 để đảm bảo traffic internet outbound của server sẽ đi qua GWLB endpoint
- Route cho **Interface Gateway**:  static route 10.1.2.0/24 trỏ tới GWLB endpoint đảm bảo traffic internet chiều về của server sẽ đi qua GWLB endpoint

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.001.png)



Tạo Route Table mới cho Application server subnet, cấu hình route 0.0.0.0/0 với next-hop Gateway Load. (Chú ý: Nhớ attach Application subnet vào Route Table)

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.002.png)



Tạo Route Table mới cho Internet Gateway, cấu hình route 10.1.2.0/24 với next-hop Gateway Load (Chú ý: Nhớ attach Internet Gateway vào Route Table)

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.003.png)

## 6.**Kiểm tra kết quả**

### 6.1. **Các bài test**

#### 6.1.1.**Kiểm tra ping 1.1.1.1 từ Application EC2** 

Thực hiện ping 1.1.1.1 từ EC2

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.004.png)

Kiểm tra kết quả trên giao diện FortiGate, traffic đã match vào policy Permit\_test\_01

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.005.png)

Nếu ta kéo policy Permit\_test\_01 xuống dưới policy Permit\_test\_02 thì traffic vẫn sẽ match vào policy Permit\_test\_01. Điều này chứng tỏ traffic đã nhận và gửi ra toàn bộ trên geneve interface. Kiểm tra kết quả matching log trên policy **Permit\_test\_01** ![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.006.png)



Bạn có thể thấy traffic đã match vào policy và được permit **Permit\_test\_01**

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.007.png)

#### 6.1.2. **Chặn kết nối tới Facebook của Application EC2**

Bạn thực hiện cấu hình policy như sau 

- Name: Block\_Facebook\_Test
- In và out interface: awsgeneve
- Source: All
- Destination: All Facebook interface service
- Service: All
- Action: DENY
- Nat: Disable
- Status: Enable

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.008.png)



Kéo policy này lên trên cùng để ưu tiên

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.007.png)




Kiểm tra kết nối tới www.facebook.com và www.google.com từ Application EC2, các kết quả truy cập facebook đều bị drop, các kết quả truy cập google vẫn được permit.

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.009.png)



Các traffic deny đã được match vào policy vừa tạo.

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.010.png)



Thực hiện disable policy trên rồi thử lại

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.011.png)



Kiểm tra kết nối tới www.facebook.com và www.google.com từ Application EC2, các kết quả truy cập facebook và google đều được permit.

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.012.png)



Các câu lệnh dung để debug trên FortiGate (lưu lại để dùng khi debug nhé)

> *config vdom*
>
> *edit FG-traffic*
>
> *diagnose debug enable*
>
> *diagnose debug flow filter addr 10.1.2.109*
>
> *diagnose debug flow show function-name enable*
>
> *diagnose debug flow trace start 100*

### 6.2. **Mô hình luồng dữ liệu**

Sau đây là mô hình luồng dữ liệu cuối cùng của bài labs. 

**Luồng traffic Out**

- (1): Khi Application EC2 muốn kết nối tới internet (ví dụ google.com hoặc 1.1.1.1) thì traffic sẽ được gửi tới GWLBe01 theo default được cấu hình trong Route Table
- (2): Traffic sau khi được tiếp nhận tại GWLBe01 tại Custom VPC sẽ được điều hướng sang GWLB tại Security VPC (thông qua sử dụng private link)
- (3): Traffic tới GWLB sẽ được gửi tới các target host bên trong Firewall Target Group (cụ thể trong bài labs chỉ register một thiết bị FortiGate)
- (4): Tại Fortigate sẽ nhận traffic tại geneve-interface tiến hành xử lý traffic theo Firewall Policy. Firewall sẽ tìm kiếm Out-going interface cho traffic (có destination 1.1.1.1) là interface geneve (dựa trên default-route đã cấu hình cho vdom FG-traffic)
- (5): GWLB hoàn trả traffic về GWLBe01
- (6): GWLBe01 gửi traffic ra internet (theo default-route trỏ tới Custion VPC IGW)
- (7): Traffic ra ngoài internet (để Application EC2 ra được internet thì private IP của nó sẽ được NAT sang Public-IP tại bước này)

**Luồng traffic IN**

- (8): Traffic từ internet trả về IGW (thực hiện NAT ngược lại bước 7)
- (9): Vì destination của traffic lúc này là IP\_Private của Application EC2 nên IGW sẽ gửi tới next-hop là GWLBe01
- (10): Tương tự tại bước 2, traffic sau khi được tiếp nhận tại GWLBe01 tại Custom VPC sẽ được điều hướng sang GWLB tại Security VPC (thông qua sử dụng private link)
- (11): Tương tự bước 3, Traffic tới GWLB sẽ được gửi tới thiết bị FortiGate
- (12): Tại Fortigate sẽ nhận traffic tại geneve-interface tiến hành xử lý traffic theo Firewall Policy (đã tạo và lưu session tại bước 4). Firewall sẽ tìm kiếm Out-going interface cho traffic có destination là IP\_Private của Application EC2, kết quả lại là interface geneve
- (13): GWLB hoàn trả traffic về GWLBe01
- (14): GWLBe01 hoàn trả traffic về Application EC2 instance

![](https://aws4vn.s3.ap-southeast-1.amazonaws.com/MarkDown/GWLB/Aspose.Words.b976b978-11aa-497e-9f0f-ee2ab176d0fc.013.png)

