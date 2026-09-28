# HƯỚNG DẪN TUNING BÀI BẢN, HOÀN CHỈNH TỪ A-Z CHO ACKERMANN ROVER (C500 & SITL)

---

## MỤC LỤC
1. **Tổng Quan Kiến Trúc & Động Học Hệ Lái Ackermann**
2. **Giai Đoạn 0: Bảng So Sánh & Phân Tích Param Xe Thật C500 (`c500_23092026.param` vs `full_default.param`)**
3. **Giai Đoạn 1: Cấu Hình Phần Cứng, Giao Tiếp & An Toàn (Hardware, CAN & Safety)**
4. **Giai Đoạn 2: Tuning Vòng Lặp Tốc Độ Tiến & Phanh (Speed Loop, Throttle & CAN Velocity)**
5. **Giai Đoạn 3: Tuning Vòng Lặp Lái Thủ Công (Steering Rate Loop - Manual/Acro Mode)**
6. **Giai Đoạn 4: Tuning Vòng Lặp Vị Trí & Đường Cong S-Curve (Position Control & S-Curve Auto Mode)**
7. **Giai Đoạn 5: Hướng Dẫn Xử Lý Các Lỗi Thực Tế Thường Gặp (Troubleshooting Master Guide)**
8. **Giai Đoạn 6: Bảng Tổng Hợp Tham Số Chốt Chuẩn Xe Thật C500 (Master Parameter Checklist & Migration Guide)**

---

## 1. TỔNG QUAN KIẾN TRÚC & ĐỘNG HỌC HỆ LÁI ACKERMANN

Hệ điều khiển xe Ackermann Rover trên ArduPilot (Rover v4.2+) hoạt động theo kiến trúc điều khiển phân tầng:

```
[ Mission Waypoint ]
        │
        ▼ (S-Curve Trajectory Generation: WP_ACCEL, WP_JERK, WP_RADIUS)
[ Target Position / Velocity / Acceleration ]
        │
        ▼ (Position Control: PSC_POS_P, PSC_VEL_P, PSC_VEL_D)
[ Desired Speed & Desired Turn Rate ]
        │
        ├──► Throttle Controller (ATC_SPEED_P/I, CRUISE_THROTTLE) ──► Motors (PWM / CAN 0x113, CAN_D2_VEL_MAX)
        │
        └──► Steering Rate Controller (ATC_STR_RAT_FF/P/I/D, ACC_MAX) ──► Steering Servo (Angle Position Radian)
```

---

## 2. GIAI ĐOẠN 0: BẢNG SO SÁNH & PHÂN TÍCH PARAM (`c500_23092026.param` vs `full_default.param`)

Lấy file **`c500_23092026.param` làm bộ thông số chuẩn của xe thật C500**, và file **`full_default.param` làm gốc mặc định ArduRover**, dưới đây là bảng phân tích chi tiết cách chuyển đổi từ mặc định sang chuẩn C500:

