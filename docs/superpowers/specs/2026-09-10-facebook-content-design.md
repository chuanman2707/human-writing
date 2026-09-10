# Thiết Kế Hệ Thống Soạn Bài Facebook Tech / Builder từ X.com (Twitter)

- **Ngày cập nhật:** 2026-09-10 (Đã tích hợp phản biện từ Senior Spec Reviewer)
- **Dự án:** Facebook-Content (`/Users/binhan/Facebook-Content`)
- **Trạng thái:** Đã tinh chỉnh & Sẵn sàng triển khai (Ready for Implementation)

---

## 1. Bối cảnh & Mục tiêu (Context & Goals)

### 1.1. Bối cảnh
Người dùng thường xuyên theo dõi các bài viết, tin tức công nghệ, thread phân tích trên X.com (Twitter) về AI, Engineering, LLM, Agentic workflows. Người dùng muốn chuyển hóa các nội dung này thành bài đăng Facebook bằng tiếng Việt để chia sẻ với cộng đồng công nghệ.

### 1.2. Vấn đề cần giải quyết
- **Bệnh "AI slop" của mô hình ngôn ngữ:** Dùng văn mẫu sáo rỗng (*"trong thời đại số"*, *"không chỉ là A mà còn là B"*), kết luận đạo lý giáo điều (*"hãy nhớ rằng"*, *"chìa khóa mở ra tương lai"*), lạm dụng dấu gạch ngang và bộ ba liệt kê nhân tạo.
- **Dịch thuật máy móc:** Thuật ngữ công nghệ bị dịch thô sang tiếng Việt gượng gạo thay vì giữ nguyên từ ngữ chuyên môn tự nhiên mà dân dev/builder hay dùng.
- **Thiếu tính nền tảng (Facebook Typography & UI):** Facebook không hỗ trợ Markdown phong phú; bài viết cần có khoảng trắng ngắt nhịp (blank lines), dòng ngắn dễ đọc lướt trên điện thoại, và né bị thuật toán bóp tương tác khi chèn link trực tiếp.

