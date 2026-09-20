# Hướng Dẫn Tuning Bộ Điều Khiển PID

## 1. Tổng quan các chế độ điều khiển (Modes of Control)

Một bộ điều khiển PID gồm 3 thành phần:

| Thành phần | Vai trò |
|---|---|
| **P** (Proportional) | Ổn định (stability) – mức tăng có thể điều chỉnh (adjustable gain) |
| **I** (Integral) | Bù trừ độ lệch tĩnh (offset) – compensate for offset |
| **D** (Derivative) | Tăng tốc độ đáp ứng của van – speed up valve movement |

### Bảng so sánh các chế độ điều khiển

| Chế độ | Đáp ứng điển hình | Ưu điểm / Nhược điểm |
|---|---|---|
| **On/Off** | Dao động liên tục quanh setpoint | (+) Chi phí thấp, đơn giản<br>(+) Sai lệch vận hành nằm ngoài dung sai kiểm soát cần thiết |
| **P** | Vọt lố ban đầu, sau đó ổn định ở mức lệch off‑set | (+) Đơn giản, ổn định, dễ setup<br>(–) Độ lệch ban đầu khá cao, duy trì ở mức lệch ổn định (còn offset) |
| **P + I** | Vọt lố nhẹ, hội tụ về setpoint không còn offset | (+) Không còn offset (sai lệch dư)<br>(+) Cần tăng dải tỷ lệ (P-band) để khắc phục hiện tượng overshoot khi đáp ứng<br>(+) Có khả năng đáp ứng overshoot khi hoạt động |
| **P + D** | Đáp ứng nhanh với thay đổi | (+) Ổn định<br>(+) Đáp ứng nhanh với thay đổi<br>(–) Có một chút offset (sai lệch dư) |
| **P + I + D** | Đáp ứng tối ưu, ít overshoot | (+) Khả năng điều khiển tốt nhất<br>(+) Không có sai lệch dư<br>(+) Độ overshoot ở mức tối thiểu<br>(–) Phức tạp về kỹ thuật lắp đặt<br>(–) Chi phí cao |

> **Lưu ý quan trọng:** Chế độ **On/Off** là hành động điều khiển ít phức tạp nhất, cung cấp mức độ điều khiển cần thiết tối thiểu — nên luôn được xem xét lựa chọn trước khi dùng chế độ phức tạp hơn.

---

## 2. Các khái niệm cơ bản (Key Concepts)

- **Time constant (τ):** Thời gian cần thiết để tín hiệu hoặc đầu ra đạt được giá trị cuối cùng từ giá trị ban đầu của nó, khi mà tỉ lệ tăng ban đầu được duy trì — thường lấy mốc **63,2%** thay đổi tính từ lúc bắt đầu thay đổi cho đến khi ổn định.

- **Hunting (sự bất ổn):** Hiện tượng tạo ra độ lệch thay đổi liên tục do sự dao động ổn không liên tục quanh setpoint. Có thể do:
  - P-band quá hẹp (too narrow)
  - I-time (thời gian tích phân) quá ngắn
  - D-time (thời gian đạo hàm) quá dài
  - Time constant dài hơn thời gian chết (dead time) trong hệ thống loại quy trình

- **Lag (độ trễ phản hồi):** Tồn tại ở **cả** hệ thống điều khiển (control system) và quá trình chịu điều khiển (process under control).

- **Rangeability (khả năng phạm vi):** Liên quan đến van điều khiển, là tỉ lệ giữa:

  $$\text{Rangeability} = \frac{\text{maximum controllable flow}}{\text{minimum controllable flow}}$$

  Đây là khả năng kiểm soát được giữa các đặc tính của van sẽ được duy trì.

---

## 3. Lắp đặt và vận hành hệ thống điều khiển quá trình

### 3.1 Các thành phần liên quan

- **Valve** – van
- **Actuator and sensor** – cơ cấu chấp hành & cảm biến
- **Power and signal lines** – nguồn và dây tín hiệu
- **Electrical wiring** – hệ thống dây điện / sự đấu nối

### 3.2 Thành phần trọng tâm: Controller (Bộ điều khiển)

Quá trình (process) hoặc ứng dụng thực tế thường tạo ra những thay đổi chậm hơn thời gian phản hồi của hệ thống điều khiển. Đây là lý do vì sao các tham số của bộ điều khiển (**P-band, I-time, D-time**) phải được điều chỉnh cho từng ứng dụng cụ thể.

Có 3 tùy chọn liên quan đến setting: **too wide, too narrow, correct (đúng)**.

---

## 4. P-Band (Dải tỷ lệ)

| Trường hợp | Hiệu ứng |
|---|---|
| **(a) P-band quá rộng (too wide)** | – Offset lớn<br>+ Hệ thống ổn định (system stable) |
| **(b) P-band quá hẹp (too narrow)** | + Giảm offset so với (a)<br>– Mất ổn định & dao động |
| **(c) Lý tưởng (ideal)** | + Giảm offset tốt<br>+ Ổn định |

**Đồ thị đặc trưng:** đường (a) tiệm cận chậm và ổn định ở mức lệch cao; đường (b) dao động mạnh quanh setpoint; đường (c) tiệm cận nhanh, êm về setpoint với offset nhỏ nhất còn chấp nhận được.

---

## 5. Integral Time – I-time (Thời gian tích phân)

| Trường hợp | Hiệu ứng |
|---|---|
| **(a) I-time quá ngắn** | Xảy ra dao động (oscillation) |
| **(b) I-time quá mức (quá dài)** | Mất quá nhiều thời gian để quay về setpoint |
| **(c) Ideal** | Trở về setpoint nhanh nhất mà không có overshoot hay dao động nào |