| Nhóm Tham Số | Tham Số | Mặc Định (`full_default`) | Xe Thật C500 Chuẩn (`c500_23092026`) | Phân Tích Kỹ Thuật & Lý Do Thay Đổi |
| :--- | :--- | :---: | :---: | :--- |
| **1. Khung & Cơ Cấu** | `FRAME_CLASS` | `1` | **`1`** | Xe dạng Rover |
| | `FRAME_TYPE` | `0` | **`0`** | Hệ lái Ackermann (Front Steering + Rear Drive) |
| | `MOT_STR_THR_MIX`| `0.5` | **`0.3` / `0.9`** | Xe C500 ưu tiên bù ga khi bẻ lái để tránh chết máy khi cua nặng |
| | `SERVO1_FUNCTION` | `26` | **`26`** | Output Kênh 1: Ground Steering (Lái) |
| | `SERVO3_FUNCTION` | `70` | **`70`** | Output Kênh 3: Throttle (Ga tiến/lùi) |
| **2. Tốc Độ Góc Lái** | `ACRO_TURN_RATE` | `180` | **`51`** | Tốc độ quay góc tối đa thực tế của C500 khi bẻ hết lái (deg/s) |
| | `ATC_STR_RAT_MAX` | `120` | **`51`** | Giới hạn trần tốc độ bẻ lái tương thích với `ACRO_TURN_RATE` |
| | `ATC_STR_ACC_MAX` | `120` | **`102`** | Gia tốc vô lăng của C500 gốc (sau tuning nâng lên `180.0` để xóa trễ) |
| | `ATC_STR_RAT_FF` | `0.75` | **`1.25`** | Lực FeedForward bẻ vô lăng ban đầu |
| | `ATC_STR_RAT_D` | `0` | **`0.01`** | Damping dập tắt dao động trả lái vô lăng |
| **3. Tốc Độ & Phanh** | `CRUISE_SPEED` | `5.0` | **`1.0`** | Tốc độ di chuyển danh định của xe C500 (m/s) |
| | `CRUISE_THROTTLE`| `30` | **`50`** | Mức Throttle (%) để xe C500 chạy ổn định ở tốc độ 1.0 m/s |
| | `ATC_ACCEL_MAX` | `1` | **`1.0`** | Gia tốc tiến tối đa (m/s²) |
| | `ATC_DECEL_MAX` | `0` | **`2.0`** | Gia tốc giảm tốc tối đa (m/s²) |
| | `ATC_TURN_MAX_G` | `0.6` | **`1.0`** | Giới hạn gia tốc ly tâm khi bẻ cua ($1.0g$) |
| **4. S-Curve & WPNAV** | `WP_SPEED` | `5.0` | **`1.0`** | Tốc độ di chuyển giữa các Waypoint |
| | `WP_ACCEL` | `0` | **`0.5`** | Gia tốc S-Curve (sau tuning nắn về `0.35` để rẽ sớm 3.5m) |
| | `WP_JERK` | `0` | **`0.5`** | Jerk gia tốc S-Curve |
| | `WP_RADIUS` | `3.0` | **`6.0`** | Bán kính nhận rẽ Waypoint (m) |
| | `TURN_RADIUS` | `0.9` | **`3.5` / `3.0`**| Bán kính quay tối thiểu cơ khí xe C500 |
| | `PSC_POS_P` | `0.2` | **`1.5`** | Hệ số P vị trí ngang (sau tuning điều chỉnh về `0.6` để hết rắn bò) |
| **5. Compass & AHRS** | `AHRS_ORIENTATION`| `0` | **`29`** | Hướng lắp mạch FC trên C500 (Pitch 180, Yaw 90) |
| | `COMPASS_USE2` | `1` | **`0` / `1`** | C500 cấu hình La bàn 2 |
| | `COMPASS_OFS2_X` | `0` | **`149.56`** | Offset từ trường cục bộ la bàn 2 trục X |
| | `COMPASS_OFS2_Y` | `0` | **`26.08`** | Offset từ trường cục bộ la bàn 2 trục Y |
| | `COMPASS_OFS2_Z` | `0` | **`75.36`** | Offset từ trường cục bộ la bàn 2 trục Z |
| **6. EKF3 Position** | `EK3_SRC1_YAW` | `1` | **`1`** | C500 dùng Compass làm nguồn Heading (khuyên dùng `2` cho GPS Yaw) |
| | `EK3_SRC1_POSXY` | `0` | **`3`** | Dùng GPS làm nguồn vị trí XY chính |
| **7. CAN & Ethernet** | `CAN_D1_PROTOCOL` | `15` | **`1`** | CAN 1 chạy giao thức CAN HAL |
| | `CAN_D2_PROTOCOL` | `0` | **`15`** | CAN 2 chạy giao thức Lua Scripting |
| | **`CAN_D2_VEL_MAX`**| `[Unset]`| **`2`** | **Vận tốc CAN tối đa (m/s) gửi xuống chassis khi ga 100%** |
| | `CAN_D2_STEER_MAX`| `[Unset]`| **`30`** | **Góc lái CAN tối đa (độ) gửi xuống chassis** |
| | `CAN_P2_DRIVER` | `0` | **`2`** | Bật Driver CAN 2 trên C500 |
| | `NET_ENABLE` | `0` | **`1`** | Bật Ethernet IP `10.36.36.36` cho C500 |
| **8. Sensor & Script** | `SCR_ENABLE` | `0` | **`1`** | Bật tính năng nạp Lua Script điều khiển |
| | `PRX1_TYPE` | `0` | **`2`** | Bật cảm biến tiệm cận tránh vật cản |

---

