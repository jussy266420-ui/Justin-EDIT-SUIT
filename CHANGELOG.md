# Justin EDIT Suite — thay đổi

## 2.4.2 — Giao diện gọn hơn, âm thanh tùy chọn
**Phân tích âm thanh thành tùy chọn**
- Lần đầu bấm **Phân tích** ở một folder, tool hỏi có phân tích âm thanh không (gợi ý theo thể loại: Cut on beat → chỉ hình; còn lại → có). Chọn "chỉ hình" thì bỏ nhận dạng lời nói (Whisper), chỉ mục âm thanh và nhận diện người nói — nhanh hơn, không tranh GPU với phần hình. Lựa chọn nhớ theo folder.
- Nút **🎙 Âm thanh** (thẻ Pipeline): chạy riêng phần âm thanh bất cứ lúc nào, không phân tích lại hình. Align theo kịch bản trên folder "chỉ hình" sẽ hỏi chạy phần âm thanh trước.
- QC âm thanh (âm lượng, vỡ tiếng) vẫn chạy cùng lần giải mã hình — đo được gần như không tốn thêm thời gian.

**Giao diện**
- Bỏ tab **Phím tắt** (agent AI vẫn chạy được lệnh Premiere và tra kiến thức phím tắt khi được hỏi).
- **Report** chuyển vào tab Review (mục thu gọn ở cuối) — 11 tab → 9 tab, sắp theo quy trình: Chạy · Kịch bản · Review · AI · Âm thanh · Chữ · Giọng · Reverse · Cài đặt.
- Cài đặt: AI lên đầu; model theo từng việc, đường dẫn engine / Python / output thu gọn (tự mở khi chưa có đường dẫn).
- Sequence: đặt hình · chuyển cảnh · tiếng B-roll (mặc định theo thể loại) thu gọn; nút "🤖" thành "🤖 Mô tả"; tab AI bớt các nút gợi ý trùng với Rà lỗi / Kịch bản.

## 2.4.1 — Tối ưu
| Việc | Trước | Sau |
|---|---|---|
| Dựng montage cut on beat (340 clip) | 3,7 s | 2,3 s |
| Dựng lại kiểu xáo (shuffle) | 5,1 s | 3,3 s |
| Lập chỉ mục âm thanh (340 clip) | 28 s | 15 s |
- Montage cho ra bản dựng **giống hệt từng shot** như trước (kiểm tra trên 2 thư mục + chế độ xáo + rà lỗi) — chỉ nhanh hơn.
- Mọi chỗ gọi Ollama (mô tả clip, tóm thoại, lọc thoại, xem kỹ) dùng chung một client: khi Ollama chạy chậm bất thường do VRAM đầy, mô hình được nạp lại tự động — trước đây chỉ có ở "Xem kỹ", nay cả mô tả clip tự động (gọi nhiều nhất).
- Đã đo: phân tích ổn định + QC đang chạy sát giới hạn bộ giải mã phần cứng NVDEC (~260 khung 4K/giây với DJI 10-bit) — phần xử lý hình (2 ms/khung) đã nằm hoàn toàn sau phần giải mã, nên không còn chỗ tăng tốc bằng phần mềm.

## 2.4.0 — Âm thanh, người nói, retention thật của kênh
> Bấm **Cài đặt → Tải trước model** một lần (thêm PANNs ~360 MB và WeSpeaker 26 MB). Giấy phép và ghi công các mô hình: `GIAY_PHEP_MO_HINH.md`.

**Tìm footage theo âm thanh** (Tìm footage → nút "Theo âm thanh")
- Gõ "tiếng cười", "sóng biển", "vỗ tay", "xe máy", "gió", "nhạc"… → các đoạn có tiếng đó, kèm nhãn âm thanh tìm thấy. 527 loại âm thanh (AudioSet), câu tiếng Việt được hiểu qua từ điển + SigLIP2.
- Lập chỉ mục âm thanh tự chạy cùng "Lập chỉ mục" / phân tích nền: ~1 phút cho 300 clip.

**Ai đang nói lúc nào** (Transcript → "Nhận diện người nói")
- Gắn S1, S2… cho từng câu; cùng một người có cùng nhãn ở mọi clip của thư mục. Tự đếm số người nói (thử với giọng biết trước: đúng 1, 2 và 3 người, 100%).
- Lệnh sửa bằng lời thấy ai đang nói ("bỏ các đoạn khách hỏi xen vào").

**Retention thật từ kênh** (tab AI → YouTube)
- Kết nối kênh một lần (đăng nhập Google trong trình duyệt của anh, chỉ quyền ĐỌC thống kê). Tool tự tìm video trên kênh khớp với bản dựng, vẽ đường giữ chân người xem và chỉ ra **chỗ người xem rời đi bất thường** và **chỗ được xem lại** — kèm shot, lời thoại ở đó.
- Từ 2 video trở lên: bài học của kênh (vd. "b-roll dài: người xem rời đi nhiều gấp 1,8×"), và Rà lỗi tự cảnh báo + đề xuất sửa những đoạn giống vậy trong bản dựng mới.
- Cần tạo OAuth client loại Desktop app trong Google Cloud Console (hướng dẫn ngay trong tab).