---

## 6. Derivative Time – D-time (Thời gian đạo hàm)

| Trường hợp | Hiệu ứng |
|---|---|
| **(a) D-time quá mức (quá dài)** | Gây ra sự thay đổi nhanh chóng quá mức → overshoot + dao động |
| **(b) D-time quá ngắn** | Tiếp cận setpoint quá lâu (đáp ứng chậm) |
| **(c) Ideal** | Đưa quá trình về điểm đặt càng nhanh, càng ổn định càng tốt, phù hợp với xu hướng thay đổi tốt |

---

## 7. Bảng tổng kết ảnh hưởng của từng thông số

| Action (tăng) | Stability (ổn định) | Response (đáp ứng) |
|---|---|---|
| ↑ P-band | ↑ (tăng ổn định) | ↓ (chậm hơn) |
| ↑ I-time | ↑ (tăng ổn định) | ↓ (chậm hơn) |
| ↑ D-time | ↓ (giảm ổn định) | ↑ (nhanh hơn) |

> Ghi nhớ nhanh: **P-band và I-time tăng → hệ ổn định hơn nhưng đáp ứng chậm hơn.** **D-time tăng → đáp ứng nhanh hơn nhưng dễ mất ổn định (overshoot/dao động) hơn.**

---

## 8. Phương pháp Ziegler–Nichols (Z-N Method)

Phương pháp đáp ứng tần số Ziegler–Nichols, đôi khi được gọi là **critical oscillation method**, rất hiệu quả trong việc thiết lập các tham số bộ điều khiển cho tải thực tế.

**Nguyên lý:** Phương pháp sử dụng bộ điều khiển như một **bộ khuếch đại (amplifier)** để đạt tới **điểm bất ổn định (point of instability)**. Tại điểm bất ổn định, toàn bộ hệ thống dao động liên tục theo cách mà biến quá trình (process variable) dao động xung quanh điểm đặt với biên độ không đổi.

### 8.1 Quan hệ Gain – P-band và hiện tượng Valve Hunting

| Khi Gain ↑, P-band ↓ | Khi Gain ↓, P-band ↑ |
|---|---|
| → Kém ổn định | → Ổn định quá trình tốt |
| → Tăng valve hunting | → Giảm valve hunting (reduce valve hunting) |

### 8.2 Quy trình (Procedure) chọn cài đặt tham số PID theo Z-N method

1. **Loại bỏ I-action** bằng cách tăng IAT (I-time / thời gian tích phân) lên **max**.
2. **Loại bỏ D-action** bằng cách chỉnh **Tp = 0** (D-time = 0).
3. Đợi đến khi process **stabilizes** (ổn định).
4. **Giảm P-band** (tức tăng Gain) cho đến khi đạt đến **điểm hệ thống dao động ổn định không đổi** (critical/sustained oscillation).
5. Tại điểm không ổn định (dao động ổn định), đo **chu kỳ (Time period T)** và ghi lại **P-band thực tế** tương ứng.
6. Sử dụng cặp giá trị (T, P-band) này làm điểm bắt đầu, tính toán các cài đặt bộ điều khiển phù hợp theo **bảng Ziegler–Nichols** bên dưới.

### 8.3 Bảng Ziegler–Nichols (tính toán tham số)

| Control mode | P-band | I-time | D-time |
|---|---|---|---|
| **P** | P-band × 2 | 0 | 0 |
| **P + I** | P-band × 2,2 | Time period (T) / 1,2 | 0 |
| **P + I + D** | P-band × 1,7 | T / 2 | T / 8 |

*(P-band ở đây là giá trị P-band đo được tại điểm dao động tới hạn (ultimate P-band); T là chu kỳ dao động đo được tại điểm đó – Ultimate Period.)*

---

## 9. Checklist tuning nhanh (tóm tắt thực hành)

1. Xác định chế độ điều khiển phù hợp (bắt đầu từ đơn giản: On/Off → P → PI → PD → PID) theo mức yêu cầu độ chính xác thực tế — luôn ưu tiên chọn chế độ đơn giản nhất đáp ứng đủ yêu cầu.
2. Nếu chọn PID, dùng **Ziegler–Nichols** để tìm điểm dao động tới hạn (Gain tới hạn / P-band tới hạn và chu kỳ T).
3. Áp dụng bảng Z-N để tính P-band, I-time, D-time ban đầu.
4. Quan sát đáp ứng thực tế:
   - Còn **offset lớn, ổn định** → P-band đang quá rộng.
   - **Dao động, mất ổn định** → P-band quá hẹp, hoặc I-time quá ngắn, hoặc D-time quá dài.
   - **Đáp ứng chậm, mất nhiều thời gian về setpoint** → I-time quá dài, hoặc D-time quá ngắn.
   - **Overshoot lớn kèm dao động khi có nhiễu nhỏ** → D-time quá dài.
5. Tinh chỉnh tinh (fine-tune) từng thông số theo hướng ngược lại với triệu chứng quan sát được, thay đổi từng bước nhỏ và quan sát lại đáp ứng.
6. Mục tiêu cuối: đáp ứng nhanh, không hoặc rất ít overshoot, không dao động, không còn offset (sai lệch dư).

---

*Tài liệu được tổng hợp và biên soạn lại từ ghi chú viết tay "Basic Control Theory" và "Installation and Commissioning of Process Controls" (mục 5.1 – 5.2, phần 7.1 – 7.2).*