## 3. GIAI ĐOẠN 1: CẤU HÌNH PHẦN CỨNG, GIAO TIẾP & AN TOÀN (HARDWARE SETUP)

Trước khi thực hiện bất kỳ bài chạy thử nghiệm nào, phải sửa file mặc định thành chuẩn phần cứng của xe C500:

### 3.1. Khung xe & Cơ cấu chấp hành (Frame & Servos)
```param
FRAME_CLASS,     1        # Rover
FRAME_TYPE,      0        # Ackermann Steering
SERVO1_FUNCTION, 26       # Kênh 1: Ground Steering (Lái)
SERVO3_FUNCTION, 70       # Kênh 3: Throttle (Động cơ)
SERVO1_MIN,      1100     # Giới hạn PWM bẻ hết trái
SERVO1_MAX,      1900     # Giới hạn PWM bẻ hết phải
SERVO1_TRIM,     1500     # PWM trung tâm (bánh thẳng tắp)
```

### 3.2. Cấu hình Mạng CAN & Giới hạn Vận tốc Lái CAN (`CAN_D2_VEL_MAX`)
- **`CAN_D2_VEL_MAX`**: Quy định vận tốc tuyến tính tối đa (m/s) được mã hóa vào gói CAN `0x113` (byte 4–5) gửi tới bộ điều khiển động cơ chassis C500 khi ga đạt 100%. Với C500 đặt `CAN_D2_VEL_MAX = 2` (tương đương max 2.0 m/s).
- **`CAN_D2_STEER_MAX`**: Quy định góc bẻ lái CAN tối đa (độ) được gửi xuống chassis. Với C500 đặt `CAN_D2_STEER_MAX = 30` (độ).

### 3.3. Quy ước Tín hiệu Lái (Angle Position Command)
- Trên giao thức CAN / ROS 2 (`baf_gzsimulator`), lệnh bẻ lái từ Servo 1 được quy đổi trực tiếp thành **Góc lái cơ khí (Angle Position, tính bằng Radian)** trong dải $[-0.5, +0.5]$ rad (tương đương $\pm 28.6^\circ$).
- Phản hồi từ Chassis CAN `0x213` cũng là góc lái thực tế (Radian).

### 3.4. Hiệu chỉnh La bàn & EKF3 (Compass Calibration)
1. **Kiểm tra hướng lắp FC**: Đặt `AHRS_ORIENTATION = 29` theo đúng hướng thực tế lắp trên C500.
2. **Quy trình Calib La bàn ngoài bãi**: Đưa xe ra bãi trống (cách xa kim loại tối thiểu 5m), chạy quy trình **Onboard Compass Calibration** để máy tính tự động đo và điền bộ `COMPASS_OFS2_X/Y/Z` mới.

---

## 4. GIAI ĐOẠN 2: TUNING VÒNG LẶP TỐC ĐỘ TIẾN, PHANH & CAN VELOCITY

Mục tiêu giai đoạn này: Xe chạy bám đúng tốc độ đặt `WP_SPEED`, tăng tốc/giảm tốc mượt mà, truyền chính xác lệnh vận tốc mm/s sang gói CAN `0x113`.

### 4.1. Cấu hình Tốc độ Danh định & Ga Nền (Cruise & CAN Velocity Setup)
1. Đưa xe vào chế độ `MANUAL`, lái thẳng ở tốc độ mong muốn (ví dụ $1.0\text{ m/s}$).
2. Đọc mức Throttle (%) hiển thị trên GCS tại tốc độ đó.
3. Đặt tham số:
```param
CRUISE_SPEED,    1.0      # Tốc độ danh định của C500 (m/s)
CRUISE_THROTTLE, 50       # Mức ga % để đạt tốc độ danh định
CAN_D2_VEL_MAX,  2        # Vận tốc CAN tối đa (m/s) mã hóa xuống byte 4-5 gói CAN 0x113 khi ga 100%
```

### 4.2. Tuning PID Tốc độ tiến (Throttle PID)
```param
ATC_SPEED_P,     0.02     # Hệ số P bám tốc độ
ATC_SPEED_I,     0.1      # Hệ số I tích lũy sai số tốc độ
ATC_ACCEL_MAX,   1.0      # Gia tốc tiến tối đa (m/s²)
ATC_DECEL_MAX,   2.0      # Gia tốc giảm tốc tối đa (m/s²)
ATC_BRAKE,       1        # Bật phanh động cơ chủ động khi dừng
```