**Sửa lỗi**
- macOS: hồ sơ "Tool đã học" và thư viện SFX của panel và engine nằm ở hai thư mục khác nhau → nay dùng chung.

## 2.3.0 — Thuật toán dựng thông minh hơn, chuẩn bị đăng YouTube
Mỗi thay đổi đều được đo trên bộ footage thử 340 clip và 40 clip, chi tiết trong `docs/LO_TRINH_THUAT_TOAN.md`.

> **Cần phân tích lại footage** (bấm Phân tích như thường lệ) để có dữ liệu hướng lia máy mới — chưa phân tích lại thì montage vẫn chạy, chỉ thiếu luật lia máy.

**Montage cut on beat**
- **Không lia ngược chiều giữa hai shot liền nhau** (giật mắt): 18 → 0 cặp trên 340 clip.
- **Chuyển động trúng nhịp:** chủ thể bắt đầu chuyển động đúng beat (visual beat) — 32 → 49 / 117 shot.
- **Phủ đủ các cảnh của chuyến quay:** trước đây montage dồn shot vào vài cảnh đẹp nhất và bỏ sót cảnh khác; nay cảnh có mặt 22 → 23/24, cắt giữa hành động 10 → 7. Footage ít thì luật tự tắt (không làm lặp cảnh).
- Tối ưu lại toàn bộ bản dựng sau lượt chọn đầu (thấy cả hai phía của mỗi shot).

**Tool học gu của anh**
- Mỗi lần Áp dụng trong khung xem trước, thay đổi anh giữ / bỏ tick được ghi lại; đủ dữ liệu (≥ 2 lần bỏ tick) thì montage chọn shot theo gu đó (ổn định, chuyển động, slow-mo…). Xem ở Cài đặt → Tool đã học. Gu không bao giờ kéo về shot rung / cắt giữa hành động.

**Tìm footage — "Xem kỹ bằng AI"**
- Bật nút này: sau khi SigLIP2 tìm, Qwen3-VL (Ollama, chạy trên máy) nhìn từng ảnh 12 kết quả đầu, chấm điểm, sắp xếp lại và làm mờ cảnh không liên quan. Đo trên 10 câu tìm: xếp hạng đúng hơn 0,877 → 0,915, loại 36/46 cảnh không liên quan. Gõ tìm câu mới là dừng ngay.
- Lệnh "thay shot 5 bằng cảnh …" cũng được AI nhìn xác minh khi nút này bật.
- Tự sửa khi Ollama chạy chậm bất thường do VRAM đầy (nạp lại mô hình).

**Transcript**
- Bắt cả tiếng "ừ / ờ / à" mà Whisper bỏ qua không ghi thành chữ (thử: 4/4, không báo nhầm).

**Tab AI → YouTube (mới)**
- **Chia chương tự động** từ lời thoại của bản dựng: đúng luật YouTube (0:00, ≥ 3 chương, ≥ 10 giây), sửa tên ngay trong bảng, chép cho mô tả video hoặc gắn chapter marker vào sequence.
- **Gợi ý thumbnail:** chấm 1 khung mỗi giây (nét, mặt người, đẹp, màu) → 8 khung khác nhau, đúng khung hình sequence (1280×720 hoặc 720×1280), cắt giữ người ở clip quay dọc.

## 2.2.2 — Worker AI dùng chung, hậu kỳ theo lô
- **Một tiến trình AI duy nhất** cho tìm footage, take giống nhau, rà lỗi, ra lệnh sửa, transcript, hậu kỳ (trước: một server tìm kiếm + mỗi lệnh sửa khởi động một Python mới). Mô hình SigLIP2 nạp một lần (chạy ngầm ngay khi mở panel); dữ liệu footage giữ trong bộ nhớ đến khi file phân tích thay đổi. Lệnh lặp lại gần như tức thì (context lần 2: < 10 ms).
- Giao tiếp JSON theo dòng có id thay cho file tạm + đọc log; worker chết thì tự khởi động lại, hỏng liên tục thì panel tự dùng đường cũ.
- Gõ tìm kiếm liên tục: yêu cầu đang chờ được thay bằng yêu cầu mới nhất.
- **Hậu kỳ gửi Premiere theo lô 8 clip** (trước: mỗi clip một lần gọi ExtendScript), tìm effect Lumetri / Warp Stabilizer một lần mỗi lô; một clip lỗi không làm dừng cả lô.

