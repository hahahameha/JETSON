# Kịch Bản Kiểm Thử Chi Tiết - Hệ Thống Phát Hiện Bệnh Lúa

## Mục Tiêu Kiểm Thử

Đánh giá toàn diện hiệu năng hệ thống AI phát hiện bệnh lúa trên Jetson Nano, bao gồm:
- Thời gian xử lý từng giai đoạn
- Hiệu suất GPU/CPU
- Độ chính xác dự đoán
- Throughput (số ảnh/giây)
- Tài nguyên hệ thống (nhiệt độ, công suất)

## Môi Trường Kiểm Thử

### Phần Cứng
- **Thiết bị:** NVIDIA Jetson Nano Developer Kit
- **CPU:** Quad-core ARM A57 @ 1.43 GHz
- **GPU:** 128-core Maxwell
- **RAM:** 4GB LPDDR4
- **Storage:** 32GB+ SD Card

### Phần Mềm
- **OS:** Ubuntu 18.04 (L4T)
- **CUDA:** 10.2.300
- **TensorRT:** 8.2.1.8
- **Python:** 3.6.9
- **Framework:** TensorFlow/TensorRT

### Mạng
- **Kết nối:** WiFi/Ethernet
- **Firebase Region:** asia-southeast1
- **Băng thông:** Tối thiểu 10 Mbps

## Chuẩn Bị Dữ Liệu

### Bộ Dữ Liệu Test
```
Tổng số ảnh: 264 ảnh
├── Brown Spot: 88 ảnh
├── Healthy: 88 ảnh
└── Leaf Blast: 88 ảnh

Định dạng: JPG
Kích thước: 640x480 - 1920x1080
Dung lượng: 100KB - 2MB/ảnh
```

### Vị Trí Dữ Liệu
```bash
Jetson Nano: ~/rice_disease/web/test_images/
├── brown_val (1).jpg
├── brown_val (2).jpg
├── ...
├── healthy_val (1).jpg
├── ...
└── leaf_blast_val (1).jpg
```

## Kịch Bản Test

### Test Case 1: Kiểm Thử Đơn Lẻ (Single Image Test)

**Mục đích:** Xác minh hệ thống hoạt động đúng với 1 ảnh

**Các bước thực hiện:**

```bash
# Bước 1: SSH vào Jetson
ssh jetson@<JETSON_IP>

# Bước 2: Di chuyển đến thư mục
cd ~/rice_disease/web

# Bước 3: Chạy test đơn lẻ
python3 run_complete_test.py --folder ./test_images --max 1

# Bước 4: Quan sát output
```

**Kết quả mong đợi:**
```
✅ Upload thành công
✅ Listener nhận và xử lý
✅ Trả về kết quả dự đoán
✅ Timing breakdown đầy đủ
✅ Không có lỗi
```

**Thời gian:** 2-3 phút

---

### Test Case 2: Kiểm Thử Nhỏ (Small Batch Test)

**Mục đích:** Kiểm tra tính ổn định với 10 ảnh

**Các bước thực hiện:**

```bash
# Chạy test 10 ảnh
python3 run_complete_test.py --folder ./test_images --max 10
```

**Metrics cần thu thập:**
- Thời gian trung bình/ảnh
- Thời gian min/max
- Success rate
- CPU/GPU usage
- Nhiệt độ CPU/GPU

**Kết quả mong đợi:**
```
Success Rate: 100%
Average Time: 2000-3000ms
Inference Time: 80-150ms
GPU Usage: 70-90%
Temperature: < 60°C
```

**Thời gian:** 5-10 phút

---

### Test Case 3: Kiểm Thử Trung Bình (Medium Batch Test)

**Mục đích:** Đánh giá hiệu năng với 50 ảnh

**Các bước thực hiện:**

```bash
# Chạy test 50 ảnh
python3 run_complete_test.py --folder ./test_images --max 50
```

**Metrics cần thu thập:**
- Timing breakdown chi tiết
- Statistical analysis (mean, std, min, max)
- System metrics over time
- Thermal throttling (nếu có)

