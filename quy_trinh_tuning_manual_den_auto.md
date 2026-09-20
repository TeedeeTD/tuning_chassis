# QUY TRÌNH TUNING HỆ THỐNG ARDUROVER TỪ MANUAL ĐẾN AUTO
**(Tài liệu Hướng dẫn Thực hành Chuẩn hóa cho Xe Ackermann)**

---

## 🎯 MỤC TIÊU PHẠM VI PHASE 1 (KIỂM SOÁT VẬN TỐC MANUAL)

Trước khi tiến hành bẻ lái hay chạy tự động (AUTO), mục tiêu Phase 1 là đảm bảo ở chế độ **MANUAL**:
> **Gửi lệnh vận tốc bao nhiêu thì 3 đường vận tốc thực tế phải KHỚP KHÍT NHAU:**
> 1. 🔴 **`RVCM.Speed`**: Vận tốc lệnh phát qua CAN từ Autopilot Pixhawk.
> 2. 🟢 **`RVST.Speed`**: Vận tốc phản hồi thực tế từ Encoder bánh xe khung gầm Chassis.
> 3. 🔵 **`THR.Speed`**: Vận tốc phản hồi thực tế ước lượng từ EKF / GPS trên Autopilot.

---

## 🛠️ QUY TRÌNH 5 BƯỚC TUNING CHI TIẾT TỪ ĐẦU

### BƯỚC 0: CHUẨN BỊ BÃI TEST & MISSION PLANNER
1. **Địa điểm:** Chọn sân bê tông hoặc mặt đất phẳng, rộng rãi (tối thiểu $15 \times 15\text{ m}$).
2. **Kết loại Telemetry:** Mở phần mềm Mission Planner, kết nối xe qua Telemetry/Radio.
3. **Mở Đồ thị Tuning trên Mission Planner:**
   - Ở màn hình **Flight Data**, nhìn xuống góc dưới cùng bên phải, tích chọn ô **`Tuning`**.
   - Bảng đồ thị Realtime sẽ hiện ra bên dưới màn hình HUD.

---

### BƯỚC 1: XÁC ĐỊNH `CRUISE_SPEED` & `CRUISE_THROTTLE`
Xe phải giữ tốc độ chuẩn trước khi tune bẻ lái.
1. Chuyển xe sang chế độ **`MANUAL` mode**.
2. Chạy xe tiến thẳng trên sân phẳng, giữ đều tay ga ở mức ổn định bạn mong muốn xe chạy tự động (thường là khoảng $50\%$ ga).
3. **Đọc 3 đường vận tốc trên Mission Planner/PlotJuggler:**
   - Kiểm tra `RVCM.Speed` $\approx$ `RVST.Speed` $\approx$ `THR.Speed` (ví dụ đạt $1.5\text{ m/s}$).
4. Vào menu **Config/Tuning** ➔ **Full Parameter List** và cài đặt:
   - **`CRUISE_SPEED` = 1.5** (m/s).
   - **`CRUISE_THROTTLE` = 50** (%).
   - Bấm **Write Params** để lưu lại.

---

### BƯỚC 2: TUNING PID TỐC ĐỘ (SPEED CONTROLLER)
1. Mở **Full Parameter List**, cài đặt:
   - **`GCS_PID_MASK` = 2** (2 = Throttle/Speed PID). Bấm **Write Params**.
2. Ở bảng đồ thị **Tuning** dưới màn hình HUD:
   - Kích đôi chuột trái (Double-click) vào vùng đồ thị màu đen.
   - Tích chọn 2 biến:
     - 🔴 **`piddesired`** (Tốc độ mong muốn do lệnh phát ra).
     - 🟢 **`pidachieved`** (Tốc độ thực tế từ GPS / Wheel Encoder).
3. Chuyển xe sang chế độ **`ACRO` mode**:
   - Đẩy tay ga tiến lên các mức tốc độ khác nhau ($0.5\text{ m/s} \rightarrow 1.0\text{ m/s} \rightarrow 1.5\text{ m/s}$).
