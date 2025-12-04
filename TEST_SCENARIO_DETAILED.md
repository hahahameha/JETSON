# KỊCH BẢN KIỂM THỬ HỆ THỐNG

## Hệ Thống Phát Hiện Bệnh Lúa Sử Dụng Deep Learning Trên Jetson Nano

---

## PHẦN I: TỔNG QUAN

### 1.1. Mục Đích Kiểm Thử

Đánh giá toàn diện hiệu năng và độ tin cậy của hệ thống phát hiện bệnh lúa triển khai trên nền tảng NVIDIA Jetson Nano, bao gồm:

1. **Hiệu năng xử lý:** Đo lường thời gian xử lý từng giai đoạn trong pipeline
2. **Tài nguyên hệ thống:** Giám sát CPU, GPU, bộ nhớ, nhiệt độ, công suất
3. **Độ tin cậy:** Đánh giá tỷ lệ thành công và xử lý lỗi
4. **Throughput:** Đo lường số lượng ảnh xử lý được trong một đơn vị thời gian
5. **Khả năng mở rộng:** Kiểm tra hiệu năng với khối lượng dữ liệu khác nhau

### 1.2. Phạm Vi Kiểm Thử

**Các thành phần được kiểm thử:**
- Module upload ảnh lên Firebase Storage
- Module download ảnh từ Firebase Storage
- Module tiền xử lý ảnh (preprocessing)
- Module suy luận AI (inference) sử dụng TensorRT
- Module hậu xử lý (postprocessing)
- Hệ thống giám sát hiệu năng (performance monitoring)

**Không thuộc phạm vi:**
- Kiểm thử độ chính xác mô hình AI (accuracy testing)
- Kiểm thử bảo mật (security testing)
- Kiểm thử giao diện người dùng (UI testing)

### 1.3. Tiêu Chí Chấp Nhận

| Chỉ Số | Mục Tiêu | Chấp Nhận Được | Không Đạt |
|--------|----------|----------------|-----------|
| Thời gian inference | < 150ms | 150-200ms | > 200ms |
| Thời gian E2E | < 2500ms | 2500-3500ms | > 3500ms |
| Tỷ lệ thành công | 100% | ≥ 95% | < 95% |
| Sử dụng GPU | 75-85% | 70-90% | < 70% hoặc > 95% |
| Nhiệt độ CPU/GPU | < 60°C | 60-70°C | > 70°C |
| Throughput (E2E) | > 0.5 FPS | 0.3-0.5 FPS | < 0.3 FPS |

---

## PHẦN II: MÔI TRƯỜNG KIỂM THỬ

### 2.1. Cấu Hình Phần Cứng

**Thiết bị chính:**
```
Tên thiết bị: NVIDIA Jetson Nano Developer Kit
CPU: Quad-core ARM Cortex-A57 @ 1.43 GHz
GPU: 128-core NVIDIA Maxwell
RAM: 4GB 64-bit LPDDR4 @ 1600 MHz
Storage: 32GB microSD (Class 10, UHS-I)
Nguồn: 5V/4A DC
```

**Thiết bị phụ:**
```
Máy tính phát triển: Windows 11
Kết nối mạng: WiFi 802.11ac / Ethernet Gigabit
Router: Băng thông tối thiểu 50 Mbps
```

### 2.2. Cấu Hình Phần Mềm

**Hệ điều hành và framework:**
```
OS: Ubuntu 18.04.6 LTS (L4T R32.7.1)
Kernel: Linux 4.9.337-tegra
Python: 3.6.9
CUDA: 10.2.300
cuDNN: 8.2.1
TensorRT: 8.2.1.8
OpenCV: 4.5.3
```

**Thư viện Python:**
```
tensorflow: 2.7.0
numpy: 1.19.5
firebase-admin: 5.2.0
psutil: 5.9.0
jetson-stats: 4.2.0
```

### 2.3. Dịch Vụ Cloud

```
Platform: Google Firebase
Project ID: rice-813b5
Region: asia-southeast1
Storage: Firebase Storage
Database: Firebase Realtime Database
```

### 2.4. Bộ Dữ Liệu Kiểm Thử

