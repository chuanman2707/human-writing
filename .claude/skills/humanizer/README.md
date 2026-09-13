# Humanizer

[![skills.sh installs](https://skills.sh/b/blader/humanizer)](https://skills.sh/blader/humanizer)

Humanizer viết lại văn bản có mùi AI để đọc tự nhiên như con người viết mà không làm thay đổi thông điệp cốt lõi. Do được xây dựng hoàn toàn bằng Markdown, công cụ này tương thích với mọi AI agent hỗ trợ cơ chế skills.

---

## Cài đặt (Installation)

Cài đặt Humanizer thông qua Skills CLI:

```bash
npx skills add blader/humanizer --global
```

Bỏ cờ `--global` nếu chỉ muốn cài đặt Humanizer trong dự án hiện tại. Thêm `--agent <tên>` hoặc `--agent '*'` để chỉ định agent nhận kỹ năng, sau đó tải lại danh sách skills. Kỹ năng này kích hoạt bằng lệnh `/humanizer`.

Đối với Claude Code (phiên bản 2.1.142 trở lên), bạn có thể cài plugin:

```text
/plugin marketplace add blader/humanizer
/plugin install humanizer@humanizer
```

Plugin sẽ kích hoạt qua lệnh `/humanizer:humanizer`.

Trong Claude Desktop, tải kho lưu trữ này dưới dạng file ZIP và tải lên làm skill. Nếu cài thủ công, chỉ cần copy thư mục vào thư mục kỹ năng của agent (`.agents/skills/` hoặc `.claude/skills/`).

---

## Cách sử dụng (Usage)

Gọi trực tiếp kỹ năng:

```
/humanizer

[dán đoạn văn bản cần sửa vào đây]
```

Hoặc yêu cầu bằng ngôn ngữ tự nhiên:

```
Nhờ bạn humanize giúp mình đoạn văn này: [văn bản của bạn]
```

Để viết lại trực tiếp một file, cung cấp đường dẫn file cho Humanizer:

```
Humanize văn phong trong docs/launch-post.md
```

### Mô phỏng giọng văn của bạn (Voice Matching)

Nếu muốn bản viết lại mang đậm phong cách cá nhân của bạn, hãy cung cấp kèm một đoạn văn mẫu:

```
/humanizer

Đây là bài mẫu phong cách viết của mình:
[dán 2-3 đoạn văn do chính bạn tự viết]

Bây giờ hãy humanize đoạn văn bản này:
[dán đoạn văn bản AI cần xử lý]
```

Humanizer sẽ nương theo nhịp điệu, cách chọn từ ngữ, dấu câu và các thói quen đặc trưng có chủ đích của bạn (kể cả việc dùng dấu gạch ngang nếu bạn thường xuyên dùng).

---

## Cơ chế hoạt động (How it works)

Mô hình ngôn ngữ lớn (LLM) dự đoán từ tiếp theo dựa trên xác suất thống kê, vì vậy theo mặc định nó luôn chọn cách diễn đạt an toàn, phù hợp với phạm vi rộng nhất của độc giả và chủ đề. Một con người thật thì chỉ viết cho một độc giả cụ thể và một chủ đề cụ thể. Mọi dấu vết mà Humanizer rà soát đều xuất phát từ sự lựa chọn mặc định đó: câu văn cố ra vẻ quan trọng thay vì bổ sung dữ kiện, nhịp điệu hay định dạng áp dụng dập khuôn theo máy móc, một chi tiết bình thường bị thổi phồng thành bước ngoặt lịch sử, hoặc lời chào hỏi của chatbot còn sót lại từ khung chat.

> "LLM sử dụng các thuật toán thống kê để dự đoán từ tiếp theo. Kết quả luôn có xu hướng rơi vào phương án có xác suất thống kê cao nhất, áp dụng được cho nhiều trường hợp nhất."
> — Wikipedia, ["Signs of AI writing"](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing)

Humanizer đánh dấu mọi dấu hiệu tìm thấy, ưu tiên các lỗi nặng trước. Hệ thống soạn thảo bản viết lại mà không bị trói buộc bởi cấu trúc câu cũ, đối chiếu bản nháp với 25 mẫu nhận diện và các luận điểm gốc, rồi mới xuất bản hoàn thiện. Hệ thống tuyệt đối không bịa đặt thông tin. Tên người, số liệu, ngày tháng, trích dẫn bắt buộc phải lấy từ nguồn gốc; nếu thiếu thông tin, Humanizer sẽ hỏi lại thay vì tự đoán mò.

Khi dán văn bản trực tiếp, Humanizer sẽ hiển thị các bước: bản nháp đầu tiên, ghi chú ngắn về những chỗ còn gượng gạo, và bản hoàn thiện cuối cùng. Khi trỏ vào một file, nó chỉ biên tập phần văn xuôi, giữ nguyên code, dữ liệu, frontmatter và link.

---

## 25 Mẫu nhận diện (The 25 Patterns)

Các mẫu được đánh số theo độ nặng và tần suất xuất hiện. 5 mẫu đầu tiên (§1 đến §5) chỉ cần xuất hiện một lần là đủ lý do để viết lại. Các mẫu được đánh dấu *(đứng một mình thì yếu)* chỉ được tính là lỗi khi có nhiều dấu hiệu cùng xuất hiện trong một đoạn, vì người viết cẩn thận đôi khi vẫn cố tình dùng chúng.

### A. Dàn cảnh thay vì tuyên bố thẳng (Staging instead of stating)

| # | Mẫu nhận diện | Trước (AI Slop) | Sau (Humanized) |
|---|---|---|---|
| 1 | **Không phải X mà là Y** | "Không chỉ là X, mà còn là Y", "Điều này không có nghĩa là X. Nó có nghĩa là Y." | Đi thẳng vào vấn đề; nói rõ nhận định cụ thể |
| 2 | **Câu chốt 1 dòng & đoạn kịch tính** | "Đó mới chính là chiến thắng thực sự." sau mỗi mục; "Không tiền lệ. Không hoài niệm." | Cắt bỏ câu chốt lặp lại; gộp các mảnh câu rời rạc thành nhận định có nghĩa |
| 3 | **Những câu triết lý nghe có vẻ sâu xa** | "Về bản chất, điều thực sự quan trọng là...", "Tính đối xứng là ngôn ngữ của niềm tin" | Thay thế câu châm ngôn sáo rỗng bằng nhận định cụ thể |
| 4 | **Màn dạo đầu dàn cảnh** | "Hãy cùng tìm hiểu sâu", "Thật lòng mà nói? Nó phụ thuộc vào..." | Bỏ đoạn dạo đầu và đi thẳng vào sự thật |
| 5 | **Tranh luận với người vô hình** | "Điều này không chủ yếu nói về...", "Một cách tiếp cận đầy cám dỗ sẽ là..." | Bỏ phản biện hoặc phương án giả không ai nêu; giữ lại nhận định có thật |

### B. Nhịp điệu dập khuôn (Rhythm by rule)

| # | Mẫu nhận diện | Trước (AI Slop) | Sau (Humanized) |
|---|---|---|---|
| 6 | **Bộ ba gượng ép** | "đổi mới, cảm hứng và thấu hiểu"; 3 ví dụ liên tiếp kèm 1 bài học | Dùng đúng số lượng chi tiết mà ngữ nghĩa đòi hỏi |
| 7 | **Lặp từ mở đầu câu** | "Cô ấy thấy... Cô ấy thấy... Cô ấy ghi nhớ..." | Gộp câu hoặc thay đổi chủ ngữ |
| 8 | **Gạch ngang làm từ nối vạn năng** (*yếu*) | "tổ chức—chứ không phải người dân—nhưng điều này—" | Dùng dấu chấm, phẩy, hai chấm, ngoặc đơn; theo bài mẫu của tác giả |
| 9 | **Chồng chất từ giảm nhẹ/nghi ngờ** (*yếu*) | "có tiềm năng dường như có thể lập luận rằng" | Chỉ giữ lại từ giảm nhẹ khi tài liệu gốc đòi hỏi |
| 10 | **Cặp từ gạch nối tràn lan** (*yếu*) | "đội ngũ là cross-functional, báo cáo là high-quality" | Chỉ giữ gạch nối khi ngữ pháp tiếng Anh yêu cầu |
| 11 | **Thể bị động & khuyết chủ ngữ** (*yếu*) | "Không cần file cấu hình. Kết quả được lưu tự động." | Nêu rõ chủ thể thực hiện hành động |

### C. Thổi phồng và mượn uy tín (Inflation and borrowed authority)

| # | Mẫu nhận diện | Trước (AI Slop) | Sau (Humanized) |
|---|---|---|---|
| 12 | **Từ ngữ AI lạm dụng quá đà** | "đào sâu... minh chứng... bức tranh toàn cảnh... thể hiện" | Dùng từ ngữ đời thường, gãy gọn; tra cứu danh sách từ trong SKILL.md |
| 13 | **Thổi phồng tầm quan trọng** | "đánh dấu bước ngoặt then chốt", "Bất chấp thách thức... tiếp tục phát triển", "Tương lai tươi sáng" | Giữ lại sự thật, bỏ phần suy diễn hoa mỹ; kết thúc ở sự thật cụ thể gần nhất |
| 14 | **Mối liên hệ mơ hồ** | "gắn liền với ban lãnh đạo", "có liên quan đến" | Chỉ rõ bản chất mối quan hệ mà tài liệu cung cấp |
| 15 | **Đuôi phân từ làm màu (-ing riders)** | "tượng trưng cho... phản ánh... góp phần vào..." | Giữ lại sự thật; chỉ giữ cụm đuôi nếu có bằng chứng |
| 16 | **Ngôn ngữ quảng cáo / tiếp thị** | "nép mình giữa vùng đất ngoạn mục tuyệt mỹ" | Mô tả trực diện sự vật đó là gì |
| 17 | **Mượn danh uy tín ẩn danh** | "Các chuyên gia tin rằng...", "được nhắc đến trên NYT, BBC, FT" | Nêu đích danh người phát ngôn và nội dung, hoặc bỏ danh sách kể lể |
| 18 | **Tránh dùng động từ đơn giản "là", "có"** | "đóng vai trò như... sở hữu... mang lại..." | Dùng trực tiếp "là", "có" |

### D. Định dạng dập khuôn (Formatting by rule)

| # | Mẫu nhận diện | Trước (AI Slop) | Sau (Humanized) |
|---|---|---|---|
| 19 | **In đậm vô tội vạ để trang trí** | "**OKR**, **KPI**"; "**Hiệu năng:** Hiệu năng được cải thiện" | Bỏ in đậm thừa; chuyển danh sách gắn nhãn thành văn xuôi |
| 20 | **Tiêu đề hoa mỹ và lạm dụng icon** | "Đàm Phán Chiến Lược Và Hợp Tác", "🚀 Giai đoạn Ra mắt:" | Dùng chữ thường tự nhiên; bỏ emoji và mũi tên trang trí |
| 21 | **Dấu ngoặc kép cong** (*yếu*) | `nói “dự án đang tiến triển”` | `nói "dự án đang tiến triển"` |

### E. Tàn dư từ hội thoại và bản nháp (Leftovers from chat and draft)

| # | Mẫu nhận diện | Trước (AI Slop) | Sau (Humanized) |
|---|---|---|---|
| 22 | **Tàn dư phản hồi chatbot** | "Câu hỏi tuyệt vời! ... Hy vọng điều này giúp ích!" | Cắt bỏ lớp vỏ bọc đãi bôi; giữ lại nội dung |
| 23 | **Thanh minh giới hạn tri thức & phỏng đoán** | "Mặc dù thông tin còn hạn chế, dường như..." | Nêu rõ nguồn không đề cập, hoặc cắt bỏ câu |
| 24 | **Tiêu đề bị lặp lại trong câu đầu tiên** | "## Hiệu năng" + "Tốc độ là yếu tố sống còn." | Để tiêu đề tự làm nhiệm vụ của nó |
| 25 | **Nói về phiên bản cũ thay vì hiện tại** | "Hàm này được thêm vào để thay thế..." | Mô tả tính năng hiện tại làm được gì |

---

## Ví dụ hoàn chỉnh (Full Example)

Tác giả cung cấp ghi chú kèm bản nháp: chuyến đi diễn ra vào tháng 10 năm ngoái, khách sạn ở Alfama, quán bánh trứng nhỏ ở Graça, chuyến xe điện mất khoảng 40 phút. Không có các ghi chú thực tế này, Humanizer sẽ hỏi lại tác giả chứ không tự bịa đặt.

**Trước (Văn phong AI Slop điển hình):**
> Gần đây tôi đã trải qua năm ngày khó quên tại Lisbon, và để tôi nói cho bạn nghe — thành phố này đã hoàn toàn đánh cắp trái tim tôi. Ngay từ khoảnh khắc đặt chân đến, tôi đã biết mình đang ở một nơi thực sự đặc biệt.
>
> Nép mình dọc theo bờ sông Tagus, Lisbon đứng sừng sững như một minh chứng sống động cho tinh thần trường tồn của Bồ Đào Nha, nơi lịch sử hào hùng và nguồn năng lượng hiện đại hòa quyện ở mỗi khúc quanh. Đúng vậy, những ngọn đồi nổi tiếng là một thách thức — đôi chân tôi chắc chắn cảm nhận được điều đó! — nhưng mỗi bước leo dốc đều tưởng thưởng cho bạn tầm nhìn toàn cảnh ngoạn mục, khiến mọi nhọc nhằn đều trở nên xứng đáng.
>
> Sẽ không có chuyến đi nào trọn vẹn nếu thiếu trải nghiệm đi trên chuyến Xe điện 28 mang tính biểu tượng, uốn lượn qua những khu phố lịch sử nhất của thành phố. Và ẩm thực thì sao? Đơn giản là tuyệt hảo. Món pastéis de nata nguyên bản tại Pastéis de Belém là một báu vật quốc gia được yêu mến, và việc thưởng thức một chiếc bánh còn nóng hổi là khoảnh khắc tôi sẽ không bao giờ quên.
>
> Nhưng điều thực sự khiến Lisbon trở nên đặc biệt không chỉ là cảnh đẹp — mà là cảm giác. Rảo bước vài bước rời khỏi các quảng trường chính, bạn sẽ khám phá ra một khía cạnh yên bình, chân thực hơn: những con ngõ ngập tràn ánh nắng, mặt tiền ốp gạch quyến rũ, và những người dân địa phương thân thiện đang tiếp tục cuộc sống hàng ngày, mời gọi bạn sống chậm lại và tận hưởng từng khoảnh khắc.
>
> Tất nhiên, Lâu đài São Jorge không thể bỏ qua mang đến tầm nhìn tuyệt đẹp bao quát những mái nhà bên dưới. Hàng dài chờ đợi có thể kéo dài, nhưng trải nghiệm này hoàn toàn xứng đáng cho bất kỳ người yêu lịch sử hay du khách tò mò nào.
>
> Tôi có quay lại không? Chắc chắn rồi. Lisbon không chỉ là một nơi để ghé thăm — đó là nơi để bạn phải lòng, hết lần này đến lần khác. Nếu bạn đang mơ về kỳ nghỉ tiếp theo, đây là một điểm đến hứa hẹn những kỷ niệm kéo dài suốt cả cuộc đời. ✨

**Sau (Đã qua xử lý Humanizer):**
> Tôi có năm ngày ở Lisbon vào tháng 10 năm ngoái và tới giờ cảm xúc vẫn còn lẫn lộn. Đẹp thì có đẹp. Nhưng cũng đau đầu gối hơn nhiều so với bất kỳ ai từng cảnh báo.
>
> Mấy con dốc mới là toàn bộ câu chuyện và chẳng hiểu sao chưa bao giờ xuất hiện trên tờ rơi quảng cáo. Khách sạn tôi ở tít trên dốc Alfama, lên ảnh thì lung linh nhưng ngoài đời thực đồng nghĩa với việc leo bộ tương đương sáu tầng lầu mỗi lần muốn đi uống cà phê. Sang đến ngày thứ hai thì bắp chân tôi bắt đầu "lên tiếng". Tôi bắt đầu lên kế hoạch cho mỗi chuyến đi bộ dựa vào việc hướng nào là xuống dốc — một cách kỳ quặc để ngắm nhìn thành phố nhưng ít nhất là thành thật với bản thân.
>
> Ai cũng bảo phải đi thử Xe điện 28, nên tôi cũng đi, bị ép dính chặt vào balo của một người lạ suốt bốn mươi phút giữa lúc ba đoàn khách du lịch đang cùng quay phim ở một góc phố. Lần sau chắc tôi sẽ đi bộ tuyến này, hoặc đi trước giờ ăn sáng. Tuy nhiên, món bánh tart trứng thì đáng để chen chúc thật. Tôi ăn một cái ở quán nhỏ bình dân vùng Graça, bánh còn ấm, và trong khoảng ba mươi giây tôi đã hiểu vì sao người ta có thể lên kế hoạch cho cả chuyến đi chỉ vì một món bánh ngọt.
>
> Điều tôi không ngờ tới là thành phố lại yên tĩnh đến thế khi bước ra khỏi những quảng trường chính. Chỉ cần đi ngược dốc hai dãy nhà là bắt gặp những dây phơi quần áo, những mảng tường gạch tróc lở, những ô cửa sổ mở toang và mấy cụ già đang mở to loa xem bóng đá. Đó mới là Lisbon mà tôi cứ nhớ mãi, chứ không phải tòa lâu đài.
>
> Lâu đài thì cũng được thôi. View đẹp, hàng chờ dài, và tôi mất nhiều thời gian lết từng bước về phía cổng vào hơn là thực sự ngắm nghía thứ gì khi đã vào trong. Nếu chỉ có hai ngày, tôi thà đổi nó lấy một buổi chiều đi lạc còn hơn.
>
> Tôi sẽ quay lại, nhưng vào mùa xuân và với một đôi giày tốt hơn. Lisbon chẳng hơi đâu uốn mình để làm mọi thứ trở nên dễ dàng cho bạn. Và tôi nghĩ mình thích điều đó, ngay cả khi đôi chân tôi chẳng đồng tình chút nào.

---

## Nguồn tài liệu tham khảo

- [Wikipedia: Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing) là nguồn cơ sở cho danh sách các mẫu nhận diện.
- [WikiProject AI Cleanup](https://en.wikipedia.org/wiki/Wikipedia:WikiProject_AI_Cleanup) duy trì và cập nhật các nguyên tắc này.

## Giấy phép (License)

MIT