4. Cân chỉnh PID Tốc độ (`ATC_SPEED_*`):
   - **Nếu `pidachieved` nằm thấp hơn `piddesired` (xe đáp ứng chậm):** Tăng nhẹ `ATC_SPEED_P` (từ `0.01` lên `0.02` hoặc `0.05`).
   - **Nếu tốc độ có sai số tĩnh (vẫn bị lệch nhẹ sau một lúc chạy):** Tăng nhẹ `ATC_SPEED_I` (từ `0.0` lên `0.2`, giới hạn `ATC_SPEED_IMAX = 0.3`).
   - *Lưu ý:* Với xe Rover, `ATC_SPEED_FF` luôn đặt bằng `0`.

---

### BƯỚC 3: TUNING FEED-FORWARD CHO STEERING (`ATC_STR_RAT_FF`)
Đây là bước quan trọng nhất đối với xe Ackermann.

#### A. Xác định Vận tốc Rẽ tối đa & Cài đặt Giới hạn:
1. Chuyển về **`MANUAL` mode**, cho xe chạy tiến ở `CRUISE_SPEED = 1.5 m/s`.
2. Đánh lái gắt nhất có thể mà bánh trước không bị trượt/khựng.
3. Cài `GCS_PID_MASK = 1` (Steering PID). Mở đồ thị Tuning:
   - Biến `pidachieved` chính là giá trị Gyro Z thực tế (tốc độ rẽ xe đạt được $\text{deg/s}$).
   - Nhìn đỉnh cao nhất của `pidachieved` (ví dụ vọt lên mốc $50^\circ/\text{s}$).
4. Tính toán theo công thức lý thuyết hoặc đo đạc:
   $$\text{ATC\\_STR\\_RAT\\_MAX} = \left(\frac{\text{CRUISE\\_SPEED}}{\text{TURN\\_RADIUS}}\right) \times \frac{180}{\pi} = \left(\frac{1.5}{1.7}\right) \times 57.3 \approx 50.5^\circ/\text{s}$$
5. Cài đặt tham số:
   - **`ACRO_TURN_RATE` = 50**
   - **`ATC_STR_RAT_MAX` = 50**

#### B. Chỉnh Feed-Forward (`ATC_STR_RAT_FF`):
1. Đặt lại `GCS_PID_MASK = 1` (Steering PID).
2. Tạm thời đặt: `ATC_STR_RAT_P = 0`, `ATC_STR_RAT_I = 0`, `ATC_STR_RAT_D = 0`.
3. Khởi điểm đặt **`ATC_STR_RAT_FF = 0.5`** (hoặc `1.0`).
4. Chuyển sang **`ACRO` mode**, đẩy ga cho xe tiến thẳng $1.5\text{ m/s}$, gạt cần bẻ lái các góc $15^\circ, 30^\circ, 45^\circ$:
   - **`piddesired`**: Tốc độ rẽ góc tay điều khiển yêu cầu.
   - **`pidachieved`**: Tốc độ rẽ xe đạt được thực tế từ IMU.
5. Cân chỉnh `ATC_STR_RAT_FF`:
   - **Nếu `pidachieved` thấp hơn `piddesired` (bánh bẻ chưa đủ sâu):** Tăng `ATC_STR_RAT_FF` (từ `0.5` $\rightarrow$ `0.7` $\rightarrow$ `1.0`).
   - **Nếu `pidachieved` cao hơn `piddesired` (rẽ quá gắt/vọt lố):** Giảm `ATC_STR_RAT_FF`.
   - **Mục tiêu:** Đường `pidachieved` bám trùng khít với `piddesired` ngay khi vừa gạt tay lái.

---