**Kết quả mong đợi:**
```
Success Rate: > 95%
Average E2E Time: 2000-3000ms
Average Inference: 80-150ms
Throughput: 0.3-0.5 FPS (E2E)
GPU Utilization: 75-85%
CPU Temperature: 50-65°C
GPU Temperature: 45-60°C
Power Consumption: 4-6W
```

**Thời gian:** 20-30 phút

---

### Test Case 4: Kiểm Thử Đầy Đủ (Full Batch Test)

**Mục đích:** Đánh giá toàn diện với 100 ảnh

**Các bước thực hiện:**

```bash
# Chạy test 100 ảnh
python3 run_complete_test.py --folder ./test_images --max 100

# Hoặc test tất cả 264 ảnh
python3 run_complete_test.py --folder ./test_images --max 264
```

**Metrics cần thu thập:**

1. **Timing Metrics:**
   - Upload time (ms)
   - Download time (ms)
   - Load time (ms)
   - Preprocessing time (ms)
   - Inference time (ms)
   - Postprocessing time (ms)
   - Total E2E time (ms)

2. **System Metrics:**
   - CPU usage (%)
   - Memory usage (%)
   - GPU usage (%)
   - GPU frequency (MHz)
   - CPU temperature (°C)
   - GPU temperature (°C)
   - Power consumption (W)

3. **Performance Metrics:**
   - Success rate (%)
   - Throughput (FPS)
   - Average latency (ms)
   - P50, P95, P99 latency (ms)

**Kết quả mong đợi:**

```
================================================================================
PERFORMANCE REPORT - JETSON NANO
================================================================================

📊 Summary:
   Total Inferences: 100
   Total Time: 250.00s
   Average Time: 2500.00ms
   Min Time: 2000.00ms
   Max Time: 3500.00ms
   Throughput: 0.4 FPS

📈 Stage Breakdown (Average):

┌─────────────────────────────────────────┬──────────────┬──────────┬────────────────────────────────────┐
│ Giai đoạn                               │ Thời gian    │ Tỷ lệ    │ Mô tả                              │
├─────────────────────────────────────────┼──────────────┼──────────┼────────────────────────────────────┤
│ Upload lên Firebase Storage             │    500.00ms  │   20.0%  │ Upload ảnh lên cloud storage       │
│ Download từ Firebase Storage            │    750.00ms  │   30.0%  │ Download ảnh từ cloud storage      │
│ Load ảnh                                │     45.00ms  │    1.8%  │ Đọc ảnh từ file                    │
│ Tiền xử lý ảnh                          │     15.00ms  │    0.6%  │ Resize 224x224, normalize [0,1]    │
│ AI Inference (TensorRT)                 │    110.00ms  │    4.4%  │ Chạy mô hình trên GPU              │
│ Hậu xử lý & tạo JSON                    │      2.00ms  │    0.1%  │ Softmax, parse kết quả             │
│ Chờ xử lý                               │   1078.00ms  │   43.1%  │ Thời gian chờ và overhead          │
├─────────────────────────────────────────┼──────────────┼──────────┼────────────────────────────────────┤
│ Tổng cộng                               │   2500.00ms  │    100%  │ End-to-end latency                 │
└─────────────────────────────────────────┴──────────────┴──────────┴────────────────────────────────────┘

🖥️  System Metrics (Average):
   CPU Usage: 50.0%
   Memory Usage: 65.0% (2516 MB)

🎮 Jetson Metrics:
   GPU Usage: 80.0%
   GPU Frequency: 921 MHz
   CPU Temperature: 55.0°C
   GPU Temperature: 52.0°C
   Power Consumption: 5.0W

📈 STATISTICAL ANALYSIS
────────────────────────────────────────────────────────────────────────────────

Upload lên Firebase Storage:
  • Average: 500.00ms
  • Min: 350.00ms
  • Max: 800.00ms
  • Std Dev: 80.00ms

Download từ Firebase Storage:
  • Average: 750.00ms
  • Min: 500.00ms
  • Max: 1200.00ms
  • Std Dev: 120.00ms

AI Inference (TensorRT):
  • Average: 110.00ms
  • Min: 80.00ms
  • Max: 180.00ms
  • Std Dev: 15.00ms

================================================================================
📊 FINAL SUMMARY
================================================================================
✅ Successful: 100/100
❌ Failed: 0/100
📈 Success Rate: 100.0%
================================================================================
```