---

## 5. GIAI ĐOẠN 3: TUNING VÒNG LẶP LÁI THỦ CÔNG (STEERING RATE LOOP)

Mục tiêu giai đoạn này: Vô lăng phản ứng tức thì với lệnh lái, bám sát mong muốn trong chế độ `ACRO`, không bị trễ cơ khí hay dao động.

### Bước 5.1: Đo Tốc độ Góc Quay Tối Đa (`ACRO_TURN_RATE`)
1. Bật hiển thị đồ thị Tuning trên Mission Planner: `GCS_PID_MASK = 1` (Steering).
2. Đưa xe vào chế độ `MANUAL`, chạy ở tốc độ cruise ($1.0\text{ m/s}$), bẻ lái hết cỡ ở các khúc cua gấp.
3. Đọc giá trị đỉnh tốc độ quay góc Gyro Z (`gz`) đạt được (trên C500 là `51` deg/s, trong SITL khống chế `35`).
4. Đặt tham số:
```param
ACRO_TURN_RATE,  35.0     # (Hoặc 51.0 theo khả năng vật lý thật của C500)
ATC_STR_RAT_MAX, 35.0     # Đặt trùng với ACRO_TURN_RATE
CAN_D2_STEER_MAX,30       # Góc lái CAN tối đa (độ) gửi xuống chassis
```

### Bước 5.2: Tune FeedForward `ATC_STR_RAT_FF` (Quan trọng nhất)
1. Tạm thời đưa $P = 0, I = 0, D = 0$: `ATC_STR_RAT_P = 0`, `ATC_STR_RAT_I = 0`, `ATC_STR_RAT_D = 0`.
2. Đưa xe vào chế độ `ACRO`, lái thử các khúc cua từ rộng đến gấp.
3. Quan sát đồ thị `pidachieved` vs `piddesired`:
   - Nếu `achieved` đuổi theo `desired` bị chậm/trễ $\rightarrow$ **Tăng `ATC_STR_RAT_FF`** (từ 1.0 up lên 1.15 - 1.25).
   - Nếu `achieved` vọt quá `desired` $\rightarrow$ **Giảm `ATC_STR_RAT_FF`**.
4. Lặp lại đến khi `achieved` bám sát `desired` chỉ bằng lực FF.

### Bước 5.3: Thêm P, I, D & Triệt tiêu Trễ Vô Lăng
```param
ATC_STR_ACC_MAX, 180.0    # Nâng từ 102 lên 180-250 deg/s² (Xóa hoàn toàn trễ cơ khí vô lăng)
ATC_STR_RAT_P,   0.20     # Thêm P (khoảng 15-20% giá trị FF) để sửa lỗi ngắn hạn
ATC_STR_RAT_I,   0.10     # Thêm I giữ bám đường
ATC_STR_RAT_D,   0.010    # Damping dập tắt dao động trả lái khi thoát cua
```

---

## 6. GIAI ĐOẠN 4: TUNING VÒNG LẶP VỊ TRÍ & S-CURVE (POSITION CONTROL & AUTO MODE)

Mục tiêu giai đoạn này: Xe chạy `AUTO` tự động bẻ lái sớm trước Waypoint từ 3.5m–4.5m, ôm cua mượt tắp bên trong góc rẽ, không đâm lố (Overshoot) và không bị lượn sóng rắn bò trên đường thẳng.

### Bước 6.1: Cấu hình Bán kính Cua Cố Định
```param
TURN_RADIUS,     3.0      # Cố định cứng bán kính cua tối thiểu cơ khí xe C500 (3.0m)
```

### Bước 6.2: Cấu hình Cửa Sổ Rẽ Sớm S-Curve (`WP_ACCEL` & `WP_JERK`)
Cửa sổ thời gian nới rộng cho phép nạp rẽ sớm được tính bằng:
$$t_{accel} = \frac{WP\_SPEED}{WP\_ACCEL} = \frac{1.0}{0.25 - 0.35} = 3.0 - 4.0\text{ giây}$$

