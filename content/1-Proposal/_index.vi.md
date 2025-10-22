---
title: "Bản đề xuất"
date: "2025-10-02"
draft: false
pre: " <b> 1. </b> "
---

## Hệ thống Phân tích Tệp Đa Nền tảng (Multi-Platform File Analysis System) - Clone VirusTotal

### 1. Tóm tắt dự án
Dự án này nhằm mục đích phát triển và triển khai một hệ thống phân tích tệp đa nền tảng, hoạt động tương tự như VirusTotal. Hệ thống sẽ cho phép người dùng tải lên các tệp đáng ngờ để được quét và phân tích bởi nhiều công cụ chống phần mềm độc hại (antivirus engines) và dịch vụ phân tích khác nhau. Mục tiêu chính là cung cấp một công cụ mạnh mẽ, dễ sử dụng để phát hiện và đánh giá các mối đe dọa tiềm ẩn từ tệp, góp phần nâng cao an ninh mạng.

Hệ thống sẽ được xây dựng và triển khai hoàn toàn trên nền tảng điện toán đám mây Amazon Web Services (AWS), tận dụng các dịch vụ quản lý, khả năng mở rộng và bảo mật hàng đầu của AWS để đảm bảo hiệu suất, độ tin cậy và tính sẵn sàng cao.

### 2. Vấn đề đặt ra
#### Vấn đề
Trong bối cảnh các mối đe dọa an ninh mạng ngày càng tinh vi và phổ biến, nhu cầu về các công cụ phân tích tệp hiệu quả là vô cùng cấp thiết. VirusTotal đã chứng minh được giá trị của mình như một dịch vụ cộng đồng quan trọng, giúp người dùng và các chuyên gia an ninh mạng nhanh chóng xác định các tệp độc hại. Dự án này ra đời với mong muốn tạo ra một giải pháp tương tự, có thể tùy chỉnh và mở rộng, phục vụ cho các mục đích nghiên cứu, giáo dục hoặc ứng dụng cụ thể.

#### Giải pháp
Nền tảng này mang lại một giao diện web tập trung, nơi người dùng có thể:
- Tải lên các tệp tin khả nghi (chương trình thực thi, script, tài liệu văn phòng).
- Gửi tên miền, địa chỉ IP hoặc URL để phân tích.
- Tự động quét dữ liệu bằng nhiều công cụ antivirus, môi trường sandbox và nguồn OSINT.
- Kết hợp kết quả với các nguồn tình báo mối đe dọa nhằm nâng cao độ chính xác.
- Chia sẻ kết quả đã được ẩn danh với cộng đồng nghiên cứu để tăng tính hợp tác.

Người dùng gửi yêu cầu phân tích tệp tin hoặc URL qua giao diện Web App. Quy trình được xử lý theo hai hướng:  

1. **Truy vấn nhanh (Query Service):**  
   - Nếu tệp/URL đã được phân tích trước đó, Web Server gửi hash đến Query Service.  
   - Query Service kiểm tra dữ liệu trong DynamoDB.  
   - Nếu có kết quả, hệ thống phản hồi ngay cho người dùng qua giao diện web.  

2. **Xử lý mới (Processing Service):**  
   - Nếu dữ liệu chưa có trong cơ sở dữ liệu, hệ thống chuyển tệp/URL thô đến Processing Service.  
   - Processing Service thực hiện phân tích, quét và tạo báo cáo.  
   - Kết quả phân tích được gửi về Web Server để trả lại cho người dùng, đồng thời lưu trữ trong DynamoDB để phục vụ cho các truy vấn sau.  

Cách tiếp cận này giúp giảm thời gian phản hồi cho các yêu cầu lặp lại, đồng thời đảm bảo khả năng mở rộng khi có nhiều yêu cầu xử lý mới.  

#### Mục tiêu
- Phát triển chức năng cốt lõi: Xây dựng giao diện người dùng (UI) cho phép tải tệp lên, một dịch vụ truy vấn để kiểm tra kết quả phân tích đã có, và một dịch vụ xử lý để thực hiện phân tích tệp mới.
- Tích hợp đa công cụ: Khả năng tích hợp với nhiều công cụ quét và phân tích khác nhau (ví dụ: các API của các công cụ antivirus, sandbox phân tích tĩnh/động).
- Triển khai trên AWS: Thiết kế và triển khai kiến trúc hệ thống trên AWS để đảm bảo tính sẵn sàng cao, khả năng mở rộng linh hoạt và bảo mật mạnh mẽ.
- Tối ưu hóa chi phí: Xây dựng một kiến trúc hiệu quả về chi phí, tận dụng các dịch vụ AWS phù hợp với quy mô dự án.
- Xây dựng quy trình CI/CD: Thiết lập quy trình Tích hợp liên tục/Triển khai liên tục (CI/CD) để tự động hóa việc phát triển và triển khai ứng dụng.