## 2.2.1 — Tối ưu tốc độ
| Việc | Trước | Sau |
|---|---|---|
| Khởi động một lệnh AI trong panel (rà lỗi / ra lệnh / transcript) | 17–45 s | 0,15 s |
| Rà lỗi bản dựng | 10 s | 0,2 s |
| Dựng montage cut on beat cả bài | ~40 s | ~1 s |
| Tìm cảnh theo mô tả khi ra lệnh sửa | ~9,5 s | 0,4 s |
| Thumbnail một khung (rà lỗi, tìm footage) | 0,84 s | 0,05 s |
- Vector chữ (câu mô tả cố định, câu tìm kiếm) được cache ra đĩa: các lệnh AI không phải nạp thư viện + mô hình mỗi lần.
- Phạt lặp cảnh trong montage tính một lượt bằng ma trận cho mọi ứng viên (trước: ~1 triệu lần gọi hàm Python); kết quả giống hệt (sai khác < 1e-5).
- Lấy khung: PyNvVideoCodec (NVDEC) trả khung về RAM, cắt bằng CPU — đo trên 24 clip mọi độ dài: 16 s, so với 52 s (giải mã keyframe một lượt) và 214 s (ffmpeg từng khung). Máy không có NVIDIA dùng keyframe một lượt.

## 2.2.0 — Chỉnh sửa bằng AI (tab AI)
Tab **AI** có thanh chọn: Trò chuyện (agent cũ) · Rà lỗi · Ra lệnh sửa · Transcript · Hậu kỳ. Mọi chỉnh sửa đều **xem trước rồi mới áp dụng**. Áp dụng tạo plan bản mới (_vN+1) và build **sequence mới**; bản cũ giữ nguyên.
- **Rà lỗi:** tìm cảnh lặp / take giống nhau đứng gần nhau, cắt giữa hành động, đoạn rung/mờ/đen, người nói trong montage nhạc, jump cut, ô thiếu hình. Mỗi lỗi kèm cách sửa; cảnh thay được chọn theo đúng luật dựng và không mang lại lỗi cũ. Thử nghiệm: bản cut on beat có 7 lỗi → bản sửa còn 0.
- **Ra lệnh sửa:** gõ tiếng Việt ("thay shot 5 bằng cảnh hoàng hôn", "cắt nhanh hơn", "đổi chỗ shot 3 và 8"). Model hiểu nội dung shot nhờ nhãn SigLIP2 tự động; cảnh theo mô tả được tìm bằng SigLIP2. Ollama local (qwen3-vl 8B) trả lời trong 4–14 giây.
- **Transcript:** sửa video như sửa văn bản — bấm chữ để cắt; tự tìm từ đệm (ừ, ờ, ừm…), lặp từ, khoảng lặng dài. Cắt đúng ranh giới từ nhờ căn chỉnh wav2vec2.
- **Hậu kỳ** (trên sequence đang mở, có sao lưu): reframe dọc 9:16 bám chủ thể (keyframe Position), cân màu đồng bộ bằng Lumetri (chỉ sửa lệch vừa phải, giữ chủ ý cảnh hoàng hôn / đêm), chống rung bằng Warp Stabilizer.

## 2.1.0
**Tốc độ**
- QC + phân tích ổn định chạy GPU thật: footage HEVC 10-bit (DJI, GoPro) trước đây âm thầm rơi về CPU. Giải mã nhanh ~12× (60 s → 5 s mỗi clip 4K).
- QC và phân tích ổn định dùng chung **một lần giải mã** cho mỗi clip.
- GPU và CPU chạy song song: GPU nhận clip nặng, CPU nhận clip nhẹ. Số luồng tự chọn theo máy (NVIDIA / Mac / Windows AMD-Intel qua D3D11VA / chỉ CPU).
- Có cảnh báo khi GPU lỗi phải chạy CPU, không còn chậm âm thầm.
- Proxy: giải mã, thu nhỏ và encode đều trên GPU (NVENC / VideoToolbox).
- Contact sheet và kiểm tra file lỗi: mỗi clip chỉ gọi ffmpeg 1 lần.

**Cut on beat**
- Bắt beat bằng AI (beat_this): hết lỗi bắt nhầm nửa tốc độ (66 → 130 BPM trên nhạc thử).
- Hết lặp source: nhận ra các take quay lại cùng một cảnh (SigLIP2) và giãn chúng ra trên toàn timeline. Trên 340 clip: 16 → 0 cặp cảnh giống nhau đứng gần nhau.
- Không cắt giữa hành động: đo chuyển động của chủ thể. Shot cắt giữa hành động 23 → 9; shot dở hành động được giữ tới beat sau khi cùng một take.

**AI Assist**
- Tìm footage bằng câu chữ (tiếng Việt / Anh), bấm kết quả để mở trong Source Monitor với in/out đặt sẵn.
- Liệt kê take giống nhau.
- Chấm điểm đẹp/nét để xếp hạng shot.
- Căn thời gian từng từ thoại: lệch đầu câu 286 ms → 11 ms.
- Tách cảnh video đã dựng (TransNetV2).
- Whisper tiếng Việt EraX (`erax-turbo`).

**Cài đặt**
- Thiếu gì tự cài nấy: Python, thư viện, FFmpeg, CUDA, gói AI, model, Ollama.
- Panel tự kiểm tra mỗi lần mở; nút **Tự sửa**, **Cài gói AI**.
- Giao diện mới (theme 2.1).