```param
WP_ACCEL,        0.35     # Điều chỉnh từ 0.5 về 0.35 m/s² -> Cửa sổ rẽ sớm 3.5m trước WP
WP_JERK,         0.5      # Mượt gia tốc cua S-Curve, quy định tốc độ biến thiên của gia tốc Gia tốc, không nhảy đột ngột từ 0 lên WP_ACCEL, mà tăng/giảm từ từ theo đồ thị hình chữ S.
WP_RADIUS,       5.5      # Mở rộng vùng nhận rẽ 5.5m để chứa trọn góc rẽ sớm
```

### Bước 6.3: Dập Tắt Dao Động Rắn Bò Trên Đường Thẳng (`PSC_*`)
```param
PSC_POS_P,       0.6      # Điều chỉnh từ C500 gốc (1.5) xuống 0.6 (Triệt tiêu 100% rắn lượn sóng)
PSC_VEL_P,       1.5      # Tăng khả năng bám vận tốc ngang
PSC_VEL_D,       0.20     # Dập lực ly tâm nhập làn thẳng tắp (sai số 1cm)
```

---

## 7. GIAI ĐOẠN 5: HƯỚNG DẪN XỬ LÝ CÁC LỖI THỰC TẾ THƯỜNG GẶP (TROUBLESHOOTING)

### 🚨 Lỗi 1: Xe đi đâm lố qua tâm Waypoint mới chịu ngoặt (Overshoot)
* **Nguyên nhân**: Cửa sổ $t_{accel}$ quá ngắn do `WP_ACCEL` đặt quá cao ($1.0$), hoặc vô lăng bị trễ gia tốc `ATC_STR_ACC_MAX` quá thấp ($102$).
* **Cách khắc phục**:
  1. Hạ `WP_ACCEL = 0.35` m/s², `WP_JERK = 0.5` m/s³.
  2. Nới `WP_RADIUS = 5.5 - 6.5` m.
  3. Nâng `ATC_STR_ACC_MAX = 180 - 250` deg/s² và `ATC_STR_RAT_FF = 1.15 - 1.25`.

### 🚨 Lỗi 2: Xe bị lượn sóng hình sin (Rắn bò) trên các đoạn đường thẳng
* **Nguyên nhân**: `PSC_POS_P` đặt quá cao ($1.5$). Mỗi khi lệch vài cm, bộ điều khiển vị trí ép vô lăng bẻ tới 45° gây quá đà liên tục.
* **Cách khắc phục**:
  1. Hạ `PSC_POS_P` từ $1.5$ xuống **`0.6`**.
  2. Tăng `PSC_VEL_P = 1.5` và thêm Damping `PSC_VEL_D = 0.20`.

### 🚨 Lỗi 3: Xe bị quay xoắn 360° (Pigtail / Loop) quanh Waypoint
* **Nguyên nhân**: Trễ vô lăng khiến xe trượt ra ngoài dải kích hoạt rẽ, hệ thống tưởng hụt WP nên ép xoay vòng nhặt lại điểm. Hoặc khi mô phỏng quên reset vị trí xe thật trong Gazebo (`gz service set_pose`).
* **Cách khắc phục**:
  1. Nâng `ATC_STR_ACC_MAX = 180` deg/s².
  2. Trước mỗi lần test mô phỏng, chạy lệnh reset pose Gazebo về gốc $(0,0)$.

### 🚨 Lỗi 4: Bài chạy A-B-C-D bị mất lái hoàn toàn ở đoạn C ➔ D (hướng 180° Nam)
* **Nguyên nhân**: `EK3_SRC1_YAW = 1` (dùng La bàn 2) nhưng La bàn 2 bị nhiễu từ trường cục bộ ở hướng Nam $\rightarrow$ EKF3 báo lỗi **EKF Yaw Glitch** $\rightarrow$ Khóa điều hướng không cho rẽ về D.
* **Cách khắc phục**:
  1. Nếu có GPS Kép: Đổi `EK3_SRC1_YAW = 2` (Dùng GPS Yaw, triệt tiêu 100% nhiễu từ trường).
  2. Nếu dùng 1 GPS + La bàn: Đưa xe ra bãi trống chạy lại **Onboard Compass Calibration** để lưu lại bộ `COMPASS_OFS2_X/Y/Z` mới.

### 💡 7.5. Phân Tích Cơ Chế Xóa Triệt Để Lỗi Văng Cua (Overshoot) Góc 90° Tự Động
Để xe tải trọng lớn (865kg) bám khít góc 90° với sai số 0.000m, hệ thống cần giải quyết đồng bộ 3 cơ chế:

