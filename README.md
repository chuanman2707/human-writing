# Facebook Tech/Builder Content Engine 🚀

> **Hệ thống tự động chuyển đổi tin tức công nghệ từ X.com (Twitter) thành bài viết Facebook chuẩn văn phong Tech / Builder thực chiến.**

---

## 1. Giới Thiệu Tổng Quan

Trong kỷ nguyên bùng nổ của AI và công nghệ, X.com (Twitter) là "thánh địa" nơi các nhà sáng lập, kỹ sư cấp cao và các phòng thí nghiệm nghiên cứu (OpenAI, Meta, Anthropic, Google...) công bố những phát kiến mới nhất. Tuy nhiên, việc đưa những thông tin này lên Facebook thường gặp phải hai vấn đề lớn:
1. **Dịch máy thô cứng hoặc AI slop:** Các bài viết tạo bằng LLM thông thường dính đầy các câu sáo rỗng (*"Trong bối cảnh thời đại số..."*, *"Không chỉ là A mà còn là B..."*), nghe như phát ngôn PR của phòng marketing thay vì góc nhìn của người làm kỹ thuật.
2. **Bị bóp tương tác (Reach penalty):** Chèn link ngoài trực tiếp vào bài viết khiến thuật toán Facebook hạn chế phân phối; cấu trúc đặc kín chữ khiến người đọc lướt qua trên điện thoại.

**Facebook-Content Engine** ra đời để giải quyết triệt để các vấn đề trên. Dự án kết hợp bộ kỹ năng tự động điều phối (**AGENTS.md**), kỹ năng viết chuyên sâu (**fb-tech-writer**), bộ lọc chống AI slop 21 quy tắc (**humanizer**), và kho bài mẫu thực tế (**few-shot corpus**) để biến mọi tweet/thread thành bài viết Facebook thu hút, giàu giá trị kỹ thuật và tối ưu tương tác.

---

## 2. Quy Trình Sử Dụng Hàng Ngày (Daily Workflow)

Quy trình sử dụng được thiết kế tối giản, đạt tốc độ cao nhất cho người dùng:

```
[Lướt X.com thấy tin hay] ──► [Paste link hoặc text vào Chat] ──► [Nhận 1 Code Block hoàn chỉnh] ──► [1-Click Copy & Đăng FB]
```

### Bước 1: Thu thập tin tức từ X.com
Khi thấy một bài đăng, cập nhật sản phẩm, mega-thread hay phát ngôn công nghệ đáng chú ý trên X.com, bạn chỉ cần:
- Copy **đường dẫn (link URL)** của bài tweet.
- Hoặc copy **toàn bộ nội dung văn bản (text)** nếu link bị khóa đăng nhập.

### Bước 2: Dán vào cửa sổ chat Antigravity
Gửi link hoặc văn bản trực tiếp vào cửa sổ chat của workspace này.
- Hệ thống có cơ chế **Always-On Trigger**: Tự động nhận diện và kích hoạt quy trình soạn bài.
- Bạn **không cần** gõ câu lệnh dài dòng hay giải thích bối cảnh.

### Bước 3: Nhận bài viết chuẩn Output Contract
Hệ thống tự động chạy ngầm **2-Pass Pipeline** (Soạn thảo $\rightarrow$ Khử AI Slop) và trả về:
1. **Một code block duy nhất:** Chứa toàn bộ nội dung bài đăng đã căn chỉnh khoảng cách dòng chuẩn xác để đọc trên di động.
2. **Gợi ý Comment:** Link nguồn, repo GitHub hoặc demo để bạn thả vào comment đầu tiên (giúp bảo toàn reach).
3. **Gợi ý Visual:** Ý tưởng hình ảnh screenshot, GIF hoặc biểu đồ benchmark phù hợp nhất.

### Bước 4: Đăng bài & Tinh chỉnh (nếu muốn)
- Nhấn nút **Copy** trên code block và đăng trực tiếp lên Facebook cá nhân hoặc Fanpage.
- Nếu muốn điều chỉnh góc nhìn (ví dụ: *"hãy nhấn mạnh hơn vào chi phí inference"* hoặc *"viết ngắn lại dưới dạng punchy post"*), bạn chỉ cần ra lệnh ngắn gọn. Agent sẽ giữ nguyên văn phong builder và chuẩn đầu ra.

---

## 3. Kiến Trúc Hệ Thống (Architecture)

Hệ thống được tổ chức thành các thành phần chuyên biệt, vận hành đồng bộ:

```
Facebook-Content/
├── AGENTS.md                                     # Quy tắc điều phối cho Antigravity / Agent Studio
├── CLAUDE.md                                     # Quy tắc chỉ dẫn dự án cho Anthropic Claude Code
├── README.md                                     # Tài liệu hướng dẫn sử dụng & quy chuẩn vận hành
├── .gitignore                                    # Cấu hình bỏ qua tài liệu nội bộ (docs/) & file rác
├── .claude/                                      # Chuẩn Kỹ năng cho Anthropic Claude Code
│   └── skills/
│       ├── fb-tech-writer/                       # Kỹ năng viết bài Tech Facebook chuyên sâu
│       │   ├── SKILL.md                          # Persona, Công thức 5 phần, Vietnamese Anti-Slop
│       │   └── references/examples.md            # Kho 6 bài mẫu thực tế kèm giải phẫu (Few-shot)
│       └── humanizer/                            # Bộ lọc khử văn phong AI (blader/humanizer)
│           ├── SKILL.md                          # 21 quy tắc loại bỏ thói quen hành văn chatbot
│           └── README.md
└── .agents/                                      # Chuẩn Kỹ năng cho Antigravity Workspace
    └── skills/
        ├── fb-tech-writer/
        └── humanizer/
```