### BƯỚC 4: TUNING P, I, D CHO STEERING RATE (`ATC_STR_RAT_*`) & ANGLE P (`ATC_STR_ANG_P`)
Sau khi `ATC_STR_RAT_FF = 1.0` giúp xe bẻ lái đúng góc, thêm P, I, D để chống nhiễu mặt đường:
1. **Tăng `ATC_STR_RAT_P`:** Tăng từ `0.1` lên `0.2` giúp bánh trước phản hồi dứt khoát khi gặp gờ gồ ghề.
2. **Tăng `ATC_STR_RAT_I`:** Tăng từ `0.1` lên `0.2` giúp duy trì góc rẽ chính xác khi giữ nguyên tay lái lâu. (Đặt `ATC_STR_RAT_IMAX = 1.0`).
3. **Chỉnh `ATC_STR_RAT_D`:** Đặt `ATC_STR_RAT_D = 0.1` để dập lắc đuôi xe. (Nếu Servo/động cơ bẻ lái bị giật rung cơ học $\rightarrow$ hạ về `0.05`).
4. **Cài đặt `ATC_STR_ANG_P` (Steering Angle P Gain):** Cài đặt khởi điểm **`ATC_STR_ANG_P = 2.0`** (có thể tăng dần tới `4.0` ở ACRO mode) để chuyển đổi mượt mà giữa góc bám hướng mong muốn và tốc độ xoay bẻ lái, giúp xe ôm cua êm ái không bị gắt góc.

---

### BƯỚC 4.1: TUNING BỘ ĐIỀU KHIỂN VỊ TRÍ POSITION CONTROLLER (`PSC_POS_P` & `PSC_VEL_*`)
Sau khi đã làm chủ góc bẻ lái, cần tune bộ điều khiển vị trí 2 tầng (Cascaded Position-Velocity Controller) để xe tự kéo bám lại vệt đường mẫu (NavLine) khi bị dạt lệch tuyến.

1. **Vai trò các tham số:**
   - **`PSC_POS_P` (Position P-gain):** Quyết định lực kéo xe quay trở lại tuyến đường mong muốn khi phát hiện sai số bám vệt ngang (Cross-Track Error).
   - **`PSC_VEL_P` (Velocity P-gain):** Chuyển đổi sai số vị trí thành đáp ứng vận tốc dịch chuyển góc lái. Cài mặc định **`PSC_VEL_P = 1.0`** (giữ `PSC_VEL_I = 0.0` để tránh vọt lố).
   - **`PSC_VEL_FLTE` (Filter Hz):** Bộ lọc tần số thấp đầu vào vận tốc vị trí. Cài **`PSC_VEL_FLTE = 5 Hz`** (và `PSC_VEL_FLTD = 5 Hz`) để triệt tiêu nhiễu tần số cao từ cảm biến GPS/Encoder.
2. **Cách chọn giá trị khởi điểm `PSC_POS_P`:**
   - **Với xe Chassis nặng tải / kích thước lớn (100kg - 500kg):** Đặt khởi điểm **`PSC_POS_P = 0.20`** để xe phản hồi êm ái, tránh bị nảy vô-lăng hay trượt lốp.
   - **Với xe Chassis nhỏ / nhẹ / đáp ứng nhanh:** Đặt khởi điểm **`PSC_POS_P = 1.00`**.
3. **Quy trình Tune thực địa:**
   - **Sử dụng chế độ `STEERING Mode`** làm bước đệm trung gian giữa ACRO và AUTO. Ở chế độ này, người vận hành dùng tay gạt ga cho xe tiến thẳng và cố tình bẻ nhẹ tay lái lệch khỏi đường mục tiêu để kiểm thử khả năng xe tự kéo bám lại tuyến trước khi chuyển sang AUTO hoàn toàn.
   - **Nếu xe kéo về tuyến quá chậm / uể uải:** Tăng `PSC_POS_P` lên từng nấc `0.1` (ví dụ `0.2` $\rightarrow$ `0.3` $\rightarrow$ `0.5`).
   - **Nếu xe về tuyến gắt quá bị lượn hình sóng (Zig-zag / Rắn bò):** Giảm `PSC_POS_P` xuống.

---

### BƯỚC 5: KIỂM TRA CHẾ ĐỘ AUTO & KHẮC PHỤC 3 LỖI THƯỜNG GẶP

