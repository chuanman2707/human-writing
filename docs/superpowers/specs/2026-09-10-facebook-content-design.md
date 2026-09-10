# Thiết Kế Hệ Thống Soạn Bài Facebook Tech / Builder từ X.com (Twitter)

- **Ngày tạo:** 2026-09-10
- **Dự án:** Facebook-Content (`/Users/binhan/Facebook-Content`)
- **Trạng thái:** Chờ duyệt (Pending Approval)

---

## 1. Bối cảnh & Mục tiêu (Context & Goals)

### 1.1. Bối cảnh
Người dùng thường xuyên theo dõi các thông tin, tin tức công nghệ, thread phân tích trên X.com (Twitter) về AI, Engineering, LLM, Agentic workflows. Người dùng muốn chuyển hóa các nội dung này thành bài đăng Facebook bằng tiếng Việt để chia sẻ với cộng đồng.

### 1.2. Vấn đề cần giải quyết
- Các bản dịch hoặc tóm tắt thông thường của AI thường mắc lỗi "AI slop": dùng văn mẫu sáo rỗng (*"trong thời đại số"*, *"không chỉ là A mà còn là B"*), kết luận đạo lý giáo điều (*"hãy nhớ rằng"*, *"chìa khóa mở ra tương lai"*), lạm dụng dấu gạch ngang và bộ ba liệt kê nhân tạo.
- Thuật ngữ công nghệ thường bị dịch máy móc sang tiếng Việt gượng gạo thay vì giữ nguyên từ ngữ chuyên môn tự nhiên mà dân dev/builder hay dùng.
- Cấu trúc bài viết chưa tối ưu cho nền tảng Facebook (cần dòng mở đầu giật hook, đoạn ngắn thoáng mắt, giữ reach bằng cách né chèn link trực tiếp vào bài).