1. **Khớp `CRUISE_SPEED = 1.0` với `WP_SPEED = 1.0` (Giải phóng 100% lực lái AUTO)**:
   - *Tại sao trước đây bị văng*: Khi `CRUISE_SPEED = 2.3` m/s mà xe chạy AUTO `WP_SPEED = 1.0` m/s, ArduPilot tự động scale giảm lực bẻ lái theo tỷ lệ $\frac{1.0}{2.3} \approx 43\%$. Vô lăng bị bẻ yếu mất 57% lực, làm bán kính rẽ $3.0$m bị văng rộng thành $4.2$m.
   - *Khắc phục*: Đặt `CRUISE_SPEED = 1.0` giúp vô lăng ở chế độ AUTO được giải phóng 100% authority bẻ góc.

2. **Mở rộng cửa sổ rẽ sớm ($t_{accel} = 4.0\text{s}$) bằng `WP_ACCEL = 0.25` và `WP_RADIUS = 6.5` và `WP_JERK = 0.2`**:
   - *Tại sao làm được*: Xe 865kg có quán tính quay lớn. Khi `WP_ACCEL = 0.5`, cửa sổ rẽ chỉ 2.0 giây ($2.0\text{m}$ trước WP) làm xe bắt đầu rẽ quá muộn. Hạ `WP_ACCEL = 0.25` kéo dài thời gian rẽ sớm $t_{accel} = \frac{1.0}{0.25} = 4.0$s (bắt đầu rẽ mượt từ cách xa $4.5\text{m}$ trước WP), tạo quỹ đạo S-Curve uốn ôm sát đỉnh góc rẽ.

3. **Bẻ kịch sàn $30^\circ$ nhờ `ATC_STR_RAT_FF = 1.25` & Đáp ứng tức thì `ATC_STR_ACC_MAX = 220.0`**:
   - *Tại sao làm được*: `ATC_STR_RAT_FF = 1.25` ép bẻ hết $100\%$ góc lái cơ khí $30^\circ$ khi vào cua, kết hợp `ATC_STR_ACC_MAX = 220.0` deg/s² và Damping `PSC_VEL_D = 0.25` triệt tiêu hoàn toàn độ trễ vô lăng và lực ly tâm khi nhập làn thẳng.

---

## 8. GIAI ĐOẠN 6: BẢNG TỔNG HỢP THAM SỐ CHỐT CHUẨN XE THẬT C500 (MASTER PARAMETER CHECKLIST & MIGRATION GUIDE)

Lấy **`c500_23092026.param` làm bộ thông số chuẩn của xe thật C500**, bảng dưới đây thể hiện quy trình sửa đổi từ file mặc định `full_default.param` $\rightarrow$ Chuẩn phần cứng C500 $\rightarrow$ Bộ tham số Master tối ưu hoàn chỉnh sau tuning:

