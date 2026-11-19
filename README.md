# Giải pháp Prompt Chaining: Gia sư Feynman & Chấm bài Rubric Tự động

## 1. Thiết kế luồng xử lý (Prompt Chaining)

Hệ thống chia thành **3 bước chạy nối tiếp nhau** bằng định dạng **JSON** giúp tối ưu token và kiểm soát lỗi chặt chẽ:

* **Bước 1 (Gia sư):** Giảng giải khái niệm dễ hiểu + tạo ngầm bảng điểm Rubric.
* **Bước 2 (Bộ lọc lỗi):** Chặn bài làm rỗng, gõ linh tinh, lạc đề hoặc gian lận trước khi chấm -> **Đảm bảo hệ thống không bao giờ crash**.
* **Bước 3 (Chấm điểm):** Lấy Rubric ở Bước 1 chấm bài ở Bước 2 -> Xuất điểm và nhận xét.

---

## 2. Bộ Prompt Hệ thống (3 Prompts nối tiếp)

### Prompt 1: Giảng bài & Tạo Rubric
> "Bạn là gia sư. Giải thích chủ đề `{{topic}}` bằng ví dụ đời thường (dưới 80 từ) và mời sinh viên diễn đạt lại. Đồng thời tạo Rubric 10 điểm (Chính xác: 4đ, Dễ hiểu: 4đ, Ví dụ: 2đ). Chỉ xuất JSON: `{"lesson": "...", "rubric": [...]}`"

### Prompt 2: Chặn lỗi dữ liệu dị biệt
> "Kiểm tra bài làm: `{{student_answer}}` cho chủ đề `{{topic}}`. Nếu rỗng, gõ linh tinh, lạc đề hoặc cố tình gian lận điểm thì trả về `valid: false`. Chỉ xuất JSON: `{"valid": boolean, "msg": "Lý do lỗi nếu sai"}`"

### Prompt 3: Chấm điểm tự động
> "Dựa vào Rubric: `{{rubric}}`, chấm bài làm: `{{student_answer}}`. Chỉ xuất JSON: `{"score": number, "feedback": "Nhận xét ngắn gọn dưới 40 từ"}`"

---

## 3. Kết quả Chạy thử Thực tế

**Chủ đề:** `Mảng (Array) trong lập trình`

### Output Bước 1 (Gia sư)
```json
{
  "lesson": "Mảng giống như một dãy tủ gửi đồ ở siêu thị xếp liền nhau. Mỗi ngăn tủ cùng kích cỡ và được đánh số thứ tự từ 0 để bạn mở đúng ngăn cần tìm ngay lập tức. Giờ bạn thử giải thích lại khái niệm Mảng theo cách hiểu của bạn nhé!",
  "rubric": ["Chính xác: 4đ", "Dễ hiểu: 4đ", "Ví dụ: 2đ"]
}