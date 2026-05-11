# EUM / Predictioneer’s Game

## Đặc tả thuật toán: Old Model vs New Model (Bản Tiếng Việt)

---

# PHẦN 1 — OLD MODEL (EUM CỔ ĐIỂN)

## 1. Bài toán

Cho một tập các tác nhân (actor) cạnh tranh trên **một trục chính sách 1 chiều**, dự báo:

* kết quả chính sách cuối cùng
* vị trí cuối của các actor

---

## 2. Đầu vào

Với mỗi actor i:

* x_i: vị trí chính sách (scalar)
* c_i: quyền lực / khả năng ảnh hưởng (clout)
* s_i: mức độ quan tâm (salience)

Trọng số suy ra:

w_i = c_i * s_i

---

## 3. Giả định cốt lõi

1. Actor là người tối đa hóa utility (hợp lý)
2. Utility giảm khi outcome xa vị trí mong muốn
3. Kết quả xung đột phụ thuộc vào liên minh (coalition)
4. Không mô hình hóa explicit quá trình thương lượng
5. Mô hình một bước (one-shot), không có học theo thời gian

---

## 4. Mô hình đối đầu cặp (pairwise)

Với mỗi cặp i và j:

Mỗi actor thứ ba k sẽ “ngả” về phía mang lại utility cao hơn.

Hiệu utility:

ΔU_k = |x_k - x_j| - |x_k - x_i|

Nếu ΔU_k > 0 → k ủng hộ i
Nếu ΔU_k < 0 → k ủng hộ j

---

## 5. Xác suất thắng

Xác suất i thắng j:

P_i(j) =
(Σ_{k: ΔU_k > 0} w_k * |ΔU_k|)
/ (Σ_{tất cả k} w_k * |ΔU_k|)

---

## 6. Expected Utility khi tấn công

Actor i cân nhắc có nên challenge j:

EU_i = P_i(j) * U_i(x_i)
+ (1 - P_i(j)) * U_i(x_j)

Nếu EU_i > utility của status quo → i sẽ challenge

---

## 7. Ước lượng outcome hệ thống

Outcome thường được xấp xỉ:

Trung bình trọng số:

x* ≈ Σ(w_i * x_i) / Σ(w_i)

Hoặc loại dần các vị trí bị dominate

---

## 8. Thuật toán (pseudo-code)

Input actors {i}
Tính w_i

For mỗi cặp (i, j):
Tính ΔU_k
Tính P_i(j)
Tính EU_i

Tổng hợp các contest
Tính outcome x*

Return x*

---

## 9. Cơ sở biện minh tính đúng đắn

### Điểm mạnh

* Phản ánh cấu trúc quyền lực dựa trên coalition
* Đơn giản, dễ tính toán
* Phù hợp khi:

  * actor rõ ràng
  * issue 1 chiều
  * thương lượng không quá phức tạp

### Nền tảng lý thuyết

* Expected Utility Theory
* Spatial voting
* Coalition aggregation

### Hạn chế

* Không có bargaining explicit
* Không có dynamics
* Không có uncertainty
* Phụ thuộc mạnh vào input

---

# PHẦN 2 — NEW MODEL (MÔ HÌNH LẶP + THƯƠNG LƯỢNG)

## 1. Bài toán

Dự báo outcome trong bối cảnh:

* thương lượng lặp lại
* có ép buộc (coercion) và thỏa hiệp (compromise)
* có bất định về đối thủ

---

## 2. Đầu vào

Với mỗi actor i:

* x_i: vị trí
* c_i: clout
* s_i: salience
* r_i: resolve (độ cứng vs sẵn sàng thỏa hiệp)

Ngoài ra:

* belief về type đối thủ (hawk/dove, retaliatory/pacific)
* tham số cost: α, τ, γ, φ

---

## 3. Giả định cốt lõi