### Chi Tiết Từng Thành Phần:
- **`AGENTS.md` (Workspace Rules):** File quy tắc cốt lõi của workspace. Định nghĩa cơ chế kích hoạt tự động ngầm, quy trình 2-pass pipeline, ràng buộc đầu ra (Output Contract), và quy chuẩn phản hồi không lời chào rườm rà.
- **`fb-tech-writer` (Core Skill):** Định hình Persona một Senior Engineer/Builder thực chiến; triển khai **Công thức 5 phần** (Hook $\rightarrow$ Data $\rightarrow$ Builder Analogy $\rightarrow$ "Mình nghĩ..." $\rightarrow$ Open CTA); xử lý các trường hợp ngoại lệ (URL bị chặn, mega-thread dài, tin hype).
- **`references/examples.md` (Few-Shot Corpus):** Kho dữ liệu gồm 6 bài viết Facebook xuất sắc đã được kiểm chứng thực tế (ChatGPT Images 2.5, Meta Muse Agent, Navier-Stokes $1M, Skyrim trên iPad, Mistral €3B, SemiAnalysis Benchmaxxed). Mỗi bài đều có giải phẫu 5 phần giúp mô hình bám sát nhịp điệu.
- **`humanizer` (Anti-Slop Engine):** Được tích hợp từ dự án mã nguồn mở `blader/humanizer`, chuyên rà soát và loại bỏ 21 tật xấu điển hình của AI (staging, nhịp điệu máy móc, thổi phồng tầm quan trọng, lạm dụng gạch nối ngang em-dash, cấu trúc Not-X-but-Y...).

---

## 4. Công Thức 5 Phần Cốt Lõi (The 5-Part Formula)

Mỗi bài viết được xuất ra đều tuân thủ cấu trúc tối ưu trải nghiệm đọc lướt trên newfeed:

| Khối | Tên khối | Vai trò & Quy cách |
|:---:|---|---|
| **1** | **Hook Giật Mở Đầu** | 2 dòng đầu tiên trước nút "Xem thêm". Dùng cảnh báo `🚨 ... 🤯/🥶`, drama đời thường `Trời ơi... 😭`, hoặc con số tài chính chấn động. |
| **2** | **Fact & Dữ Liệu Cốt Lõi** | Số liệu định lượng (thời gian, % tốc độ, Before/After). Trình bày ngắn gọn bằng bullet points (-) hoặc câu ngắn, không dùng đoạn văn dài đặc chữ. |
| **3** | **Góc Nhìn Builder & Analogy** | Soi chiếu tác động đối với coder/product maker. Dùng các công cụ dev quen thuộc (Claude Code, git, test, reverse engineering) làm hệ quy chiếu. |
| **4** | **Insight Cá Nhân & Reality Check** | **Bắt buộc mở đầu bằng `Mình nghĩ...`** kèm emoji `😰` hoặc `🥶`. Chỉ ra paradigm shift của ngành kèm cái đầu lạnh phản biện (không tin mù quáng vào benchmark, lưu ý hạn chế thực tế). |
| **5** | **Growth Hack CTA** | Dẫn link xuống comment. Kết bài bằng **ĐÚNG 1 câu hỏi mở** kích thích tranh luận kỹ thuật, kết thúc bằng icon chỉ tay xuống `👇`. |

---

## 5. Quy Tắc Định Dạng Facebook & Mẹo Tăng Trưởng Tương Tác (Growth Hacks)

Để đạt lượt hiển thị (organic reach) và tương tác thảo luận cao nhất trên Facebook:

1. **Zero External Link in Post Body (Tuyệt đối không để link ngoài trong thân bài):**
   - Thuật toán của Meta chủ động bóp reach bài viết chứa liên kết dẫn người dùng rời khỏi nền tảng.
   - Luôn đưa link gốc, GitHub repo, whitepaper xuống **phần bình luận đầu tiên**. Trong bài viết chỉ cần để câu dẫn nhẹ nhàng: *"Link demo/repo anh em xem dưới comment nhé"*.
2. **Mobile First & Skimmable (Khoảng trắng thoáng mắt cho di động):**
   - Hơn 90% người dùng lướt Facebook trên màn hình smartphone.
   - Mỗi đoạn văn chỉ dài từ 1 đến 3 câu, cách nhau bởi một dòng trống hoàn chỉnh. Tránh tuyệt đối các "bức tường chữ" (wall of text).
3. **100% Native English Tech Terms (Giữ nguyên thuật ngữ kỹ thuật bản địa):**
   - Lập trình viên Việt Nam giao tiếp hàng ngày bằng thuật ngữ tiếng Anh gốc.
   - Sử dụng tự nhiên: `workflow`, `rollout`, `production`, `API`, `repo`, `latency`, `inference`, `benchmark`, `context window`... Không bao giờ dịch thô gượng gạo sang tiếng Việt.
4. **Văn phong "Mình" & "Anh em" (Peer-to-Peer Authenticity):**
   - Độc giả tech chỉ tin cậy những người cùng làm nghề. Giữ xưng hô thân mật, gần gũi, ngang hàng.
   - Tránh xa giọng điệu thuyết giảng, dạy đời hoặc văn mẫu tin tức báo chí.
5. **Open Question với Icon `👇` (Kích hoạt thuật toán tương tác):**
   - Thuật toán Facebook ưu tiên bài viết có thảo luận 2 chiều sâu sắc ở phần bình luận.
   - Một câu hỏi mở trúng băn khoăn thực tế ở cuối bài giúp biến người đọc thụ động thành người tham gia tranh luận.