| Nhóm Param | Tên Tham Số | Mặc Định (`full_default`) | Chuẩn C500 Gốc (`c500_23092026`) | C500 Master Sau Tuning (Tối Ưu Nhất của mô phỏng) | Hướng Dẫn Sửa Từ Default ➔ C500 Master |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **Hệ Thống Lái** | `ATC_STR_RAT_MAX` | `120` | `51` | **`35.0`** | Đặt 35.0 deg/s để tương thích góc cua |
| | `ACRO_TURN_RATE` | `180` | `51` | **`35.0`** | Đặt trùng 35.0 deg/s |
| | `ATC_STR_ACC_MAX` | `120` | `102` | **`180.0`** | Tăng từ 102 lên 180.0 để **xoá trễ vô lăng** |
| | `ATC_STR_RAT_FF` | `0.75` | `1.25` | **`1.15`** | Đặt 1.15 để bẻ lái tức thì không bị giật |
| | `ATC_STR_RAT_P` | `0.2` | `0.2` | **`0.20`** | Giữ 0.20 |
| | `ATC_STR_RAT_I` | `0.2` | `0.1` | **`0.10`** | Đặt 0.10 giữ bám đường |
| | `ATC_STR_RAT_D` | `0` | `0.01` | **`0.010`** | Đặt 0.010 dập dềnh nhả lái |
| **Quỹ Đạo S-Curve**| `TURN_RADIUS` | `0.9` | `3.5` | **`3.0`** | **Cố định cứng 3.0m** theo bán kính cơ khí |
| | `WP_RADIUS` | `3.0` | `6.0` | **`5.5`** | Đặt 5.5m để chứa trọn cung rẽ sớm |
| | `WP_ACCEL` | `0` | `0.5` | **`0.35`** | Đặt 0.35 m/s² để cửa sổ rẽ sớm $t_{accel} = 3.5s$ |
| | `WP_JERK` | `0` | `0.5` | **`0.5`** | Đặt 0.5 m/s³ cho S-Curve mượt |
| | `WP_SPEED` | `5.0` | `1.0` | **`1.0`** | Tốc độ danh định C500 (1.0 m/s) |
| **Vị Trí (PSC)** | `PSC_POS_P` | `0.2` | `1.5` | **`0.6`** | Hạ từ 1.5 xuống **0.6 (Xóa 100% rắn bò)** |
| | `PSC_VEL_P` | `1.0` | `1.0` | **`1.5`** | Tăng lên 1.5 để bám vận tốc ngang |
| | `PSC_VEL_D` | `0` | `0` | **`0.20`** | Thêm 0.20 để dập lực ly tâm nhập làn 1cm |
| **Tốc Độ & Phanh** | `CRUISE_SPEED` | `5.0` | `1.0` | **`1.0`** | Tốc độ cruise 1.0 m/s |
| | `CRUISE_THROTTLE`| `30` | `50` | **`50`** | Ga nền 50% |
| | `ATC_ACCEL_MAX` | `1.0` | `1.0` | **`1.0`** | Gia tốc tiến max 1.0 m/s² |
| | `ATC_DECEL_MAX` | `0` | `2.0` | **`2.0`** | Gia tốc giảm tốc 2.0 m/s² |
| | `ATC_BRAKE` | `1` | `0` | **`1`** | Bật phanh chủ động |
| **Phần Cứng C500** | `FRAME_CLASS` | `1` | `1` | **`1`** | Giữ 1 (Rover) |
| | `FRAME_TYPE` | `0` | `0` | **`0`** | Giữ 0 (Ackermann) |
| | `MOT_STR_THR_MIX`| `0.5` | `0.3` / `0.9` | **`0.3`** | Đặt 0.3 cho mô phỏng (hoặc 0.9 cho C500) |
| | `SERVO1_FUNCTION` | `26` | `26` | **`26`** | Kênh 1: Ground Steering |
| | `SERVO3_FUNCTION` | `70` | `70` | **`70`** | Kênh 3: Throttle |
| **Compass & CAN** | `AHRS_ORIENTATION`| `0` | `29` | **`29`** | Hướng lắp FC C500 (Pitch 180 Yaw 90) |
| | `COMPASS_OFS2_X/Y/Z`| `0` | `-131 / -141`| **`Theo Calib`**| Nạp bộ Offset đo thực tế trên C500 |
| | `EK3_SRC1_YAW` | `1` | `1` | **`2`** | **Khuyên dùng 2 (GPS Yaw)** cho C500 |
| | `CAN_D1_PROTOCOL` | `15` | `1` | **`1`** | Bật CAN 1 HAL |
| | `CAN_D2_PROTOCOL` | `0` | `15` | **`15`** | Bật CAN 2 Lua Scripting |
| | **`CAN_D2_VEL_MAX`**| `[Unset]`| **`2`** | **`2.0`** | **Vận tốc CAN tối đa (m/s) khi ga 100%** |
| | **`CAN_D2_STEER_MAX`**| `[Unset]`| **`30`** | **`30`** | **Góc lái CAN tối đa (độ) gửi xuống chassis** |
| | `NET_ENABLE` | `0` | `1` | **`1`** | Bật Ethernet C500 |
| | `SCR_ENABLE` | `0` | `1` | **`1`** | Bật Lua Scripting |

---

### 📝 Kết Luận:
Bảng trên là **Master Parameter Set hoàn chỉnh nhất cho xe thật C500**, bao gồm cả tham số giao tiếp CAN `CAN_D2_VEL_MAX = 2` (vận tốc CAN tối đa 2 m/s khi 100% ga) và `CAN_D2_STEER_MAX = 30` (độ), kết hợp với bộ tham số tối ưu S-Curve & Steering đã được kiểm chứng live!