### 1.3. Mục tiêu hệ thống
1. Tích hợp skill chuẩn quốc tế `humanizer` từ tác giả `blader` (https://github.com/blader/humanizer) vào dự án cục bộ.
2. Xây dựng skill chuyên biệt `fb-tech-writer` chứa bộ gen (DNA) văn phong Tech / Builder thực chiến dựa trên các mẫu bài viết xuất sắc đã qua kiểm chứng.
3. Thiết lập cơ chế điều phối tự động qua `AGENTS.md`: Người dùng chỉ cần paste text/link từ X.com vào chat, hệ thống tự động nhận diện và trả về bài viết Facebook hoàn chỉnh (Quick copy), không rườm rà.

---

## 2. Kiến trúc & Cấu trúc thư mục (Architecture)

```
Facebook-Content/
├── .agents/
│   └── skills/
│       ├── humanizer/
│       │   ├── SKILL.md            # Skill humanizer chuẩn (21 anti-slop patterns)
│       │   └── README.md           # Hướng dẫn tham khảo của thư viện gốc
│       └── fb-tech-writer/
│           └── SKILL.md            # Skill chuyên trách bài viết Facebook Tech tiếng Việt
├── docs/
│   └── superpowers/
│       └── specs/
│           └── 2026-09-10-facebook-content-design.md
├── AGENTS.md                       # Workspace Rules: Tự động kích hoạt khi có input từ X.com
└── README.md                       # Hướng dẫn sử dụng nhanh cho người dùng
```

---

## 3. Chi tiết các thành phần (Components Design)

### 3.1. Skill `humanizer` (`.agents/skills/humanizer/SKILL.md`)
- **Nguồn:** Clone chính xác từ `https://github.com/blader/humanizer`.
- **Nhiệm vụ:**
  - Phát hiện và triệt tiêu 21 thói quen viết của AI (Staging, Rhythm by rule, Inflation, Formatting by rule, Leftovers).
  - Đảm bảo thông tin giữ nguyên vẹn 100%, không bịa đặt số liệu hay chi tiết kỹ thuật.
  - Loại bỏ các từ nối máy móc, đảo ngữ nhân tạo.

### 3.2. Skill `fb-tech-writer` (`.agents/skills/fb-tech-writer/SKILL.md`)
Chứa toàn bộ bộ gen (DNA) đã được cô đọng từ các bài viết mẫu thực tế:

#### A. Công thức 5 phần (The 5-Part Formula)
1. **Hook giật mở đầu (The Alarm Hook):**
   - Chọn 1 trong 3 kiểu hook:
     - *Tin chấn động / Bước nhảy:* `🚨 [Tên công ty/model] vừa [hành động] [cảm xúc 🤯 hoặc 🥶]`
     - *Drama / Người trong cuộc:* Cảm thán đời thường, chỉ ra nghịch lý (*"Trời ơi, ông tech lead Google..."*).
     - *Deal tài chính / Quy mô khủng:* `🚨AI vừa có thêm đối thủ được bơm [số tiền] [emoji 🥶]`.
   - Có thể chêm 1 câu ngắn in hoa gây kích thích tò mò: `PORT QUA ẤY PORT GAME!`.
2. **Fact & Dữ liệu cốt lõi (Data & Specs):**
   - Nêu ngay thông số, số liệu, so sánh Before $\rightarrow$ After dạng gạch đầu dòng ngắn.
   - Trực diện, không mở bài dông dài.
3. **Góc nhìn Builder / Coder & Phép so sánh (Analogy):**
   - Đặt vấn đề dưới góc nhìn của người làm kỹ thuật (*"Với anh em build sản phẩm thì..."*, *"Coder tụi mình nhìn..."*).
   - Lấy thứ quen thuộc để giải thích thứ mới (*"Nếu Claude Code là agent trong màn hình, thì Muse là agent bước ra ngoài đời..."*).
4. **Insight cá nhân ("Mình nghĩ...") & Reality Check:**
   - **Bắt buộc mở đầu bằng *"Mình nghĩ..."*** để chỉ ra sự chuyển dịch của ngành (*paradigm shift*), kèm emoji `😰` hoặc `🥶`.
   - Luôn kèm theo sự tỉnh táo: ghi nhận hạn chế, nhắc nhở bài toán cần kiểm chứng độc lập, không tâng bốc mù quáng.
5. **Kêu gọi tương tác & Mẹo giữ reach (Growth Hack CTA):**
   - Nếu có tài nguyên, link repo, demo: dặn dò để dưới comment: *"Hình demo anh em để dưới comment, repo với setup guide ông ấy hứa share sớm."*
   - Kết bài bằng **1 câu hỏi mở** nhắm trúng trăn trở/workflow thực tế của anh em, kết thúc bằng `👇`.

#### B. Bộ từ điển & Quy tắc khử "AI Slop tiếng Việt"
- **Tuyệt đối cấm:**
  - *"Trong bối cảnh / Trong thời đại số / Trong kỷ nguyên công nghệ 4.0..."*
  - *"Không chỉ là A, mà còn là B..."* (cấu trúc Not-X-but-Y)
  - *"Bạn có bao giờ tự hỏi..."*, *"Hãy cùng khám phá..."*
  - *"Hãy nhớ rằng..."*, *"Đó chính là chìa khóa..."*, *"Tương lai thuộc về..."*
  - Liệt kê bộ ba rỗng tuếch (*"sáng tạo, đột phá và toàn diện"*).
  - In đậm label đầu dòng máy móc (**Ưu điểm:**, **Nhược điểm:**).
- **Từ ngữ nối câu tự nhiên:**
  - *"Đáng nói hơn, anh em có thể..."*
  - *"Sam Altman cũng nói thẳng là..."*
  - *"Nhìn qua các demo, nó đã đủ tốt để..."*
  - *"Tức là..."* (dịch số liệu kỹ thuật ra ngôn ngữ đời thường)
  - *"Cái đáng nói là..."*
- **Thuật ngữ chuẩn xác:** Giữ nguyên tiếng Anh cho các từ vựng kỹ thuật: *workflow, rollout, production, API, stop-motion, agent, Secure VM, repo, debug, benchmark, latency, context window, checkpoint, fine-tune, inference...*

### 3.3. Quy tắc hệ thống `AGENTS.md` (Workspace Rules)
- Tự động kích hoạt khi nhận thấy người dùng gửi nội dung xuất phát từ X.com (bài post, tweet thread, link x.com).
- Trực tiếp áp dụng kỹ năng `fb-tech-writer` kết hợp `humanizer`.
- Định dạng trả lời: Trả ngay bài viết hoàn chỉnh trong chat để người dùng 1-click copy đăng bài. Không hỏi đi hỏi lại những câu thừa.

---

## 4. Kế hoạch kiểm thử & Xác thực (Verification Plan)

1. **Kiểm tra cú pháp & cấu trúc thư mục:**
   - Đảm bảo `.agents/skills/humanizer/SKILL.md` đầy đủ nội dung theo chuẩn Antigravity.
   - Đảm bảo `.agents/skills/fb-tech-writer/SKILL.md` có đầy đủ các few-shot samples từ các bài mẫu người dùng cung cấp.
   - Đảm bảo `AGENTS.md` có format rules đúng quy định.
2. **Kiểm thử thực tế (End-to-End Prompt Test):**
   - Đưa một đoạn tweet tiếng Anh điển hình từ X.com (ví dụ: tweet công bố model mới hoặc update agent).
   - Kiểm tra kết quả tạo ra:
     - [ ] Có đúng cấu trúc 5 phần không?
     - [ ] Có loại bỏ sạch các pattern AI slop không?
     - [ ] Đoạn insight có mở đầu bằng *"Mình nghĩ..."* không?
     - [ ] Câu hỏi cuối bài có kết thúc bằng `👇` không?
     - [ ] Thuật ngữ công nghệ có được giữ chuẩn tiếng Anh không?
