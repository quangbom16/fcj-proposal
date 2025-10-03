---
title: "Nền tảng Phân tích Mã độc & Tình báo Mối đe dọa"
date: "2025-10-02"
draft: false
---

## Giải pháp Web hợp nhất cho việc Phân tích Tệp tin và URL đáng ngờ

### 1. Tóm tắt điều hành
Nền tảng Phân tích Mã độc & Tình báo Mối đe dọa được phát triển dành cho một nhóm nghiên cứu tại một trường đại học ở TP. Hồ Chí Minh, với mục tiêu nâng cao khả năng phát hiện mã độc và chia sẻ tình báo an ninh mạng. Nền tảng cho phép người dùng tải lên các tệp tin, tên miền, địa chỉ IP và URL nghi ngờ để phân tích tự động. Lấy cảm hứng từ VirusTotal, hệ thống tích hợp nhiều công cụ quét và nguồn tình báo mã nguồn mở (OSINT), nhưng được triển khai ở quy mô nhỏ hơn, phù hợp cho hoạt động học tập và nghiên cứu. Hệ thống được thiết kế để phục vụ 10–15 nhà nghiên cứu thường xuyên, cung cấp khả năng phát hiện thời gian thực, chia sẻ kết quả và truy cập an toàn dựa trên phân quyền.

### 2. Vấn đề đặt ra
#### Vấn đề
Các phương pháp phân tích mã độc truyền thống thường đòi hỏi nhiều công cụ rời rạc, phải thực hiện thủ công và thiếu tính tập trung trong việc tổng hợp kết quả. Trong khi đó, các dịch vụ thương mại như VirusTotal tuy mạnh mẽ nhưng lại vướng phải các hạn chế về quyền riêng tư, giới hạn truy cập hoặc chi phí cao. Điều này gây khó khăn cho sinh viên và nhà nghiên cứu khi cần một môi trường được kiểm soát để tiến hành thí nghiệm và học tập về an ninh mạng.

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

#### Lợi ích và Hiệu quả đầu tư
Nền tảng này là một **công cụ phục vụ giảng dạy và nghiên cứu an ninh mạng**, giúp sinh viên và nhà nghiên cứu thực hành trực tiếp với quy trình phát hiện mã độc, đồng thời mở rộng hiểu biết về tình báo mối đe dọa. Việc tập trung hóa nhiều công cụ trong một hệ thống duy nhất vừa giảm thiểu thao tác thủ công, vừa nâng cao độ chính xác và tính ổn định.

**Lợi ích nổi bật:**
- Cung cấp môi trường thực hành trực tiếp cho sinh viên và nhà nghiên cứu.  
- Rút ngắn thời gian phát hiện nhờ quét tự động theo thời gian thực.  
- Tạo nền tảng để tích hợp các mô hình phát hiện dựa trên AI trong tương lai.  
- Hỗ trợ chia sẻ báo cáo an toàn và ẩn danh, thúc đẩy hợp tác nghiên cứu.  

Nhờ giảm thiểu các bước thủ công và hợp nhất dữ liệu, hệ thống có thể hoàn vốn sau 6–12 tháng, đồng thời tạo ra giá trị lâu dài cho hoạt động nghiên cứu và giảng dạy.

### 3. Kiến trúc giải pháp

<div style="display: flex; justify-content: center;">
  <iframe src="/high-level-view-2.drawio.html?v=1" width="900" height="300" style="border:none;"></iframe>
</div>

<div style="display: flex; justify-content: center; margin-top:20px;">
  <iframe src="/high-level-view.drawio.html" width="800" height="1100" style="border:none;"></iframe>
</div>

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

**Kế hoạch dự án**
- **Trước kỳ thực tập (Tháng 0):** 1 tháng cho việc lên kế hoạch và đánh giá hạ tầng cũ.  
- **Trong kỳ thực tập (Tháng 1–3):** 3 tháng.  
  - Tháng 1: Nghiên cứu AWS và nâng cấp phần cứng.  
  - Tháng 2: Thiết kế và điều chỉnh kiến trúc.  
  - Tháng 3: Triển khai, kiểm thử và đưa hệ thống vào hoạt động.  
- **Sau khi ra mắt:** Tiếp tục nghiên cứu và cải tiến trong vòng 1 năm.  
