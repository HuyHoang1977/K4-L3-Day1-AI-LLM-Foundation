# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Với temperature thấp như 0.0, phản hồi thường ổn định, trực tiếp và ít biến đổi giữa các lần chạy. Khi tăng lên 1.0 hoặc 1.5, model có xu hướng đa dạng hơn về cách diễn đạt và ý tưởng, nhưng cũng dễ lan man hoặc kém nhất quán hơn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ chọn temperature khoảng 0.2-0.4 cho chatbot hỗ trợ khách hàng. Mức này giữ câu trả lời ổn định, chính xác và dễ kiểm soát, nhưng vẫn đủ tự nhiên trong cách diễn đạt.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Workload này tạo ra 10.000 x 3 x 350 = 10.500.000 token output mỗi ngày. Theo bảng giá trong lab, GPT-4o tốn khoảng 105 USD/ngày còn GPT-4o-mini khoảng 6,30 USD/ngày, tức GPT-4o đắt khoảng 16,7 lần. GPT-4o đáng dùng cho phân tích phức tạp hoặc câu trả lời cần chất lượng cao; mini phù hợp cho phân loại, FAQ và tác vụ lặp lại với lưu lượng lớn.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Prompt dành cho giáo viên tiểu học thường tạo câu trả lời ngắn hơn, dùng từ phổ thông, phép so sánh và ví dụ gần gũi. Prompt dành cho chuyên gia tài chính thường tạo câu trả lời dài hơn, dùng thuật ngữ như sổ cái phân tán, cơ chế đồng thuận và mật mã học. System prompt định hình vai trò, giọng điệu, mức độ chi tiết và cách chọn ví dụ, nhưng không thay thế yêu cầu cụ thể của user.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với một đoạn tiếng Việt khoảng 100 từ, tôi sẽ đo token bằng `count_tokens` rồi so với 100 / 0.75, tức khoảng 133 token. Ví dụ nếu tiktoken cho 160 token thì chênh lệch là (160 - 133) / 133, khoảng 20%. Tiếng Việt có nhiều dấu, từ ghép và cách tách token khác tiếng Anh nên cùng số từ có thể tạo ra nhiều token hơn; đây là lý do nên đo bằng tokenizer thực tế thay vì chỉ đếm từ.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng khi người dùng phải chờ model tạo câu trả lời dài, vì chữ xuất hiện sớm giúp giảm cảm giác chờ và cho phép theo dõi tiến độ. Non-streaming phù hợp với tác vụ ngắn, xử lý nền hoặc khi ứng dụng cần nhận toàn bộ kết quả để parse JSON, kiểm tra hợp lệ rồi mới hiển thị.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff làm các lần retry sau cách nhau lâu hơn, nhờ đó giảm áp lực lên API khi hệ thống đang quá tải và cho server thời gian hồi phục. Nếu hàng nghìn client cùng retry sau đúng một khoảng cố định, chúng sẽ tạo các đợt request đồng thời, tiếp tục làm nghẽn server và dễ dẫn đến lỗi dây chuyền.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Persona tôi chọn là: "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn, rõ ràng bằng tiếng Việt và dùng ví dụ thực tế khi giải thích khái niệm khó." Cụm "trả lời ngắn gọn" giúp phù hợp với giao diện terminal và giảm token; chỉ định "bằng tiếng Việt" giữ ngôn ngữ nhất quán; còn "dùng ví dụ thực tế" giúp người mới dễ hiểu hơn.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất là history chỉ giữ ba lượt nên trợ lý có thể quên thông tin ở đầu phiên. Một cải thiện cụ thể là tóm tắt các lượt cũ trước khi cắt history: sau mỗi vài lượt, gọi một prompt riêng để tạo bản tóm tắt ngắn, lưu tóm tắt đó như một system hoặc user context, rồi chỉ giữ các lượt gần nhất cùng bản tóm tắt. Cách này giảm số token nhưng vẫn bảo toàn thông tin quan trọng.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
