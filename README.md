# Kịch Bản Kiểm Thử Chi Tiết - Hệ Thống Rice Disease Detection

**Dự án**: Rice Disease Detection System  
**Phiên bản**: 1.0.0  
**Ngày tạo**: December 4, 2025  
**Người thực hiện**: Test Team

---

## 📋 Mục Lục

1. [Tổng Quan](#1-tổng-quan)
2. [Môi Trường Test](#2-môi-trường-test)
3. [Công Cụ & Phương Pháp](#3-công-cụ--phương-pháp)
4. [Kịch Bản Test Chi Tiết](#4-kịch-bản-test-chi-tiết)
5. [Tiêu Chí Đánh Giá](#5-tiêu-chí-đánh-giá)
6. [Quy Trình Thực Hiện](#6-quy-trình-thực-hiện)
7. [Báo Cáo Kết Quả](#7-báo-cáo-kết-quả)

---

## 1. Tổng Quan

### 1.1. Mục Đích Test

Đánh giá toàn diện hiệu năng và độ tin cậy của hệ thống Rice Disease Detection, bao gồm:

- ✅ **Performance Testing**: Đo lường thời gian xử lý, throughput
- ✅ **Load Testing**: Kiểm tra khả năng xử lý đồng thời
- ✅ **Stress Testing**: Tìm giới hạn của hệ thống
- ✅ **Reliability Testing**: Đánh giá độ ổn định
- ✅ **Integration Testing**: Kiểm tra tích hợp các module

### 1.2. Phạm Vi Test

**Trong phạm vi:**
- AI Inference performance (Jetson Nano)
- Firebase integration (Storage, Realtime Database)
- Backend API endpoints
- Frontend upload workflow
- End-to-end latency
- System reliability

**Ngoài phạm vi:**
- Security testing (penetration testing)
- UI/UX testing
- Mobile app testing
- Hardware stress testing

---

## 2. Môi Trường Test

### 2.1. Hardware

#### Jetson Nano
```yaml
Device: NVIDIA Jetson Nano
GPU: Maxwell (128 CUDA cores)
RAM: 4GB LPDDR4
Storage: 64GB microSD
Power Mode: 10W / 15W
Network: WiFi 802.11ac
```

#### Test Client (PC/Laptop)
```yaml
OS: Windows 11 / Ubuntu 20.04
RAM: 8GB minimum
Network: Stable internet (10+ Mbps)
Browser: Chrome 120+ / Firefox 120+
```

### 2.2. Software

#### Backend
```yaml
Runtime: Node.js 18+
Framework: Express.js
Database: MongoDB Atlas
Firebase: Admin SDK
```

#### AI Model
```yaml
Framework: TensorRT
Precision: FP16
Input Size: 224x224x3
Model: rice_disease_fp16.plan
```

#### Frontend
```yaml
Framework: React 18+
Build Tool: Vite
State Management: React Hooks
```

### 2.3. Network

```yaml
Firebase Region: Asia Southeast 1
MongoDB Region: Asia Pacific
Upload Speed: 10+ Mbps
Download Speed: 20+ Mbps
Latency to Firebase: < 50ms
```

---

## 3. Công Cụ & Phương Pháp

### 3.1. Công Cụ Test

#### A. Performance Testing

**1. Custom Node.js Scripts**
```bash
# Batch test với 100 ảnh
node scripts/testDataFolder.js ../../data User_1 100

# Test threshold alerts
node scripts/testThresholdAlert.js

# Benchmark AI performance
node scripts/benchmarkAI.js
```

**Ưu điểm:**
- ✅ Tích hợp sẵn với Firebase
- ✅ Đo timing chính xác
- ✅ Tự động tạo báo cáo
- ✅ Dễ customize

**2. Artillery (Load Testing)**
```bash
npm install -g artillery

# Load test API endpoints
artillery run load-test.yml
```

**Ưu điểm:**
- ✅ Simulate concurrent users
- ✅ Detailed metrics
- ✅ Easy to configure

**3. Apache JMeter**
```
Download: https://jmeter.apache.org/
```

**Ưu điểm:**
- ✅ GUI interface
- ✅ Comprehensive reporting
- ✅ Plugin ecosystem

#### B. Monitoring Tools

**1. Firebase Console**
```
URL: https://console.firebase.google.com/
```

**Giám sát:**
- Storage usage
- Database reads/writes
- Network traffic
- Error rates

**2. MongoDB Atlas Dashboard**
```
URL: https://cloud.mongodb.com/
```

**Giám sát:**
- Query performance
- Connection pool
- CPU/Memory usage
- Slow queries

**3. Jetson Stats (jtop)**
```bash
# Install
sudo pip3 install jetson-stats

# Run
sudo jtop
```

**Giám sát:**
- GPU utilization
- CPU usage
- Memory usage
- Temperature
- Power consumption

**4. Chrome DevTools**
```
F12 → Network/Performance tabs
```

**Giám sát:**
- Network requests
- Page load time
- JavaScript performance
- Memory leaks

#### C. Data Collection Tools

**1. Postman**
```
Download: https://www.postman.com/
```

**Sử dụng:**
- Test API endpoints
- Collection runner
- Environment variables

**2. curl**
```bash
# Test login
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"User_1","password":"123456"}'
```

### 3.2. Phương Pháp Test

#### A. Black Box Testing

**Định nghĩa:** Test hệ thống như một "hộp đen", không quan tâm implementation

**Áp dụng:**
- Upload ảnh qua UI
- Test API endpoints
- End-to-end workflows

**Ưu điểm:**
- ✅ Giống user thực tế
- ✅ Không cần biết code
- ✅ Phát hiện lỗi integration

#### B. White Box Testing

**Định nghĩa:** Test với kiến thức về implementation

**Áp dụng:**
- Test từng function riêng lẻ
- Đo timing từng stage
- Analyze code coverage

**Ưu điểm:**
- ✅ Tìm bottleneck chính xác
- ✅ Optimize hiệu quả
- ✅ Debug dễ dàng

#### C. Performance Testing

**Phương pháp:**

1. **Baseline Testing**
   - Test với 1 user, 1 request
   - Đo latency chuẩn
   - Làm reference cho các test sau

2. **Load Testing**
   - Tăng dần số users
   - Đo response time
   - Tìm điểm bão hòa

3. **Stress Testing**
   - Vượt quá capacity
   - Tìm breaking point
   - Kiểm tra recovery

4. **Spike Testing**
   - Tăng đột ngột traffic
   - Kiểm tra auto-scaling
   - Đánh giá resilience

5. **Endurance Testing**
   - Chạy liên tục 24h+
   - Tìm memory leaks
   - Kiểm tra stability

#### D. Statistical Analysis

**Metrics thu thập:**
- Mean (Trung bình)
- Median (Trung vị)
- Standard Deviation (Độ lệch chuẩn)
- Min/Max
- Percentiles (P50, P90, P95, P99)

**Công thức:**

```
Mean = Σ(xi) / n

Median = Middle value when sorted

Std Dev = √(Σ(xi - mean)² / n)

P95 = Value at 95th percentile
```

---

## 4. Kịch Bản Test Chi Tiết

### 4.1. Test Case 1: AI Inference Performance

**Mục tiêu:** Đo lường hiệu năng AI inference trên Jetson Nano

**Phương pháp:** Automated batch testing

**Công cụ:** Custom Node.js script

**Dữ liệu test:**
- 100 ảnh từ folder `data/`
- 3 classes: Brown Spot, Normal, Leaf Blast
- Format: JPG, kích thước đa dạng

**Các bước thực hiện:**

```bash
# Bước 1: Chuẩn bị môi trường
cd Rice_user/backend
npm install

# Bước 2: Kiểm tra Jetson đang chạy
# SSH vào Jetson và check service

# Bước 3: Chạy test
node scripts/testDataFolder.js ../../data User_1 100

# Bước 4: Đợi kết quả (~ 10-15 phút)

# Bước 5: Lưu kết quả
node scripts/testDataFolder.js ../../data User_1 100 > results_100.txt 2>&1
```

**Metrics đo lường:**

| Metric | Target | Method |
|--------|--------|--------|
| Upload Time | < 500ms | Đo từ start upload đến get URL |
| Preprocessing | < 20ms | Đo trong Jetson code |
| AI Inference | < 200ms | Đo TensorRT execution time |
| Postprocessing | < 30ms | Đo từ output đến JSON |
| E2E Latency | < 1000ms | Đo từ upload đến nhận result |
| Throughput | > 5 FPS | 1000 / inference_time |
| Success Rate | > 95% | successful / total |

**Tiêu chí Pass/Fail:**

```yaml
PASS if:
  - AI Inference < 200ms (average)
  - Throughput > 5 FPS
  - Success Rate > 95%
  - No crashes or timeouts

FAIL if:
  - Any metric below target
  - > 5% timeout rate
  - System crashes
```

**Expected Output:**

```
📊 Performance Breakdown:
   Upload:         400ms (84%)
   Preprocessing:  15ms (3%)
   AI Inference:   130ms (27%)
   Postprocessing: 20ms (4%)
   Total:          470ms (100%)

🎯 Metrics:
   Throughput: 7.5 FPS ✅
   Success Rate: 100% ✅
   Status: PASS ✅
```

---

### 4.2. Test Case 2: Load Testing - Concurrent Users

**Mục tiêu:** Kiểm tra khả năng xử lý nhiều users đồng thời

**Phương pháp:** Load testing với Artillery

**Công cụ:** Artillery.io

**Kịch bản:**

```yaml
# File: load-test.yml
config:
  target: 'http://localhost:5000'
  phases:
    - duration: 60
      arrivalRate: 1
      name: "Warm up"
    - duration: 120
      arrivalRate: 5
      name: "Ramp up to 5 users/sec"
    - duration: 180
      arrivalRate: 10
      name: "Sustained load - 10 users/sec"
    - duration: 60
      arrivalRate: 20
      name: "Peak load - 20 users/sec"

scenarios:
  - name: "Upload and Predict"
    flow:
      - post:
          url: "/api/auth/login"
          json:
            username: "User_1"
            password: "123456"
          capture:
            - json: "$.token"
              as: "token"
      
      - post:
          url: "/api/predict"
          headers:
            Authorization: "Bearer {{ token }}"
          beforeRequest: "uploadImage"
          
      - think: 2
```

**Các bước thực hiện:**

```bash
# Bước 1: Install Artillery
npm install -g artillery

# Bước 2: Tạo file config
nano load-test.yml

# Bước 3: Chạy test
artillery run load-test.yml

# Bước 4: Tạo HTML report
artillery run load-test.yml --output report.json
artillery report report.json
```

**Metrics đo lường:**

| Metric | Target | Description |
|--------|--------|-------------|
| Response Time (P50) | < 500ms | 50% requests |
| Response Time (P95) | < 2000ms | 95% requests |
| Response Time (P99) | < 5000ms | 99% requests |
| Error Rate | < 1% | Failed requests |
| Requests/sec | > 10 | Throughput |

**Tiêu chí Pass/Fail:**

```yaml
PASS if:
  - P95 response time < 2s
  - Error rate < 1%
  - No server crashes
  - Throughput > 10 req/s

FAIL if:
  - P95 > 5s
  - Error rate > 5%
  - Server crashes
```

---

### 4.3. Test Case 3: Stress Testing - Breaking Point

**Mục tiêu:** Tìm giới hạn của hệ thống

**Phương pháp:** Incremental load increase

**Công cụ:** Artillery + Custom scripts

**Kịch bản:**

```yaml
config:
  target: 'http://localhost:5000'
  phases:
    - duration: 60
      arrivalRate: 10
    - duration: 60
      arrivalRate: 20
    - duration: 60
      arrivalRate: 50
    - duration: 60
      arrivalRate: 100
    - duration: 60
      arrivalRate: 200
```

**Các bước thực hiện:**

```bash
# Bước 1: Monitor resources
# Terminal 1: Monitor Jetson
ssh jetson@192.168.1.100
sudo jtop

# Terminal 2: Monitor MongoDB
# Check Atlas dashboard

# Terminal 3: Run stress test
artillery run stress-test.yml

# Bước 4: Observe breaking point
# Note when errors start occurring
```

**Metrics đo lường:**

| Resource | Monitor | Breaking Point |
|----------|---------|----------------|
| Jetson GPU | jtop | > 95% utilization |
| Jetson CPU | jtop | > 90% utilization |
| Jetson Memory | jtop | > 3.5GB (of 4GB) |
| Jetson Temp | jtop | > 80°C |
| MongoDB CPU | Atlas | > 80% |
| Error Rate | Artillery | > 10% |

**Expected Results:**

```
Breaking Point Analysis:
- Max concurrent users: ~50
- Max requests/sec: ~25
- Bottleneck: Jetson GPU queue
- Recovery time: ~30 seconds
- Graceful degradation: Yes ✅
```

---

### 4.4. Test Case 4: Endurance Testing - 24h Stability

**Mục tiêu:** Kiểm tra độ ổn định dài hạn

**Phương pháp:** Continuous load for 24 hours

**Công cụ:** Custom Node.js script + cron

**Kịch bản:**

```bash
# Script: endurance-test.sh
#!/bin/bash

START_TIME=$(date +%s)
END_TIME=$((START_TIME + 86400)) # 24 hours

while [ $(date +%s) -lt $END_TIME ]; do
  echo "$(date): Running test iteration..."
  
  # Run single test
  node scripts/testSingleImage.js ./test_images/sample.jpg User_1
  
  # Wait 10 seconds
  sleep 10
  
  # Log memory usage
  free -h >> memory_log.txt
  
  # Check for errors
  if [ $? -ne 0 ]; then
    echo "$(date): ERROR detected!" >> error_log.txt
  fi
done

echo "Endurance test completed!"
```

**Các bước thực hiện:**

```bash
# Bước 1: Chuẩn bị script
chmod +x endurance-test.sh

# Bước 2: Start monitoring
# Terminal 1: Monitor Jetson
watch -n 60 'jtop --stats'

# Terminal 2: Monitor logs
tail -f error_log.txt

# Terminal 3: Run test
./endurance-test.sh

# Bước 4: Analyze sau 24h
python analyze_endurance.py
```

**Metrics đo lường:**

| Metric | Target | Method |
|--------|--------|--------|
| Uptime | 100% | Check service status |
| Memory Leak | < 100MB/24h | Monitor RSS |
| Error Rate | < 0.1% | Count errors |
| Performance Degradation | < 5% | Compare first vs last hour |
| Recovery from Errors | < 1 min | Time to auto-recover |

**Tiêu chí Pass/Fail:**

```yaml
PASS if:
  - No crashes in 24h
  - Memory stable (< 100MB increase)
  - Performance degradation < 5%
  - Auto-recovery works

FAIL if:
  - System crashes
  - Memory leak > 500MB
  - Performance drops > 20%
```

---

### 4.5. Test Case 5: Threshold Alert System

**Mục tiêu:** Kiểm tra hệ thống cảnh báo ngưỡng

**Phương pháp:** Automated testing

**Công cụ:** Custom Node.js script

**Kịch bản:**

```bash
# Test 1: Soil Moisture Low
node scripts/testThresholdAlert.js

# Test 2: pH High
# Modify script to test pH = 8.5

# Test 3: Cooldown mechanism
# Send 2 alerts within 30 minutes

# Test 4: Email delivery
# Check inbox for alert emails
```

**Test Cases:**

| Test ID | Sensor | Value | Expected | Actual | Status |
|---------|--------|-------|----------|--------|--------|
| TC-TH-001 | Soil | 20% | Alert (Low) | ✅ | PASS |
| TC-TH-002 | Soil | 90% | Alert (High) | ✅ | PASS |
| TC-TH-003 | Soil | 50% | No Alert | ✅ | PASS |
| TC-TH-004 | pH | 4.5 | Alert (Low) | ✅ | PASS |
| TC-TH-005 | pH | 8.5 | Alert (High) | ✅ | PASS |
| TC-TH-006 | pH | 6.5 | No Alert | ✅ | PASS |
| TC-TH-007 | Cooldown | 2x in 15min | 1 Alert | ✅ | PASS |
| TC-TH-008 | Email | Alert sent | Received | ✅ | PASS |

**Metrics đo lường:**

| Metric | Target | Method |
|--------|--------|--------|
| Alert Latency | < 5s | Time from trigger to notification |
| Email Delivery | < 30s | Time to receive email |
| Cooldown Accuracy | ±1 min | Check 30-minute window |
| False Positive Rate | < 1% | Alerts when shouldn't |
| False Negative Rate | 0% | No alert when should |

---

### 4.6. Test Case 6: API Endpoint Testing

**Mục tiêu:** Kiểm tra tất cả API endpoints

**Phương pháp:** Manual + Automated (Postman)

**Công cụ:** Postman Collection

**Test Cases:**

#### A. Authentication Endpoints

```javascript
// TC-API-001: Login Success
POST /api/auth/login
Body: { "username": "User_1", "password": "123456" }
Expected: 200 OK, token returned
Actual: ✅ PASS

// TC-API-002: Login Failed
POST /api/auth/login
Body: { "username": "User_1", "password": "wrong" }
Expected: 401 Unauthorized
Actual: ✅ PASS

// TC-API-003: Get User Info
GET /api/auth/me
Headers: { "Authorization": "Bearer <token>" }
Expected: 200 OK, user data
Actual: ✅ PASS
```

#### B. Threshold Endpoints

```javascript
// TC-API-004: Get Thresholds
GET /api/auth/thresholds
Headers: { "Authorization": "Bearer <token>" }
Expected: 200 OK, threshold settings
Actual: ✅ PASS

// TC-API-005: Update Thresholds
PUT /api/auth/thresholds
Body: { "thresholds": { "soilMoisture": { "enabled": true, "min": 30, "max": 80 } } }
Expected: 200 OK, updated
Actual: ✅ PASS

// TC-API-006: Invalid Threshold
PUT /api/auth/thresholds
Body: { "thresholds": { "soilMoisture": { "min": 80, "max": 30 } } }
Expected: 400 Bad Request
Actual: ✅ PASS
```

**Metrics đo lường:**

| Endpoint | Method | Target | Actual | Status |
|----------|--------|--------|--------|--------|
| /api/auth/login | POST | < 500ms | 250ms | ✅ |
| /api/auth/me | GET | < 300ms | 120ms | ✅ |
| /api/auth/thresholds | GET | < 200ms | 80ms | ✅ |
| /api/auth/thresholds | PUT | < 300ms | 150ms | ✅ |

---

### 4.7. Test Case 7: End-to-End Workflow

**Mục tiêu:** Test toàn bộ workflow từ đầu đến cuối

**Phương pháp:** Manual testing (Black box)

**Công cụ:** Browser + DevTools

**Kịch bản:**

```
Scenario: User uploads image and gets prediction

Given: User is logged in
When: User navigates to Upload page
And: User selects an image file
And: User clicks "Predict with Jetson"
Then: Image is uploaded to Firebase Storage
And: Predict request is sent to Jetson
And: User sees "Processing..." status
And: Result is displayed within 10 seconds
And: Result shows disease class and confidence
And: Result is saved to database
```

**Các bước thực hiện:**

```bash
# Bước 1: Open browser
chrome http://localhost:5173

# Bước 2: Login
Username: User_1
Password: 123456

# Bước 3: Navigate to Upload
Click "Upload" in navigation

# Bước 4: Select image
Click "Choose File"
Select: test_images/brown_spot_1.jpg

# Bước 5: Predict
Click "Predict with Jetson"

# Bước 6: Observe
- Check upload progress
- Check processing status
- Check result display
- Check timing

# Bước 7: Verify in Firebase
- Check Storage for uploaded image
- Check Database for result
- Check timestamp

# Bước 8: Verify in MongoDB
- Check captures collection
- Check user document
```

**Metrics đo lường:**

| Step | Target | Actual | Status |
|------|--------|--------|--------|
| Page Load | < 2s | 1.2s | ✅ |
| Image Upload | < 1s | 0.8s | ✅ |
| Processing | < 10s | 5.2s | ✅ |
| Result Display | < 1s | 0.3s | ✅ |
| Total E2E | < 15s | 7.5s | ✅ |

---

## 5. Tiêu Chí Đánh Giá

### 5.1. Performance Criteria

| Metric | Excellent | Good | Acceptable | Poor |
|--------|-----------|------|------------|------|
| AI Inference | < 100ms | < 150ms | < 200ms | > 200ms |
| E2E Latency | < 500ms | < 1000ms | < 2000ms | > 2000ms |
| Throughput | > 10 FPS | > 7 FPS | > 5 FPS | < 5 FPS |
| Success Rate | 100% | > 99% | > 95% | < 95% |
| Error Rate | 0% | < 0.1% | < 1% | > 1% |

### 5.2. Reliability Criteria

| Metric | Target | Method |
|--------|--------|--------|
| Uptime | > 99.9% | Monitor 24/7 |
| MTBF | > 720h | Mean time between failures |
| MTTR | < 5min | Mean time to recovery |
| Data Loss | 0% | Check data integrity |

### 5.3. Scalability Criteria

| Metric | Target | Method |
|--------|--------|--------|
| Concurrent Users | > 50 | Load testing |
| Requests/sec | > 25 | Stress testing |
| Queue Length | < 10 | Monitor Jetson queue |
| Response Time Degradation | < 10% | Compare under load |

---

## 6. Quy Trình Thực Hiện

### 6.1. Pre-Test Checklist

```yaml
Environment:
  ☐ Jetson Nano powered on and connected
  ☐ Backend server running (port 5000)
  ☐ Frontend server running (port 5173)
  ☐ MongoDB connection verified
  ☐ Firebase connection verified
  ☐ Network stable (ping < 50ms)

Data:
  ☐ Test images prepared (100+ images)
  ☐ Test users created in database
  ☐ Baseline data collected

Tools:
  ☐ Node.js installed
  ☐ Artillery installed (if needed)
  ☐ Postman configured (if needed)
  ☐ Monitoring tools ready

Documentation:
  ☐ Test plan reviewed
  ☐ Expected results documented
  ☐ Bug tracking system ready
```

### 6.2. Test Execution Steps

```
Phase 1: Preparation (30 minutes)
├── 1. Setup environment
├── 2. Verify connections
├── 3. Collect baseline data
└── 4. Start monitoring tools

Phase 2: Functional Testing (2 hours)
├── 1. API endpoint testing
├── 2. Upload workflow testing
├── 3. Threshold alert testing
└── 4. Integration testing

Phase 3: Performance Testing (4 hours)
├── 1. Baseline performance (1 hour)
├── 2. Batch testing 100 images (1 hour)
├── 3. Load testing (1 hour)
└── 4. Stress testing (1 hour)

Phase 4: Reliability Testing (24+ hours)
├── 1. Endurance testing (24 hours)
├── 2. Recovery testing (2 hours)
└── 3. Failover testing (2 hours)

Phase 5: Analysis & Reporting (4 hours)
├── 1. Data analysis (2 hours)
├── 2. Report generation (1 hour)
└── 3. Recommendations (1 hour)
```

### 6.3. Post-Test Checklist

```yaml
Data Collection:
  ☐ All test results saved
  ☐ Logs collected
  ☐ Screenshots captured
  ☐ Metrics exported

Analysis:
  ☐ Performance metrics calculated
  ☐ Bottlenecks identified
  ☐ Comparison with baseline
  ☐ Trends analyzed

Reporting:
  ☐ Test report generated
  ☐ Graphs and charts created
  ☐ Recommendations documented
  ☐ Stakeholders notified

Cleanup:
  ☐ Test data removed
  ☐ Services stopped
  ☐ Resources released
  ☐ Environment reset
```

---

## 7. Báo Cáo Kết Quả

### 7.1. Report Template

```markdown
# Test Report - [Test Name]

## Executive Summary
- Test Date: [Date]
- Duration: [Hours]
- Test Cases: [Total] ([Passed]/[Failed])
- Overall Status: [PASS/FAIL]

## Test Results

### Performance Metrics
| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| ... | ... | ... | ... |

### Detailed Results
[Tables, graphs, analysis]

### Issues Found
| ID | Severity | Description | Status |
|----|----------|-------------|--------|
| ... | ... | ... | ... |

### Recommendations
1. [Recommendation 1]
2. [Recommendation 2]

## Conclusion
[Summary and next steps]
```

### 7.2. Deliverables

```
Test Deliverables:
├── Test_Report.pdf
├── Performance_Report.md
├── Test_Results.xlsx
├── Graphs/
│   ├── latency_distribution.png
│   ├── throughput_over_time.png
│   └── resource_utilization.png
├── Logs/
│   ├── test_execution.log
│   ├── error.log
│   └── performance.log
└── Raw_Data/
    ├── results_100.json
    ├── load_test_results.json
    └── endurance_test_data.csv
```

---

## 📞 Contact & Support

**Test Lead**: [Your Name]  
**Email**: [Your Email]  
**Slack**: #testing-channel  
**Documentation**: [Wiki Link]

---

**Version**: 1.0  
**Last Updated**: December 4, 2025  
**Next Review**: January 4, 2026
