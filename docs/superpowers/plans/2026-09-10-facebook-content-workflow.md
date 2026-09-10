# Facebook Tech Content Workflow Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Xây dựng hệ thống tự động trong workspace `/Users/binhan/Facebook-Content` biến các bài đăng/thread từ X.com thành bài viết Facebook tiếng Việt chuẩn Tech / Builder thực chiến, loại bỏ triệt để AI slop, tích hợp skill `humanizer` từ GitHub và skill `fb-tech-writer`.

**Architecture:** Sử dụng kiến trúc Antigravity Agent Customizations bao gồm:
1. Workspace skill `humanizer` từ GitHub `blader/humanizer` để khử 21 patterns AI slop.
2. Workspace skill `fb-tech-writer` chứa bộ gen viết bài Facebook Tech và kho tài liệu tham chiếu 6 bài mẫu (`references/examples.md`).
3. Quy tắc `AGENTS.md` điều phối tự động nhận diện nội dung X.com và xuất kết quả theo Output Contract (1 code block duy nhất).

**Tech Stack:** Antigravity Agent Customizations (Markdown, YAML Frontmatter, Prompt Engineering, Git).

**Spec:** [docs/superpowers/specs/2026-09-10-facebook-content-design.md](file:///Users/binhan/Facebook-Content/docs/superpowers/specs/2026-09-10-facebook-content-design.md)

## Global Constraints

- Mọi skill phải tuân thủ chuẩn Antigravity: thư mục `.agents/skills/<name>/SKILL.md` với YAML frontmatter hợp lệ.
- YAML frontmatter của `SKILL.md` phải có `name` và `description` dạng `Use when...`.
- Toàn bộ bài đăng Facebook đầu ra phải tuân thủ Output Contract: duy nhất 1 code block, không lời chào/kết dông dài, kèm 2 gợi ý Comment & Visual.
- Tuyệt đối cấm các mẫu câu AI slop tiếng Việt (*"trong bối cảnh", "không chỉ là A mà còn là B", "hãy nhớ rằng", "chìa khóa mở ra tương lai"*).
- Thuật ngữ công nghệ phải giữ nguyên tiếng Anh chuẩn (*workflow, rollout, production, API, agent, benchmark, latency...*).

---

### Task 1: Cài đặt và cấu hình skill `humanizer` từ GitHub

**Files:**
- Create: `.agents/skills/humanizer/SKILL.md`
- Create: `.agents/skills/humanizer/README.md`

**Interfaces:**
- Consumes: Nội dung từ repo `https://github.com/blader/humanizer` (đặc biệt là `SKILL.md` và `README.md`).
- Produces: Skill `.agents/skills/humanizer/SKILL.md` có đầy đủ 21 quy tắc khử AI slop và frontmatter chuẩn Antigravity.

- [ ] **Step 1: Fetch nội dung từ repo `blader/humanizer` và tạo file `SKILL.md`**

Tải và lưu nội dung `SKILL.md` gốc, chuẩn hóa frontmatter:
```yaml
---
name: humanizer
description: Use when reviewing or rewriting draft text to eliminate AI writing tells, synthetic transitions, bloated metaphors, and formulaic structures
---
```

- [ ] **Step 2: Lưu file `README.md` của humanizer vào `.agents/skills/humanizer/README.md`**

Lưu tài liệu hướng dẫn tham khảo gốc của tác giả `blader`.

- [ ] **Step 3: Xác minh tính toàn vẹn của skill `humanizer`**

Kiểm tra:
- File `.agents/skills/humanizer/SKILL.md` tồn tại và có đầy đủ 21 patterns (Staging, Rhythm by rule, Inflation, Formatting by rule, Leftovers).
- Frontmatter chuẩn format YAML.

- [ ] **Step 4: Commit**

```bash
git add .agents/skills/humanizer
git commit -m "feat(skills): add humanizer skill from blader/humanizer"
```

---

### Task 2: Xây dựng kho dữ liệu 6 bài mẫu (Few-shot corpus)

**Files:**
- Create: `.agents/skills/fb-tech-writer/references/examples.md`

**Interfaces:**
- Consumes: 6 bài viết mẫu thực tế do người dùng cung cấp.
- Produces: File `references/examples.md` cấu trúc hóa 6 bài mẫu kèm phân tích giải phẫu từng bài (Hook, Fact, Builder Analogy, "Mình nghĩ...", CTA).

- [ ] **Step 1: Viết file `references/examples.md`**

Ghi lại nguyên văn 6 bài mẫu:
1. *ChatGPT Images 2.5:* Giữ likeness qua nhiều lượt chỉnh sửa, API Flare vs Sunburst, paradigm shift từ tạo ảnh sang production.
2. *Meta MUSE agent:* Cá nhân hóa, Secure VM, WhatsApp, analogy với Claude Code.
3. *OpenAI giải Navier-Stokes $1M:* 10.000 agents, Lean verification, bước nhảy từ coding agent sang toán học thuần túy.
4. *Tech lead Google chạy Skyrim trên iPad qua GPT-6 Astra:* Châm biếm drama, agent tự debug overnight, đối lập Google vs OpenAI.
5. *Mistral AI gọi vốn €3B:* Đặt cược của châu Âu, inference economics, cạnh tranh coding agent.
6. *SemiAnalysis vạch trần Gemini & Muse Spark "benchmaxxed":* Terminal-Bench 2.1 vs 4.0, reality check về overfit benchmark.

- [ ] **Step 2: Kiểm tra nội dung file `references/examples.md`**

Đảm bảo đầy đủ cả 6 bài, format rõ ràng, không bị thiếu sót nội dung nào.

- [ ] **Step 3: Commit**

```bash
git add .agents/skills/fb-tech-writer/references/examples.md
git commit -m "feat(skills): add 6-sample few-shot corpus for fb-tech-writer"
```

---

### Task 3: Xây dựng skill chuyên trách `fb-tech-writer`

**Files:**
- Create: `.agents/skills/fb-tech-writer/SKILL.md`

**Interfaces:**
- Consumes: Spec thiết kế và kho bài mẫu tại `references/examples.md`.
- Produces: Skill hoàn chỉnh `.agents/skills/fb-tech-writer/SKILL.md` điều khiển toàn bộ tư duy và phong cách viết bài Facebook Tech tiếng Việt.

- [ ] **Step 1: Viết nội dung file `.agents/skills/fb-tech-writer/SKILL.md`**

Nội dung bao gồm:
1. Frontmatter chuẩn:
   ```yaml
   ---
   name: fb-tech-writer
   description: Use when converting tech posts, announcements, tweets, or threads from X.com into natural, high-engagement Vietnamese Facebook posts for tech builders and engineers
   ---
   ```
2. Công thức 5 phần chi tiết (The 5-Part Formula).
3. Bộ quy tắc chống AI slop tiếng Việt (từ cấm, từ nối tự nhiên, quy tắc typography).
4. Output Contract (Bọc trong 1 code block duy nhất + 2 dòng gợi ý Comment & Visual).
5. Internal 2-Pass Pipeline (Drafting $\rightarrow$ Humanizing/Anti-Slop Check).
6. Edge Cases handling (Link X bị chặn, Thread dài synthesis, Tweet ngắn, Reality check trước Hype).
7. Liên kết tham chiếu đến `references/examples.md`.

- [ ] **Step 2: Xác minh cú pháp và cấu trúc file `SKILL.md`**

Đảm bảo không có lỗi định dạng YAML, các quy tắc chặt chẽ, không có placeholder TBD/TODO.

- [ ] **Step 3: Commit**

```bash
git add .agents/skills/fb-tech-writer/SKILL.md
git commit -m "feat(skills): add fb-tech-writer skill with Vietnamese anti-slop and 5-part formula"
```

---

### Task 4: Thiết lập Workspace Rules `AGENTS.md` và tài liệu `README.md`

**Files:**
- Create: `AGENTS.md`
- Create: `README.md`

**Interfaces:**
- Consumes: Quy trình tự động hóa đã định nghĩa trong Spec.
- Produces: `AGENTS.md` đóng vai trò bộ não điều phối ngầm (always-on rule) và `README.md` hướng dẫn sử dụng cho người dùng.

- [ ] **Step 1: Tạo file `AGENTS.md` tại thư mục gốc**

Nội dung quy định:
- Khi người dùng gửi bất kỳ bài đăng, thread hoặc link nào từ X.com:
  - Tự động kích hoạt quy trình soạn bài Facebook Tech (`fb-tech-writer` kết hợp `humanizer`).
  - Trả về kết quả ngay lập tức theo đúng Output Contract (1 code block duy nhất + 2 gợi ý comment & visual).
  - Không mở đầu bằng lời chào rườm rà.

- [ ] **Step 2: Tạo file `README.md` tại thư mục gốc**

Nội dung hướng dẫn:
- Giới thiệu dự án và kiến trúc hệ thống.
- Hướng dẫn người dùng cách sử dụng (chỉ cần copy paste link/text từ X vào chat).
- Các ví dụ mẫu và lưu ý về định dạng Facebook.

- [ ] **Step 3: Commit**

```bash
git add AGENTS.md README.md
git commit -m "feat(config): add AGENTS.md workspace rules and user guide README.md"
```

---

### Task 5: Kiểm thử End-to-End quy trình với bài post thực tế

**Files:**
- Test input: Đoạn tweet tiếng Anh thực tế về cập nhật AI / LLM / Agentic Coding từ X.com.
- Verification target: Output bài viết tiếng Việt trên chat.

**Interfaces:**
- Consumes: Raw text của 1 tweet từ X.com.
- Produces: Bài viết Facebook hoàn chỉnh đáp ứng 100% Output Contract.

- [ ] **Step 1: Chọn một mẫu tweet thực tế làm dữ liệu kiểm thử**

Sử dụng mẫu tweet công bố kỹ thuật thực tế (ví dụ: tweet từ Anthropic hoặc OpenAI về coding agent).

- [ ] **Step 2: Chạy kiểm thử chuyển hóa nội dung qua pipeline**

Mô phỏng yêu cầu người dùng và sinh bài viết Facebook.

- [ ] **Step 3: Đánh giá chất lượng đầu ra theo checklist**

Kiểm tra:
- [ ] Bọc trong DUY NHẤT 1 code block?
- [ ] Đúng 5 phần (Hook giật với `🚨`/`🤯`/`🥶`, Fact, Builder analogy, "Mình nghĩ..." với `😰`/`🥶`, CTA mở với `👇`)?
- [ ] Không có từ ngữ AI slop tiếng Việt?
- [ ] Thuật ngữ công nghệ giữ nguyên tiếng Anh?
- [ ] Có gợi ý Comment và Visual phía dưới?

- [ ] **Step 4: Hoàn tất và nghiệm thu**