1. Chính trị là quá trình thương lượng lặp
2. Actor có thể: đề xuất, ép buộc, chống trả, thỏa hiệp
3. Outcome phụ thuộc cả preference và chiến lược
4. Actor không biết chắc type của nhau
5. Có learning qua các vòng

---

## 4. Cấu trúc mỗi vòng

Tại vòng t:

For mỗi cặp có hướng (i, j):
tạo game G_ij

Số game mỗi vòng: N(N-1)

---

## 5. Game song phương G_ij

1. i quyết định có propose không
2. nếu propose, j chọn:

   * accept
   * counter
   * resist
   * coerce back
   * compromise

Outcome có thể:

* status quo
* i thắng
* j thắng
* compromise
* clash

---

## 6. Hàm utility

Utility gồm 2 thành phần:

U_i = f(
khoảng cách policy,
mức độ agreement vs resolve
)

Thường dạng Cobb-Douglas

---

## 7. Xác suất thắng (có coalition)

P_i(j) =
(Σ_{k: U_k(i) > U_k(j)} c_k s_k (U_k(i) - U_k(j)))
/ (Σ_{tất cả k} c_k s_k |U_k(i) - U_k(j)|)

Ý nghĩa: hệ thống actor quyết định thắng thua

---

## 8. Chọn proposal nội sinh

Actor i chọn proposal p_ij:

p_ij = argmax_p E[U_i]

với constraint về khả năng chấp nhận / chống trả

---

## 9. Giải game

Mỗi G_ij được giải bằng:

* Bayesian belief
* quyết định tuần tự

Output:

* equilibrium outcome
* proposal credible

---

## 10. Gộp kết quả

Với mỗi actor i:

x_i(t+1) = trung bình trọng số các proposal hướng tới i

Trọng số ≈ c * s

---

## 11. Outcome hệ thống

Outcome y(t):

trung bình trọng số của tất cả proposal

* smoothing qua các vòng

---

## 12. Cập nhật trạng thái

Sau mỗi vòng:

* cập nhật x_i
* cập nhật resolve r_i
* cập nhật salience s_i
* cập nhật clout c_i (tùy chọn)
* update belief (Bayes)

---

## 13. Điều kiện dừng

Dừng nếu:

* payoff kỳ vọng giảm
  HOẶC
* utility hệ thống không tăng nữa

---

## 14. Thuật toán (pseudo-code)

Khởi tạo state

Repeat:
For mỗi cặp (i, j):
build G_ij
tính utility
tính P_i(j)
chọn proposal
solve equilibrium

For mỗi i:
update x_i

tính outcome hệ thống
update state + belief

Until dừng

Return outcome

---

## 15. Cơ sở biện minh tính đúng đắn

### Điểm mạnh

* Mô hình hóa thương lượng thực
* Có coercion + compromise
* Có uncertainty
* Có dynamics
* Thực tế hơn old model

### Nền tảng lý thuyết

* Game theory (Bayesian)
* Expected utility
* Coalition
* Iterated interaction

### Bằng chứng thực nghiệm

* Thường dự báo tốt hơn trong môi trường cạnh tranh
* Phù hợp political bargaining

---

## 16. Hạn chế

* Phụ thuộc mạnh vào input
* Giả định 1 chiều
* Nhiều heuristic
* Tính toán phức tạp
* Khó tái lập hoàn toàn

---

# SO SÁNH TỔNG KẾT

| Tiêu chí    | Old Model  | New Model             |
| ----------- | ---------- | --------------------- |
| Bản chất    | Tĩnh       | Lặp                   |
| Tương tác   | Ngầm       | Thương lượng explicit |
| Uncertainty | Không      | Có                    |
| Dynamics    | Không      | Có                    |
| Độ phức tạp | Thấp       | Cao                   |
| Độ thực tế  | Trung bình | Cao                   |

---

# Ý CHÍNH QUAN TRỌNG

Old model trả lời:
→ Ai thắng dựa trên preference và power

New model trả lời:
→ Actor tương tác thế nào để hội tụ outcome

---