**Thông tin chung:**
```
Tổng số ảnh: 264 ảnh
Nguồn: Bộ dữ liệu validation
Định dạng: JPEG
Độ phân giải: 640x480 đến 1920x1080 pixels
Kích thước file: 100KB - 2MB
```

**Phân bố theo lớp:**
```
├── Brown Spot (Đốm nâu): 88 ảnh (33.3%)
├── Healthy (Khỏe mạnh): 88 ảnh (33.3%)
└── Leaf Blast (Đạo ôn lá): 88 ảnh (33.3%)
```

**Vị trí lưu trữ:**
```
Đường dẫn: ~/rice_disease/web/test_images/
Cấu trúc:
├── brown_val (1).jpg đến brown_val (88).jpg
├── healthy_val (1).jpg đến healthy_val (88).jpg
└── leaf_blast_val (1).jpg đến leaf_blast_val (88).jpg
```

---

## PHẦN III: PHƯƠNG PHÁP KIỂM THỬ

### 3.1. Công Cụ Kiểm Thử

**Script kiểm thử chính:**
```bash
run_complete_test.py
```

**Chức năng:**
- Tự động khởi tạo Firebase connection
- Khởi động performance monitoring
- Chạy prediction listener ở background
- Upload và xử lý ảnh tuần tự
- Thu thập metrics chi tiết
- Tạo báo cáo tổng hợp

**Module hỗ trợ:**
```
performance_monitor.py: Thu thập metrics hệ thống
predict_listener_monitored.py: Xử lý prediction với monitoring
predict_with_monitoring.py: Test local không qua Firebase
```

### 3.2. Metrics Thu Thập

**Timing Metrics (đơn vị: milliseconds):**

1. `t_upload`: Thời gian upload ảnh lên Firebase Storage
2. `t_download`: Thời gian download ảnh từ Firebase Storage
3. `t_load`: Thời gian load ảnh vào memory
4. `t_preprocess`: Thời gian tiền xử lý ảnh
5. `t_inference`: Thời gian suy luận AI
6. `t_postprocess`: Thời gian hậu xử lý
7. `t_e2e`: Tổng thời gian end-to-end

**System Metrics:**
1. `cpu_usage`: Mức sử dụng CPU (%)
2. `memory_usage`: Mức sử dụng RAM (%)
3. `gpu_usage`: Mức sử dụng GPU (%)
4. `gpu_freq`: Tần số GPU (MHz)
5. `cpu_temp`: Nhiệt độ CPU (°C)
6. `gpu_temp`: Nhiệt độ GPU (°C)
7. `power`: Công suất tiêu thụ (W)

**Performance Metrics:**
1. `success_rate`: Tỷ lệ thành công (%)
2. `throughput`: Số ảnh xử lý/giây (FPS)
3. `latency_p50`: Độ trễ trung vị (ms)
4. `latency_p95`: Độ trễ percentile 95 (ms)
5. `latency_p99`: Độ trễ percentile 99 (ms)

### 3.3. Công Thức Tính Toán

**Thời gian End-to-End:**
```
t_e2e = t_upload + t_request + t_processing + t_parse
```

**Thời gian xử lý Jetson:**
```
t_jetson = t_download + t_load + t_preprocess + t_inference + t_postprocess
```

**Throughput:**
```
Throughput (FPS) = 1000 / t_average (ms)
```

**Tỷ lệ thành công:**
```
Success Rate (%) = (Số ảnh thành công / Tổng số ảnh) × 100
```

**Độ lệch chuẩn:**
```
σ = √(Σ(xi - μ)² / N)
```

---

## PHẦN IV: CÁC KỊCH BẢN KIỂM THỬ

### 4.1. Kịch Bản 1: Kiểm Thử Đơn Lẻ (Single Image Test)

**Mục đích:** Xác minh hệ thống hoạt động đúng với một ảnh đơn lẻ

**Điều kiện tiên quyết:**
- Jetson Nano đã được cấu hình đầy đủ
- Kết nối mạng ổn định
- Firebase đã được khởi tạo
- Có ít nhất 1 ảnh trong thư mục test

**Các bước thực hiện:**