Tạo đường chạy Waypoint (hình vuông hoặc Ziczac) trên Mission Planner, chuyển **`AUTO` mode** và khắc phục các hiện tượng:

| Hiện tượng lỗi thực tế | Nguyên nhân chính | Cách khắc phục tham số chuẩn |
|---|---|---|
| **1. Xe di chuyển thẳng bị hình sin (Zig-zag / Rắn bò)** | `ATC_STR_ANG_P` quá cao hoặc `PSC_POS_P` nhạy quá đà. | • Giảm `ATC_STR_ANG_P` từ `2.0` xuống `1.2 - 1.5`.<br>• Giảm nhẹ `PSC_POS_P` về `1.0`. |
| **2. Overshoot Waypoint (Rẽ lấn lề / Trôi lề ngoài)** | `WP_RADIUS` không khớp với `TURN_RADIUS` vật lý. | • Đồng bộ `WP_RADIUS = TURN_RADIUS = 1.7m`.<br>• Tăng `PSC_POS_P` lên `1.2` để Pixhawk kéo xe bám làn gắt hơn. |
| **3. Vọt lố sau khi tránh vật cản (Avoidance Overshoot)** | Vận tốc tránh quá cao hoặc lề an toàn quá hẹp. | • Giảm `PSC_AVOID_SPD` xuống `1.0 m/s`.<br>• Tăng `AVOID_MARGIN = 1.0 - 1.5m`. |

---

### BƯỚC 5.1: VI ĐIỀU CHỈNH THỰC ĐỊA KHI XE MANG TẢI TRỌNG NẶNG
Sau khi chạy kiểm thử AUTO mode cơ bản thành công, tiến hành chất tải nặng thực tế lên xe (100kg - 500kg) và vi điều chỉnh:
1. Vi điều chỉnh $\pm 5\%$ các giá trị **`ATC_STR_RAT_P`** và **`PSC_POS_P`** tùy theo độ ma sát thực tế của bề mặt bãi test (đất, sỏi hoặc bê tông) để bù trượt tải khi ôm cua góc rộng.
2. Kiểm tra lại nhiệt độ động cơ và độ trễ phản hồi tay lái sau khi hoàn tất vi điều chỉnh.

---
### BƯỚC 5.2: CẤU HÌNH TỰ ĐỘNG GIẢM TỐC KHI VÀO CUA GẮT (> 45°)
Để xe tự động phân biệt: Cua nông (< 10°) giữ nguyên tốc độ $1.5\text{ m/s}$, còn Cua gắt (≥ 45°) tự động hãm phanh giảm tốc về mốc an toàn $1.0\text{ m/s}$ (không bị lịm ga < 0.5 m/s):
1. **Cấu hình Phân loại Waypoint tự động (`WP_PIVOT_ANGLE`):**
   - Đặt **`WP_PIVOT_ANGLE = 45`** (độ).
   - **Tác dụng:** 
     - Góc bẻ lái giữa các đoạn $< 45^\circ$: Xe cắt cua mượt (Fast Waypoint) giữ nguyên tốc độ.
     - Góc bẻ lái $\ge 45^\circ$: Pixhawk tự chuyển thành Normal Stop Waypoint, hãm phanh giảm tốc dừng/chậm lại rẽ hướng rồi mới đi tiếp.
2. **Cấu hình Giảm tốc theo Gia tốc ngang S-Curve (`ATC_TURN_MAX_G`):**
   - Đặt **`WP_RADIUS = 1.0`** (m) (siết bán kính chấp nhận điểm để xe không cắt cua từ quá xa).
   - Khống chế gia tốc ngang theo công thức $V_{\text{cua}} = \sqrt{\text{ATC\\_TURN\\_MAX\\_G} \times 9.81 \times R}$:
     $$\text{ATC\\_TURN\\_MAX\\_G} = \frac{(V_{\text{cua\\_mong\\_muon}})^2}{9.81 \times \text{TURN\\_RADIUS}}$$
   - **Áp dụng cho xe `TURN_RADIUS = 3.0m`, muốn vận tốc cua $1.0\text{ m/s}$:**
     - Đặt **`ATC_TURN_MAX_G = 0.035`** (G).
     - **Hiệu ứng:** Xe chạy thẳng duy trì $1.5\text{ m/s}$, khi vào cua gắt $R = 3.0\text{m}$ tự động hãm phanh về đúng $1.0\text{ m/s}$ mà không bao giờ bị lịm ga $< 0.5\text{ m/s}$.