**Thời gian:** 40-60 phút

---

### Test Case 5: Kiểm Thử Stress (Stress Test)

**Mục đích:** Đánh giá giới hạn và độ ổn định của hệ thống

**Các bước thực hiện:**

```bash
# Test tất cả 264 ảnh liên tục
python3 run_complete_test.py --folder ./test_images --max 264

# Hoặc chạy nhiều lần
for i in {1..3}; do
    echo "Run $i/3"
    python3 run_complete_test.py --folder ./test_images --max 100
    sleep 60  # Nghỉ 1 phút giữa các lần
done
```

**Metrics cần quan sát:**
- Thermal throttling
- Memory leaks
- Performance degradation over time
- Error rate increase

**Kết quả mong đợi:**
```
Success Rate: > 95%
No thermal throttling
No memory leaks
Consistent performance
Temperature: < 70°C
```

**Thời gian:** 2-3 giờ

---

## Phân Tích Kết Quả

### 1. Timing Analysis

**Mục tiêu:**
- Upload time: < 600ms
- Download time: < 1000ms
- Inference time: < 200ms
- Total E2E: < 3000ms

**Công thức tính:**
```
Total E2E = Upload + Request + Processing + Parse
Processing = Download + Load + Preprocess + Inference + Postprocess
```

**Bottleneck Identification:**
```python
# Tính tỷ lệ từng giai đoạn
upload_pct = (upload_time / total_time) * 100
download_pct = (download_time / total_time) * 100
inference_pct = (inference_time / total_time) * 100

# Xác định bottleneck
if download_pct > 50:
    print("Bottleneck: Network download")
elif upload_pct > 30:
    print("Bottleneck: Network upload")
elif inference_pct > 20:
    print("Bottleneck: AI inference")
```

### 2. System Resource Analysis

**CPU Usage:**
- Idle: 10-20%
- During inference: 40-60%
- Peak: < 80%

**GPU Usage:**
- Idle: 0-5%
- During inference: 70-90%
- Peak: < 95%

**Memory Usage:**
- Baseline: 1.5-2.0 GB
- During inference: 2.0-2.5 GB
- Peak: < 3.5 GB

**Temperature:**
- Idle: 35-45°C
- During inference: 50-65°C
- Critical: > 70°C (thermal throttling)

**Power:**
- Idle: 2-3W
- During inference: 4-6W
- Peak: < 10W

### 3. Performance Metrics

**Throughput:**
```
Jetson Only (no network): ~10 FPS (100ms/image)
End-to-End (with network): ~0.4 FPS (2500ms/image)
```

**Latency Percentiles:**
```
P50 (Median): 2400ms
P95: 3000ms
P99: 3500ms
```

**Success Rate:**
```
Target: > 95%
Acceptable: > 90%
Critical: < 85%
```

---

## File Kết Quả

### 1. complete_test_results.json
```json
{
  "test_info": {
    "date": "2024-12-04",
    "total_images": 100,
    "success": 100,
    "failed": 0,
    "success_rate": 100.0
  },
  "metrics": [
    {
      "timestamp": "2024-12-04T10:30:45",
      "image": "brown_val (1).jpg",
      "class": "Brown_Spot",
      "confidence": 0.9567,
      "timing": {
        "upload_ms": 511.35,
        "download_ms": 769.10,
        "preprocessing_ms": 2.58,
        "inference_ms": 114.65,
        "postprocessing_ms": 0.06,
        "total_e2e_ms": 2474.47
      },
      "system": {
        "cpu_percent": 48.7,
        "gpu_percent": 82.3,
        "cpu_temp": 52.3,
        "gpu_temp": 49.7,
        "power": 5.12
      }
    }
  ]
}
```

### 2. complete_test_results.csv
```csv
Timestamp,Image,Class,Confidence,Upload,Download,Preproc,Inference,Postproc,Total,CPU%,GPU%,Temp
2024-12-04T10:30:45,brown_val (1).jpg,Brown_Spot,0.9567,511.35,769.10,2.58,114.65,0.06,2474.47,48.7,82.3,52.3
...
```