```bash
# Bước 1: Kết nối SSH đến Jetson Nano
ssh jetson@<JETSON_IP>

# Bước 2: Di chuyển đến thư mục làm việc
cd ~/rice_disease/web

# Bước 3: Kiểm tra file ảnh test
ls -lh test_images/ | head -5

# Bước 4: Chạy kiểm thử với 1 ảnh
python3 run_complete_test.py --folder ./test_images --max 1

# Bước 5: Kiểm tra kết quả
cat complete_test_results.json | python3 -m json.tool
```

**Kết quả mong đợi:**
- Hệ thống upload ảnh thành công
- Listener nhận và xử lý request
- Trả về kết quả dự đoán chính xác
- Timing breakdown hiển thị đầy đủ
- Không có lỗi trong quá trình xử lý
- File kết quả được tạo: `complete_test_results.json`, `.csv`, `.txt`

**Thời gian thực hiện:** 2-3 phút

**Tiêu chí đánh giá:**
- ✅ Pass: Tất cả bước thực hiện thành công, có kết quả dự đoán
- ❌ Fail: Có lỗi xảy ra hoặc không có kết quả


### 4.2. Kịch Bản 2: Kiểm Thử Quy Mô Nhỏ (Small Batch Test)

**Mục đích:** Đánh giá tính ổn định với 10 ảnh

**Điều kiện tiên quyết:**
- Kịch bản 1 đã pass
- Có ít nhất 10 ảnh trong thư mục test

**Các bước thực hiện:**

```bash
# Chạy test với 10 ảnh
python3 run_complete_test.py --folder ./test_images --max 10

# Quan sát metrics trong quá trình chạy
# Kiểm tra nhiệt độ
jtop

# Sau khi hoàn thành, xem báo cáo
cat test_summary_report.txt
```

**Metrics cần ghi nhận:**

| Metric | Giá Trị Đo Được | Ghi Chú |
|--------|-----------------|---------|
| Thời gian trung bình (ms) | _________ | |
| Thời gian min (ms) | _________ | |
| Thời gian max (ms) | _________ | |
| Độ lệch chuẩn (ms) | _________ | |
| Tỷ lệ thành công (%) | _________ | |
| CPU usage trung bình (%) | _________ | |
| GPU usage trung bình (%) | _________ | |
| Nhiệt độ CPU max (°C) | _________ | |
| Nhiệt độ GPU max (°C) | _________ | |
| Công suất trung bình (W) | _________ | |

**Kết quả mong đợi:**
- Success rate: 100%
- Thời gian trung bình: 2000-3000ms
- Inference time: 80-150ms
- GPU usage: 70-90%
- Nhiệt độ: < 60°C
- Không có thermal throttling

**Thời gian thực hiện:** 5-10 phút

**Tiêu chí đánh giá:**
- ✅ Pass: Success rate ≥ 90%, không có lỗi nghiêm trọng
- ⚠️ Warning: Success rate 80-90%, có cảnh báo nhiệt độ
- ❌ Fail: Success rate < 80% hoặc hệ thống crash


### 4.3. Kịch Bản 3: Kiểm Thử Quy Mô Trung Bình (Medium Batch Test)

**Mục đích:** Đánh giá hiệu năng chi tiết với 50 ảnh

**Điều kiện tiên quyết:**
- Kịch bản 2 đã pass
- Jetson Nano đã nguội (nhiệt độ < 50°C)

**Các bước thực hiện:**

```bash
# Kiểm tra nhiệt độ trước khi test
jtop

# Chạy test 50 ảnh
python3 run_complete_test.py --folder ./test_images --max 50

# Trong quá trình chạy, mở terminal khác để monitor
# Terminal 2:
watch -n 1 'jtop --json | jq ".stats"'
```

**Phân tích kết quả:**

**Bảng 1: Timing Breakdown (Trung bình)**

