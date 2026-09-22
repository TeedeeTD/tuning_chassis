# BẢNG TRA CỨU CHI TIẾT CÁC THAM SỐ TUNING ARDUROVER (TỪ MANUAL ĐẾN AUTO)

| PARAM | GIÁ TRỊ KHỞI ĐIỂM | THỨ NGUYÊN (ĐƠN VỊ TÍNH) | ĐỊNH NGHĨA / MÔ TẢ | MỨC ĐỘ ẢNH HƯỞNG / MỨC ĐỘ PHỤ THUỘC | GHI CHÚ (CÔNG THỨC) |
|---|---|---|---|---|---|
| `AHRS_ORIENTATION` | `0 (None)` | Enum | Hướng lắp đặt phần cứng mạch Pixhawk so với khung xe | Thay đổi khi mạch Pixhawk bị gắn xoay nghiêng/ngược so với đầu xe | Ví dụ `29` = ROTATION_YAW_270 |
| `COMPASS_ORIENT2` | `0 (None) hoặc 29` | Enum | Hướng lắp đặt phần cứng cảm biến La bàn 2 | Thay đổi khi cảm biến la bàn bị gắn xoay 90°, 180° hoặc 270° | 29 = YAW_270 (xoay sang trái 90°) |
| `COMPASS_DEC` | `-0.033` | rad | Độ lệch từ thiên địa lý của Trái Đất | ⚠️ Tuyệt đối KHÔNG dùng để bù góc chéo la bàn. Nhập sai gây lỗi DCM Yaw inconsistent 20 deg | Tại VN ≈ -1.9° (-0.033 rad) |
| `COMPASS_OFS2_X / Y / Z` | `-131 / -141 / -72` | mGauss | Offset Hard-Iron từ trường 3 trục của La bàn 2 | Dịch chuyển gốc đo từ trường 3 trục để khử từ tính khung xe | Cài tự động qua Onboard Mag Calib |
| `COMPASS_AUTO_ROT` | `1` | Flag | Tự động học và xoay bù góc la bàn khi xe chạy | 1 = Bật tự động học góc xoay la bàn khi xe vận hành | Tự động cân la bàn |
| `CRUISE_SPEED` | `1.5` | m/s | Vận tốc di chuyển chuẩn mong muốn khi chạy tự động | Tăng: Xe chạy nhanh hơn ở AUTO mode. Giảm: Xe chạy chậm lại | Cơ sở tính toán S-Curve & Feed-Forward |
| `CRUISE_THROTTLE` | `50` | % | Mức ga cơ bản (Throttle %) thực tế để đạt CRUISE_SPEED | Tăng: Nếu động cơ yếu cần nhiều ga hơn. Giảm: Nếu động cơ mạnh | Xác định bằng cách chạy Manual 50% ga |
| `GCS_PID_MASK` | `0` | Bitmask | Kích hoạt gửi dữ liệu piddesired / pidachieved lên đồ thị Tuning | 1 = Steering PID, 2 = Throttle/Speed PID | Dùng khi soi đồ thị Tuning Live |
| `ATC_SPEED_P` | `0.02` | Không | Hệ số P của bộ điều khiển PID Tốc độ | Tăng: Phản hồi ga nhạy hơn khi thiếu tốc. Quá cao: Giật ga nhấp nhô | Tăng nhẹ nếu pidachieved < piddesired |
| `ATC_SPEED_I` | `0.20` | Không | Hệ số I tích phân bù sai số tĩnh tốc độ | Tăng: Triệt tiêu sai số tốc độ khi chở nặng/chạy dốc. Quá cao: Chồm ga | Giới hạn bởi ATC_SPEED_IMAX |
| `ATC_SPEED_IMAX` | `0.30` | Không (0-1) | Giới hạn tối đa cho thành phần tích phân I tốc độ | Tăng: Mở rộng khả năng bù tải. Giảm: Chống vọt ga khi kẹt tải | Khuyên dùng 0.3 |
| `ATC_SPEED_FF` | `0.0` | Không | Hệ số Feed-Forward tốc độ | Xe Rover luôn đặt = 0.0 | Không dùng cho Rover |
| `ACRO_TURN_RATE` | `50` | deg/s | Tốc độ quay góc tay bẻ lái tối đa ở ACRO mode | Tăng: Tay lái ACRO nhạy hơn. Giảm: Lái đằm hơn | ACRO_TURN_RATE = ATC_STR_RAT_MAX |
| `ATC_STR_RAT_MAX` | `50` | deg/s | Tốc độ bẻ lái góc yaw tối đa cho phép của xe | Tăng: Cho phép rẽ gắt hơn. Giảm: Khống chế không cho bẻ gãy lái | ATC_STR_RAT_MAX = (CRUISE_SPEED / TURN_RADIUS) * 57.3 |
| `ATC_STR_RAT_FF` | `1.0` | Không | Hệ số Feed-Forward chuyển lệnh bẻ lái trực tiếp ra bánh | Tăng: Bánh bẻ sâu hơn khi gạt cần. Giảm: Bánh bẻ nông hơn | Tham số quan trọng nhất cho xe Ackermann |
| `ATC_STR_RAT_P` | `0.20` | Không | Hệ số P dập sai số tốc độ góc bẻ lái | Tăng: Bánh phản hồi dứt khoát khi gặp gờ gồ ghề. Quá cao: Rung vô-lăng | Chống nhiễu mặt đường |
| `ATC_STR_RAT_I` | `0.20` | Không | Hệ số I giữ góc rẽ duy trì theo thời gian | Tăng: Giữ góc rẽ ổn định khi ôm cua dài. Quá cao: Lượn hình sóng | Chống trôi lái |
| `ATC_STR_RAT_IMAX` | `1.0` | Không (0-1) | Giới hạn tích phân I góc bẻ lái | Khống chế lực giữ tích phân bẻ lái | Khuyên dùng 1.0 |
| `ATC_STR_RAT_D` | `0.10` | Không | Hệ số D vi phân dập dao động bẻ lái | Tăng: Dập lắc đuôi xe. Quá cao: Giật rung cơ cấu vô-lăng/Servo | Hạ về 0.05 nếu Servo bị rung |
| `ATC_STR_ANG_P` | `2.0` | 1/s | Hệ số P chuyển đổi giữa góc hướng bám và tốc độ bẻ lái | Tăng: Chuyển hướng ôm cua gắt hơn. Giảm: Ôm cua mượt êm hơn. Quá cao: Rắn bò | Cài 1.2 - 2.0 cho Ackermann |
| `ATC_STR_ACC_MAX` | `100` | deg/s² | Gia tốc bẻ lái tối đa của cơ cấu vô-lăng | Tăng: Đánh lái nhanh giật cục. Giảm: Đánh lái chuyển hướng mượt mà | Giúp chuyển hướng mượt |
| `PSC_POS_P` | `1.0` | 1/s | Hệ số P kéo xe về vệt đường mẫu khi bị dạt lề (Cross-Track Error) | Tăng: Kéo về vệt gắt hơn. Giảm: Phản hồi êm mượt hơn cho xe nặng tải. Quá cao: Zig-zag | Xe nặng (100-500kg) dùng 0.2, xe nhẹ dùng 1.0 |
| `PSC_VEL_P` | `1.0` | Không | Hệ số P bộ điều khiển vận tốc vị trí | Giữ mặc định = 1.0 | Giữ PSC_VEL_I = 0.0 |
| `PSC_VEL_FLTE` | `5.0` | Hz | Bộ lọc tần số thấp nhiễu vận tốc đầu vào Position Controller | Tăng: Lọc ít hơn. Giảm: Lọc nhiễu tần số cao từ GPS/Encoder mạnh hơn | Khuyên dùng 5 Hz |
| `PSC_VEL_FLTD` | `5.0` | Hz | Bộ lọc tần số thấp vi phân vận tốc vị trí | Lọc nhiễu vi phân vận tốc vị trí | Khuyên dùng 5 Hz |
| `WP_RADIUS` | `1.7` | m | Bán kính chấp nhận kích hoạt chuyển Waypoint | Tăng: Chuyển WP từ xa (cua rộng). Giảm: Ép đâm sát tâm WP. Nên bằng TURN_RADIUS | Đồng bộ WP_RADIUS = TURN_RADIUS |
| `TURN_RADIUS` | `1.7` | m | Bán kính đường cong ôm cua S-Curve thực tế của xe | Tăng: Mở rộng bán kính lượn cua. Giảm: Ép cua gắt hơn | Đo đạc bán kính rẽ thực tế của xe |
| `WP_SPEED` | `1.5` | m/s | Vận tốc chạy tự động trong bài Mission AUTO | Đồng bộ bằng với CRUISE_SPEED | WP_SPEED = CRUISE_SPEED |
| `MOT_STR_THR_MIX` | `0.8` | Không (0-1) | Tỉ lệ tự động hãm phanh/giảm ga theo góc bẻ lái (chuẩn cho xe Ackermann) | Tăng (0.8-1.0): Hãm tốc độ sâu khi góc lái bẻ gắt (cua 90°), giúp ôm cua mượt không văng lề. 0.0: Tắt | 0.8 - 1.0 khuyên dùng cho xe 500kg |
| `ATC_TURN_MAX_G` | `0.25 - 0.3` | G | Gia tốc ly tâm (gia tốc ngang) tối đa cho phép khi rẽ cua S-Curve | Tăng: Giữ tốc độ cao khi rẽ cua. Giảm: Ép hãm giảm tốc độ dài khi vào cua gắt | V_cua = sqrt(ATC_TURN_MAX_G * 9.81 * R) |
| `ATC_DECEL_MAX` | `1.0` | m/s² | Gia tốc phanh hãm tuyến tính S-Curve khi tiến vào WP cuối | Tăng (>0): Rải dốc phanh mượt dừng tại WP cuối. Đặt = 0: Trôi đà giật cục | ATC_DECEL_MAX = 1.0 - 3.0 m/s² |
| `PSC_AVOID_SPD` | `1.0` | m/s | Tốc độ giới hạn khi xe chạy tránh vật cản | Giảm: Giúp xe tránh vật cản an toàn không vọt lố lề ngoài | Khuyên dùng 1.0 m/s |
| `AVOID_MARGIN` | `1.0` | m | Khoảng cách lề an toàn phanh dừng trước vật cản | Tăng: Dừng xa vật cản hơn. Giảm: Đứng sát vật cản hơn | Khoảng cách phanh an toàn trước vật cản |
| `OA_TYPE` | `0` | Enum | Thuật toán bẻ lái tránh vật cản vòng quanh | 0 = Disabled (Dừng thẳng tại chỗ, không bẻ lái né nhánh) | Cho xe dừng thẳng chờ vật cản đi qua |
| `AVOID_ENABLE` | `1` | Flag | Bật chế độ phanh dừng an toàn trước vật cản | 1 = Bật Proximity Avoidance | Bật tính năng phanh dừng vật cản |
| `AVOID_BACKZ_SPD` | `0.0` | m/s | Khống chế tốc độ lùi khi gặp vật cản | 0.0 = Tuyệt đối không cài số lùi | Khóa số lùi |
| `CAN_D1_STEER_MAX` | `30` | Độ (°) | Góc bẻ bánh tối đa gửi qua CAN driver RoverXCAN | Mở rộng góc bẻ bánh tối đa qua CAN lên 30° | Giới hạn góc lái CAN |