---

## 📊 BẢNG SO SÁNH BIẾN ĐỔI CHI TIẾT GIỮA BẢN 03/08 VÀ BẢN 09/08 LATEST

Dưới đây là bảng 15 tham số đã được cải tiến từ `ban_chuan_da_fix_03_08_26.param` sang `09082026_latest.param`:

| Tham số | Bản chuẩn 03/08 | Bản Latest 09/08 | Ý nghĩa cải tiến kỹ thuật |
|---|---|---|---|
| **`CRUISE_SPEED`** | $1.0\text{ m/s}$ | **$1.5\text{ m/s}$** | Nâng vận tốc hoạt động chuẩn từ 1.0 lên 1.5 m/s. |
| **`CRUISE_THROTTLE`**| $70\%$ | **$50\%$** | Hạ % ga cơ bản học được phù hợp với lực động cơ. |
| **`WP_SPEED`** | $0.5\text{ m/s}$ | **$1.5\text{ m/s}$** | Đồng bộ tốc độ chạy AUTO mission khớp với `CRUISE_SPEED`. |
| **`ACRO_TURN_RATE`** | $160^\circ/\text{s}$ | **$50^\circ/\text{s}$** | Khống chế tốc độ rẽ Acro an toàn trùng với tính toán thực tế. |
| **`ATC_STR_RAT_MAX`**| $360^\circ/\text{s}$ | **$50^\circ/\text{s}$** | Khống chế Steering Rate Max $= 50^\circ/\text{s}$ tránh bẻ gãy lái. |
| **`ATC_TURN_MAX_G`** | $2.0\text{ G}$ | **$0.6\text{ G}$** | Hạ gia tốc ngang từ 2.0G xuống 0.6G để chống lật xe. |
| **`ATC_SPEED_P`** | $0.01$ | **$0.02$** | Tăng nhẹ P-gain ga giúp xe bám tốc độ nhạy hơn. |
| **`ATC_SPEED_I`** | $0.00$ | **$0.20$** | Tăng I-gain ga bù sai số tĩnh khi chạy dốc/mang tải. |
| **`ATC_SPEED_IMAX`** | $1.0$ | **$0.30$** | Siết giới hạn tích phân ga IMAX $= 0.3$ tránh bị chốm ga. |
| **`ATC_DECEL_MAX`** | $3.0\text{ m/s²}$ | **$5.0\text{ m/s²}$** | Tăng lực phanh an toàn dừng xe nhanh hơn. |
| **`ATC_STR_ACC_MAX`**| $360^\circ/\text{s²}$ | **$100^\circ/\text{s²}$** | Giảm gia tốc bẻ lái giúp tay lái chuyển hướng êm mượt. |
| **`TURN_RADIUS`** | $1.4\text{ m}$ | **$1.7\text{ m}$** | Cập nhật bán kính cua đo đạc thực tế của xe. |
| **`WP_RADIUS`** | $2.0\text{ m}$ | **$1.7\text{ m}$** | Đồng bộ `WP_RADIUS = TURN_RADIUS = 1.7m`. |
| **`PSC_POS_P`** | $0.20$ | **$1.00$** | Tăng P-position từ 0.2 lên 1.0 giúp bám vệt đường S-Curve chuẩn. |
| **`CAN_D1_STEER_MAX`**| $20^\circ$ | **$30^\circ$** | Mở rộng góc bẻ bánh tối đa qua CAN lên $30^\circ$. |