### 1.3. Mục tiêu hệ thống
1. Tích hợp skill chuẩn quốc tế `humanizer` từ tác giả `blader` (https://github.com/blader/humanizer) vào dự án cục bộ tại `.agents/skills/humanizer/`.
2. Xây dựng skill chuyên biệt `fb-tech-writer` chứa bộ gen (DNA) văn phong Tech / Builder thực chiến dựa trên kho 6 bài mẫu xuất sắc đã qua kiểm chứng.
3. Thiết lập cơ chế điều phối tự động qua `AGENTS.md`: Tự động nhận diện khi người dùng gửi nội dung từ X.com, xử lý theo quy trình 2 bước ngầm (2-Pass Pipeline) và trả về bài viết Facebook hoàn chỉnh trong 1 code block để 1-click copy ngay lập tức.

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
│           ├── SKILL.md            # Core instructions, 5-part formula & Vietnamese anti-slop rules
│           └── references/
│               └── examples.md     # Kho 6 bài mẫu thực tế (Few-shot corpus)
├── docs/
│   └── superpowers/
│       └── specs/
│           └── 2026-09-10-facebook-content-design.md
├── AGENTS.md                       # Workspace Rules: Tự động điều phối khi có input từ X.com
└── README.md                       # Hướng dẫn sử dụng nhanh cho người dùng
```

---

## 3. Chi tiết các thành phần (Components Design)

### 3.1. Skill `humanizer` (`.agents/skills/humanizer/SKILL.md`)
- **Nguồn:** Clone nguyên bản từ `https://github.com/blader/humanizer`.
- **Frontmatter:**
  ```yaml
  ---
  name: humanizer
  description: Use when reviewing or rewriting draft text to eliminate AI writing tells, synthetic transitions, bloated metaphors, and formulaic structures
  ---
  ```
- **Nhiệm vụ:**
  - Triệt tiêu 21 thói quen viết của AI (Staging, Rhythm by rule, Inflation, Formatting by rule, Leftovers).
  - Giữ nguyên sự thật và dữ liệu gốc 100%, không bịa đặt số liệu hay chi tiết kỹ thuật.

### 3.2. Skill `fb-tech-writer` (`.agents/skills/fb-tech-writer/SKILL.md`)
- **Frontmatter:**
  ```yaml
  ---
  name: fb-tech-writer
  description: Use when converting tech posts, announcements, tweets, or threads from X.com into natural, high-engagement Vietnamese Facebook posts for tech builders and engineers
  ---
  ```
- **Nhiệm vụ:** Áp dụng bộ gen viết bài Facebook Tech thực chiến và kết nối với kho tài liệu mẫu tại `references/examples.md`.

#### A. Công thức 5 phần (The 5-Part Formula)
1. **Hook giật mở đầu (The Alarm Hook):**
   - *Kiểu 1 (Tin chấn động / Bước nhảy):* `🚨 [Tên công ty/model] vừa [hành động] [cảm xúc 🤯 hoặc 🥶]`
   - *Kiểu 2 (Drama / Người trong cuộc):* Cảm thán đời thường, chỉ ra nghịch lý (*"Trời ơi, ông tech lead Google..."*).
   - *Kiểu 3 (Deal tài chính / Quy mô khủng):* `🚨AI vừa có thêm đối thủ được bơm [số tiền] [emoji 🥶]`.
   - Có thể chêm 1 câu ngắn in hoa gây kích thích tò mò: `PORT QUA ẤY PORT GAME!`.
2. **Fact & Dữ liệu cốt lõi (Data & Specs):**
   - Nêu ngay thông số, số liệu, so sánh Before $\rightarrow$ After dạng gạch đầu dòng ngắn.
   - Trực diện, đi thẳng vào bản chất, không mở bài dông dài.
3. **Góc nhìn Builder / Coder & Phép so sánh (Analogy):**
   - Đặt vấn đề dưới góc nhìn người làm kỹ thuật (*"Với anh em build sản phẩm thì..."*, *"Coder tụi mình nhìn..."*).
   - Lấy thứ quen thuộc để giải thích thứ mới (*"Nếu Claude Code là agent trong màn hình, thì Muse là agent bước ra ngoài đời..."*).
4. **Insight cá nhân ("Mình nghĩ...") & Reality Check:**
   - **Bắt buộc mở đầu bằng *"Mình nghĩ..."*** để chỉ ra sự chuyển dịch của ngành (*paradigm shift*), kèm emoji `😰` hoặc `🥶`.
   - Luôn kèm theo sự tỉnh táo: ghi nhận hạn chế, nhắc nhở bài toán cần kiểm chứng độc lập, không tâng bốc mù quáng.
5. **Kêu gọi tương tác & Mẹo giữ reach (Growth Hack CTA):**
   - Nếu có tài nguyên (repo, demo, link đọc thêm): dặn dò để dưới comment (*"Hình demo anh em để dưới comment, repo với setup guide ông ấy hứa share sớm."*).
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

---

### 3.3. Chuẩn định dạng đầu ra (Output Contract for Quick Copy)

Để phục vụ trải nghiệm người dùng 1-click copy:
1. **Toàn bộ nội dung bài đăng Facebook phải được bọc trong DUY NHẤT 1 code block:**
   ````text
   ```text
   [Toàn bộ nội dung bài viết Facebook ở đây, sẵn sàng để copy và post ngay]
   ```
   ````
2. **Không có bất kỳ lời chào hay câu dẫn mở đầu** (*"Chào anh, đây là bài viết..."* hay *"Dưới đây là nội dung..."*).
3. **Phía dưới code block chỉ cung cấp 2 gợi ý ngắn gọn:**
   - 💬 **Gợi ý Comment:** [Link gốc / Repo GitHub / Tài liệu đính kèm để post dưới comment giữ tương tác]
   - 🖼 **Gợi ý Visual:** [Loại hình ảnh / demo GIF / screenshot phù hợp để đính kèm bài]

---

### 3.4. Quy trình xử lý 2 bước ngầm (Internal 2-Pass Pipeline)

Khi nhận được nội dung từ người dùng:
- **Pass 1 - Soạn thảo cấu trúc (Drafting via fb-tech-writer):** Bóc tách thông điệp cốt lõi từ X.com, định hình 5 phần (Hook $\rightarrow$ Data $\rightarrow$ Analogy $\rightarrow$ "Mình nghĩ..." $\rightarrow$ CTA).
- **Pass 2 - Rà soát & Tinh lọc (Humanizing & Anti-Slop Check):** Soát lỗi dựa trên 21 quy tắc của `humanizer` và bộ lọc tiếng Việt:
  - Có câu nào mang cấu trúc *Not-X-but-Y* không? $\rightarrow$ Viết lại trực diện.
  - Có câu nào mang giọng điệu thuyết giảng không? $\rightarrow$ Cắt bỏ.
  - Thuật ngữ công nghệ đã ở dạng nguyên bản chưa? $\rightarrow$ Khôi phục tiếng Anh chuẩn.

---

### 3.5. Xử lý các tình huống ngoại lệ (Edge Cases & Fallbacks)

1. **Người dùng chỉ gửi Link X.com (URL Only):**
   - Agent dùng web search/fetch để lấy nội dung tweet.
   - *Fallback:* Nếu link bị chặn (403, yêu cầu login X.com), agent phản hồi ngắn gọn: *"Link X này bị giới hạn truy cập. Anh em paste giúp mình text hoặc ảnh chụp tweet vào đây nhé!"*
2. **Mega-Thread dài (10 - 20 tweets):**
   - Áp dụng cơ chế **Thread Synthesis**: Không dịch từng tweet. Trích xuất đúng 1 thông điệp đắt giá nhất + 3-4 thông số kỹ thuật then chốt, cô đọng thành 1 bài Facebook hoàn chỉnh.
3. **Tweet siêu ngắn (Chỉ 1 câu + ảnh/video):**
   - Giữ bài viết dạng ngắn gọn, sắc sảo (Punchy post). Tuyệt đối không bịa đặt số liệu để kéo dài bài.
4. **Tweet mang tính PR / Hype / Benchmark mập mờ:**
   - Đưa vào góc nhìn phản biện khách quan trong phần *"Mình nghĩ..."* và Reality check (như case SemiAnalysis vạch trần benchmaxxed).

---

## 4. Kế hoạch kiểm thử & Xác thực (Verification Plan)

1. **Kiểm tra cú pháp & cấu trúc:**
   - Đảm bảo các thư mục `.agents/skills/humanizer/` và `.agents/skills/fb-tech-writer/` được tạo đầy đủ.
   - Đảm bảo `references/examples.md` lưu trữ trọn vẹn 6 bài mẫu gốc.
2. **Kiểm thử thực tế (End-to-End Test):**
   - Chạy thử nghiệm với 1 tin tức/tweet thực tế từ X.com.
   - Kiểm tra kết quả có tuân thủ 100% Output Contract (1 code block duy nhất, cấu trúc 5 phần, không có AI slop).