| Giai Đoạn | Thời Gian (ms) | Tỷ Lệ (%) | Mô Tả |
|-----------|----------------|-----------|-------|
| Upload lên Firebase | _________ | _____ | Upload ảnh lên cloud storage |
| Download từ Firebase | _________ | _____ | Download ảnh từ cloud storage |
| Load ảnh | _________ | _____ | Đọc ảnh vào memory |
| Tiền xử lý | _________ | _____ | Resize 224x224, normalize [0,1] |
| AI Inference | _________ | _____ | Chạy mô hình trên GPU |
| Hậu xử lý | _________ | _____ | Softmax, parse kết quả |
| **Tổng cộng** | **_________** | **100%** | **End-to-end latency** |

**Bảng 2: Statistical Analysis**

| Giai Đoạn | Min (ms) | Max (ms) | Mean (ms) | Std Dev (ms) |
|-----------|----------|----------|-----------|--------------|
| Upload | _____ | _____ | _____ | _____ |
| Download | _____ | _____ | _____ | _____ |
| Inference | _____ | _____ | _____ | _____ |
| Total E2E | _____ | _____ | _____ | _____ |

**Bảng 3: System Resources**

| Metric | Min | Max | Mean | Unit |
|--------|-----|-----|------|------|
| CPU Usage | _____ | _____ | _____ | % |
| Memory Usage | _____ | _____ | _____ | % |
| GPU Usage | _____ | _____ | _____ | % |
| GPU Frequency | _____ | _____ | _____ | MHz |
| CPU Temperature | _____ | _____ | _____ | °C |
| GPU Temperature | _____ | _____ | _____ | °C |
| Power Consumption | _____ | _____ | _____ | W |

**Thời gian thực hiện:** 20-30 phút

**Tiêu chí đánh giá:**
- ✅ Pass: Đạt tất cả tiêu chí mục tiêu
- ⚠️ Warning: Đạt tiêu chí chấp nhận được
- ❌ Fail: Không đạt tiêu chí tối thiểu


### 4.4. Kịch Bản 4: Kiểm Thử Quy Mô Lớn (Large Batch Test)

**Mục đích:** Đánh giá toàn diện với 100 ảnh

**Điều kiện tiên quyết:**
- Kịch bản 3 đã pass
- Đủ dung lượng lưu trữ cho log files
- Kết nối mạng ổn định

**Các bước thực hiện:**

```bash
# Xóa log cũ
rm -f complete_test_results.*

# Chạy test 100 ảnh
python3 run_complete_test.py --folder ./test_images --max 100 2>&1 | tee test_100_images.log

# Sau khi hoàn thành, phân tích kết quả
python3 analyze_results.py complete_test_results.json
```

**Phân tích chi tiết:**

**1. Bottleneck Analysis**

Xác định giai đoạn chiếm thời gian nhiều nhất:

```
Nếu t_download > 50% của t_e2e:
    → Bottleneck: Network download
    → Giải pháp: Tối ưu băng thông, cache, CDN

Nếu t_upload > 30% của t_e2e:
    → Bottleneck: Network upload  
    → Giải pháp: Compress ảnh, tối ưu băng thông

Nếu t_inference > 20% của t_e2e:
    → Bottleneck: AI processing
    → Giải pháp: Tối ưu model, quantization
```

**2. Performance Trend Analysis**

Vẽ biểu đồ thời gian xử lý theo số thứ tự ảnh:

```
Nếu thời gian tăng dần:
    → Có thể có memory leak hoặc thermal throttling
    
Nếu thời gian ổn định:
    → Hệ thống hoạt động tốt
    
Nếu thời gian giảm dần:
    → Có thể do cache warming up
```

**3. Resource Utilization**

```
GPU Usage:
- < 70%: Underutilized, có thể tăng batch size
- 70-90%: Optimal
- > 90%: Có thể bị bottleneck

Temperature:
- < 60°C: Excellent
- 60-70°C: Good
- > 70°C: Risk of throttling
```

**Kết quả mong đợi:**

```
Success Rate: ≥ 95%
Average E2E Time: 2000-3000ms
Average Inference: 80-150ms
Throughput (E2E): 0.3-0.5 FPS
Throughput (Jetson only): 8-12 FPS
GPU Utilization: 75-85%
CPU Temperature: < 65°C
GPU Temperature: < 60°C
Power Consumption: 4-6W
No thermal throttling
No memory leaks
```

**Thời gian thực hiện:** 40-60 phút