### 3. Kiến trúc hệ thống
#### Tổng quan

![High Level View 2](/images/high-level-view-2.drawio.png)

1. Người dùng gửi yêu cầu phân tích tệp.
2. Máy chủ Web (Web Server) gửi yêu cầu đến Dịch vụ Truy vấn (Query Service). (Truy vấn bằng cách sử dụng mã băm - hash của tệp)
3. Dịch vụ Truy vấn giao tiếp với Cơ sở dữ liệu (Database).
4. Cơ sở dữ liệu phản hồi lại Dịch vụ Truy vấn.
5. Dịch vụ Truy vấn phản hồi lại Máy chủ Web.
6. Nếu truy vấn thành công, gửi phản hồi HTML cho Người dùng.
7. Nếu không thành công, thì gửi tệp đến Dịch vụ Xử lý (Processing Service). (Máy chủ Web nên giữ dữ liệu tệp thô)
8. Dịch vụ Xử lý gửi báo cáo trở lại Máy chủ Web.
9. Đồng thời lưu trữ báo cáo vào Cơ sở dữ liệu.
10. Cơ sở dữ liệu đồng bộ hóa.

#### Sơ đồ AWS

![High Level View](/images/high-level-view.drawio.png)

- **Tầng Web App (UI):**  
  Người dùng truy cập qua ALB, yêu cầu được phân phối đến các EC2 trong Auto Scaling Group. Tầng này chịu trách nhiệm giao diện và tiếp nhận request.  

- **Tầng Services:**  
  Bao gồm **Query Service** và **Processing Service**, được triển khai trong Auto Scaling Group ở các subnet riêng.  
  - **Query Service:** Kết nối với DynamoDB để xử lý các yêu cầu truy vấn hash.  
  - **Processing Service:** Nhận dữ liệu thô, phân tích mã độc, tạo báo cáo, sau đó lưu kết quả vào DynamoDB.  

- **Tầng Dữ liệu:**  
  **Amazon DynamoDB** lưu trữ cả hai dạng dữ liệu:  
  - **View:** Kết quả phân tích sẵn sàng cho người dùng.  
  - **Event Store:** Nhật ký và dữ liệu thô phục vụ phân tích chi tiết.  

- **Khả năng mở rộng:**  
  Cả Web App và Services đều dùng Auto Scaling Group, đảm bảo hệ thống có thể đáp ứng nhiều yêu cầu song song mà vẫn duy trì hiệu năng. 

#### Các dịch vụ AWS được sử dụng
Hệ thống sử dụng các dịch vụ AWS chính sau:  

- **Amazon VPC (Virtual Private Cloud):** Tạo mạng ảo riêng, chia thành nhiều subnet (10.0.100.0/24, 10.0.101.0/24, 10.0.102.0/24, 10.0.103.0/24) trong các Availability Zone khác nhau để tăng tính sẵn sàng.  
- **Elastic Load Balancer (ALB):** Phân phối lưu lượng từ người dùng đến các Web App (UI) chạy trên EC2.  
- **Amazon EC2:** Chạy ứng dụng Web và các dịch vụ xử lý.  
- **Auto Scaling Group:** Tự động mở rộng hoặc thu hẹp số lượng EC2 để đảm bảo hiệu năng và tiết kiệm chi phí.  
- **Amazon DynamoDB:** Cơ sở dữ liệu NoSQL dùng để lưu trữ báo cáo phân tích, kết quả truy vấn và dữ liệu đồng bộ giữa các service.  


### 5. Lộ trình & Mốc phát triển

#### Kế hoạch dự án

- **Trong kỳ thực tập (Tháng 1–3):** 3 tháng.  
  - Tháng 1: Nghiên cứu AWS và thử nghiệm 
  - Tháng 2: Thiết kế và điều chỉnh kiến trúc.  
  - Tháng 3: Triển khai, kiểm thử và đưa hệ thống vào hoạt động.  

### 6. Ước tính ngân sách

- Dịch vụ AWS:

  - EC2 instances: 6 × t4g.nano (6 × 0.0042 × 720h): $18.14
  - EBS: 6 × 20 GB = 120 GB × $0.10/GB: $12.00
  - Load Balancer (ALB): 1 ALB, lưu lượng nhẹ: $15.00
  - NAT instance: 1 × t4g.nano (thay thế NAT Gateway): $3.02
  - DynamoDB: 10 GB, on-demand nhỏ: $5.00
  - Data transfer OUT: 100 GB × $0.09/GB: $9.00
  - CloudWatch & misc: Nhật ký (logs) + chỉ số cơ bản (basic metrics): $3.00


Tổng cộng: ≈ $69.14 / tháng