### 3. test_summary_report.txt
```
Báo cáo tổng hợp với bảng timing breakdown và statistical analysis
```

---

## Đánh Giá và Kết Luận

### Tiêu Chí Đánh Giá

| Metric | Target | Good | Acceptable | Poor |
|--------|--------|------|------------|------|
| Inference Time | < 150ms | < 120ms | 120-200ms | > 200ms |
| Total E2E Time | < 3000ms | < 2500ms | 2500-3500ms | > 3500ms |
| Success Rate | 100% | > 98% | 95-98% | < 95% |
| GPU Usage | 80-90% | 75-85% | 70-90% | < 70% or > 95% |
| Temperature | < 60°C | < 55°C | 55-65°C | > 65°C |
| Throughput (E2E) | > 0.5 FPS | > 0.4 FPS | 0.3-0.4 FPS | < 0.3 FPS |

### Phân Tích Bottleneck

**Network Bottleneck (Phổ biến nhất):**
```
Download: 750ms (30%)
Upload: 500ms (20%)
Total Network: 1250ms (50% của E2E)

Giải pháp:
- Sử dụng Firebase region gần hơn
- Tăng băng thông mạng
- Compress ảnh trước khi upload
- Cache ảnh đã xử lý
```

**AI Inference Bottleneck:**
```
Inference: 110ms (4.4%)

Đánh giá: ✅ Tốt (< 200ms target)
Không cần tối ưu thêm
```

**System Resource:**
```
GPU: 80% (✅ Tốt)
CPU: 50% (✅ Tốt)
Memory: 65% (✅ Tốt)
Temperature: 55°C (✅ Tốt)
Power: 5W (✅ Tốt)
```

### Kết Luận

**Điểm Mạnh:**
- ✅ AI inference rất nhanh (110ms)
- ✅ GPU utilization tốt (80%)
- ✅ Nhiệt độ ổn định (< 60°C)
- ✅ Success rate cao (100%)
- ✅ Không có memory leak

**Điểm Cần Cải Thiện:**
- ⚠️ Network latency cao (50% tổng thời gian)
- ⚠️ Throughput E2E thấp (0.4 FPS)

**Khuyến Nghị:**
1. Tối ưu network (Firebase region, băng thông)
2. Xem xét edge processing (giảm upload/download)
3. Batch processing nếu có thể
4. Caching cho ảnh đã xử lý

---

## Phụ Lục

### A. Lệnh Hữu Ích

```bash
# Kiểm tra GPU
jtop

# Kiểm tra nhiệt độ
cat /sys/devices/virtual/thermal/thermal_zone*/temp

# Kiểm tra power mode
sudo nvpmodel -q

# Set max performance
sudo jetson_clocks

# Kiểm tra network
ping -c 10 firebasestorage.googleapis.com
speedtest-cli

# Xem log real-time
tail -f complete_test_results.json

# Export CSV sang Excel
libreoffice complete_test_results.csv
```

### B. Troubleshooting

**Lỗi: Timeout**
```bash
# Tăng timeout
python3 run_complete_test.py --folder ./test_images --max 100 --timeout 60
```

**Lỗi: Out of Memory**
```bash
# Giảm số ảnh test
python3 run_complete_test.py --folder ./test_images --max 50

# Hoặc restart Jetson
sudo reboot
```

**Lỗi: High Temperature**
```bash
# Enable fan
sudo jetson_clocks --fan

# Reduce power mode
sudo nvpmodel -m 1  # 5W mode
```

### C. Tài Liệu Tham Khảo

- [JETSON_MONITORING_GUIDE.md](./web/JETSON_MONITORING_GUIDE.md)
- [QUICK_START.md](./web/QUICK_START.md)
- [DEPLOYMENT_CHECKLIST.md](./DEPLOYMENT_CHECKLIST.md)
- [MONITORING_SUMMARY.md](./MONITORING_SUMMARY.md)

---

**Người thực hiện:** _________________

**Ngày thực hiện:** _________________

**Kết quả:** ☐ Pass  ☐ Fail  ☐ Need Review

**Ghi chú:** _________________