### 4.5. Kịch Bản 5: Kiểm Thử Stress (Stress Test)

**Mục đích:** Đánh giá giới hạn và độ bền của hệ thống

**Điều kiện tiên quyết:**
- Tất cả kịch bản trước đã pass
- Có đủ thời gian (2-3 giờ)
- Giám sát liên tục

**Các bước thực hiện:**

```bash
# Test tất cả 264 ảnh
python3 run_complete_test.py --folder ./test_images --max 264

# Hoặc test lặp lại nhiều lần
for i in {1..3}; do
    echo "=== Run $i/3 ==="
    python3 run_complete_test.py --folder ./test_images --max 100
    echo "Waiting 60s for cooldown..."
    sleep 60
done
```

**Metrics cần quan sát:**

1. **Thermal Behavior:**
   - Nhiệt độ có tăng liên tục không?
   - Có xảy ra thermal throttling không?
   - Thời gian để nhiệt độ ổn định?

2. **Memory Behavior:**
   - Memory usage có tăng dần không?
   - Có memory leak không?
   - Garbage collection hoạt động tốt không?

3. **Performance Degradation:**
   - Thời gian xử lý có tăng theo thời gian không?
   - Throughput có giảm không?
   - Error rate có tăng không?

**Kết quả mong đợi:**
- Success rate: ≥ 95% trong suốt quá trình
- Không có thermal throttling
- Không có memory leak
- Performance ổn định
- Nhiệt độ < 70°C
- Hệ thống không crash

**Thời gian thực hiện:** 2-3 giờ

**Tiêu chí đánh giá:**
- ✅ Pass: Hệ thống hoạt động ổn định, không có degradation
- ⚠️ Warning: Có thermal throttling nhẹ hoặc performance giảm < 10%
- ❌ Fail: Hệ thống crash, memory leak, hoặc performance giảm > 20%


---

## PHẦN V: PHÂN TÍCH KẾT QUẢ

### 5.1. Mẫu Báo Cáo Kết Quả

**Bảng 4: Kết Quả Tổng Hợp Các Kịch Bản**

| Kịch Bản | Số Ảnh | Success Rate | Avg Time (ms) | Avg Inference (ms) | Throughput (FPS) | Kết Quả |
|----------|--------|--------------|---------------|-------------------|------------------|---------|
| KB1: Đơn lẻ | 1 | _____ % | _____ | _____ | _____ | ☐ Pass ☐ Fail |
| KB2: Nhỏ | 10 | _____ % | _____ | _____ | _____ | ☐ Pass ☐ Fail |
| KB3: Trung bình | 50 | _____ % | _____ | _____ | _____ | ☐ Pass ☐ Fail |
| KB4: Lớn | 100 | _____ % | _____ | _____ | _____ | ☐ Pass ☐ Fail |
| KB5: Stress | 264 | _____ % | _____ | _____ | _____ | ☐ Pass ☐ Fail |

### 5.2. Phân Tích Timing Breakdown

**Bảng 5: Phân Tích Chi Tiết Thời Gian Xử Lý (Kịch Bản 4 - 100 ảnh)**

| Giai Đoạn | Min (ms) | Max (ms) | Mean (ms) | Std Dev | Tỷ Lệ (%) | Đánh Giá |
|-----------|----------|----------|-----------|---------|-----------|----------|
| Upload | _____ | _____ | _____ | _____ | _____ | ☐ Tốt ☐ TB ☐ Kém |
| Download | _____ | _____ | _____ | _____ | _____ | ☐ Tốt ☐ TB ☐ Kém |
| Load | _____ | _____ | _____ | _____ | _____ | ☐ Tốt ☐ TB ☐ Kém |
| Preprocess | _____ | _____ | _____ | _____ | _____ | ☐ Tốt ☐ TB ☐ Kém |
| Inference | _____ | _____ | _____ | _____ | _____ | ☐ Tốt ☐ TB ☐ Kém |
| Postprocess | _____ | _____ | _____ | _____ | _____ | ☐ Tốt ☐ TB ☐ Kém |
| **Total E2E** | **_____** | **_____** | **_____** | **_____** | **100%** | **☐ Tốt ☐ TB ☐ Kém** |

**Tiêu chí đánh giá:**
- Tốt: Đạt mục tiêu
- TB (Trung bình): Chấp nhận được
- Kém: Không đạt

### 5.3. Phân Tích Tài Nguyên Hệ Thống

**Bảng 6: Sử Dụng Tài Nguyên (Kịch Bản 4 - 100 ảnh)**

| Tài Nguyên | Min | Max | Mean | Mục Tiêu | Đạt/Không |
|------------|-----|-----|------|----------|-----------|
| CPU Usage (%) | _____ | _____ | _____ | 40-60% | ☐ Đạt ☐ Không |
| Memory Usage (%) | _____ | _____ | _____ | < 80% | ☐ Đạt ☐ Không |
| GPU Usage (%) | _____ | _____ | _____ | 75-85% | ☐ Đạt ☐ Không |
| GPU Freq (MHz) | _____ | _____ | _____ | 921 | ☐ Đạt ☐ Không |
| CPU Temp (°C) | _____ | _____ | _____ | < 60 | ☐ Đạt ☐ Không |
| GPU Temp (°C) | _____ | _____ | _____ | < 60 | ☐ Đạt ☐ Không |
| Power (W) | _____ | _____ | _____ | 4-6 | ☐ Đạt ☐ Không |

### 5.4. Xác Định Bottleneck

**Phân tích bottleneck dựa trên tỷ lệ thời gian:**

```
Bottleneck chính: _________________ (_____ %)

Nguyên nhân:
☐ Network latency (Upload/Download)
☐ AI inference
☐ Preprocessing
☐ System resources
☐ Khác: _________________

Đề xuất giải pháp:
1. _________________________________
2. _________________________________
3. _________________________________
```


---

## PHẦN VI: KẾT LUẬN VÀ KHUYẾN NGHỊ

### 6.1. Tổng Kết Kết Quả

**Đánh giá tổng quan:**

```
Tổng số kịch bản: 5
Số kịch bản Pass: _____ / 5
Số kịch bản Fail: _____ / 5

Kết luận chung:
☐ Hệ thống đạt yêu cầu, sẵn sàng triển khai
☐ Hệ thống cần cải thiện một số điểm
☐ Hệ thống chưa đạt yêu cầu, cần tối ưu lại
```

### 6.2. Điểm Mạnh

```
1. _________________________________
2. _________________________________
3. _________________________________
```

### 6.3. Điểm Cần Cải Thiện

```
1. _________________________________
   Mức độ: ☐ Cao ☐ Trung bình ☐ Thấp
   
2. _________________________________
   Mức độ: ☐ Cao ☐ Trung bình ☐ Thấp
   
3. _________________________________
   Mức độ: ☐ Cao ☐ Trung bình ☐ Thấp
```

### 6.4. Khuyến Nghị Tối Ưu

**Tối ưu ngắn hạn (1-2 tuần):**
1. _________________________________
2. _________________________________
3. _________________________________

**Tối ưu trung hạn (1-2 tháng):**
1. _________________________________
2. _________________________________
3. _________________________________

**Tối ưu dài hạn (3-6 tháng):**
1. _________________________________
2. _________________________________
3. _________________________________

### 6.5. So Sánh Với Các Nghiên Cứu Liên Quan

**Bảng 7: So Sánh Hiệu Năng**

| Nghiên Cứu | Platform | Inference Time | Throughput | Accuracy |
|------------|----------|----------------|------------|----------|
| Hệ thống này | Jetson Nano | _____ ms | _____ FPS | _____ % |
| Nghiên cứu A | Jetson Xavier | 50 ms | 20 FPS | 95% |
| Nghiên cứu B | Raspberry Pi 4 | 300 ms | 3 FPS | 92% |
| Nghiên cứu C | Cloud GPU | 30 ms | 33 FPS | 97% |

**Nhận xét:**
```
_________________________________
_________________________________
_________________________________
```

---

## PHẦN VII: PHỤ LỤC

### 7.1. Danh Sách Lệnh Hữu Ích

**Giám sát hệ thống:**
```bash
# Xem GPU/CPU real-time
jtop

# Xem nhiệt độ
cat /sys/devices/virtual/thermal/thermal_zone*/temp

# Xem power mode
sudo nvpmodel -q

# Set max performance
sudo jetson_clocks

# Enable fan
sudo jetson_clocks --fan
```

**Kiểm tra kết nối:**
```bash
# Test network
ping -c 10 firebasestorage.googleapis.com

# Test bandwidth
speedtest-cli

# Check Firebase connection
python3 -c "from firebase_uploader import init_firebase; init_firebase()"
```

**Phân tích kết quả:**
```bash
# View JSON
cat complete_test_results.json | python3 -m json.tool | less

# View CSV
column -t -s, complete_test_results.csv | less

# Export to Excel
libreoffice complete_test_results.csv
```

### 7.2. Troubleshooting

**Vấn đề 1: Timeout khi xử lý**
```
Nguyên nhân: Network chậm hoặc Jetson quá tải
Giải pháp:
1. Tăng timeout: --timeout 60
2. Kiểm tra network: ping, speedtest
3. Giảm số ảnh test: --max 50
```

**Vấn đề 2: Out of Memory**
```
Nguyên nhân: Memory leak hoặc batch size quá lớn
Giải pháp:
1. Restart Jetson: sudo reboot
2. Giảm số ảnh: --max 50
3. Clear cache: sync; echo 3 > /proc/sys/vm/drop_caches
```

**Vấn đề 3: Nhiệt độ cao (> 70°C)**
```
Nguyên nhân: Tản nhiệt không đủ
Giải pháp:
1. Enable fan: sudo jetson_clocks --fan
2. Giảm power mode: sudo nvpmodel -m 1
3. Thêm tản nhiệt
4. Cải thiện thông gió
```

**Vấn đề 4: GPU utilization thấp (< 70%)**
```
Nguyên nhân: Bottleneck ở CPU hoặc I/O
Giải pháp:
1. Kiểm tra CPU usage
2. Tối ưu preprocessing
3. Sử dụng batch processing
```

### 7.3. Checklist Trước Khi Test

```
☐ Jetson Nano đã được cấu hình đầy đủ
☐ Kết nối mạng ổn định (ping < 50ms)
☐ Firebase đã được khởi tạo
☐ Có đủ dung lượng lưu trữ (> 1GB free)
☐ Nhiệt độ ban đầu < 50°C
☐ Power mode đã được set (nvpmodel -m 0)
☐ Jetson clocks đã được enable
☐ Có đủ ảnh test trong thư mục
☐ Đã backup dữ liệu quan trọng
☐ Có đủ thời gian để chạy test
```

### 7.4. Tài Liệu Tham Khảo

1. JETSON_MONITORING_GUIDE.md - Hướng dẫn chi tiết monitoring
2. QUICK_START.md - Hướng dẫn nhanh
3. DEPLOYMENT_CHECKLIST.md - Checklist triển khai
4. MONITORING_SUMMARY.md - Tổng quan monitoring
5. TEST_SCENARIO_DETAILED.md - Kịch bản test chi tiết

---

## PHẦN VIII: PHIẾU GHI NHẬN KẾT QUẢ

**Thông tin chung:**
```
Người thực hiện: _______________________
Ngày thực hiện: _______________________
Thời gian bắt đầu: _______________________
Thời gian kết thúc: _______________________
Tổng thời gian: _______________________
```

**Kết quả tổng hợp:**
```
Kịch bản 1 (1 ảnh):     ☐ Pass  ☐ Fail
Kịch bản 2 (10 ảnh):    ☐ Pass  ☐ Fail
Kịch bản 3 (50 ảnh):    ☐ Pass  ☐ Fail
Kịch bản 4 (100 ảnh):   ☐ Pass  ☐ Fail
Kịch bản 5 (264 ảnh):   ☐ Pass  ☐ Fail

Tổng kết: ☐ Pass  ☐ Fail  ☐ Cần xem xét
```

**Ghi chú:**
```
_________________________________
_________________________________
_________________________________
_________________________________
```

**Chữ ký xác nhận:**
```
Người thực hiện: _______________________

Người kiểm tra: _______________________

Ngày: _______________________
```

---

**HẾT**
