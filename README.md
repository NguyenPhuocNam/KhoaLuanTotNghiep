🎓 Xây Dựng Công Cụ Tìm Kiếm Sản Phẩm Thời Trang Kết Hợp Ngữ Nghĩa Hình Ảnh Và Văn Bản Sử Dụng Mô Hình CLIP
Sinh viên thực hiện: Nguyễn Phước Nam - 22T1020252
Giảng viên hướng dẫn: TS. Đoàn Thị Hồng Phước
📖 1. Mô tả bài toán (Introduction)
Bối cảnh: Mua sắm thời trang trực tuyến đang bùng nổ, nhưng người dùng thường gặp khó khăn khi muốn tìm một sản phẩm kết hợp giữa hình ảnh họ có và một vài thay đổi trong đầu. Các công cụ truyền thống chỉ hỗ trợ tìm bằng text hoặc ảnh tĩnh, không xử lý được các nhu cầu tinh chỉnh linh hoạt (ví dụ: "thích kiểu áo này nhưng đổi sang màu đen và tay dài").
Mục tiêu: Xây dựng hệ thống truy xuất hình ảnh đa phương thức (Composed Image Retrieval - CIR) chuyên biệt cho ngành thương mại điện tử thời trang, hỗ trợ xử lý trực tiếp ngôn ngữ tiếng Việt.
Đầu vào (Input): Một hình ảnh tham chiếu (ảnh sản phẩm gốc) kết hợp với một đoạn văn bản mô tả yêu cầu chỉnh sửa bằng tiếng Việt.
Đầu ra (Output): Danh sách Top-K sản phẩm thời trang có sẵn trong kho dữ liệu phù hợp nhất với ý định tìm kiếm của người dùng.
🗂️ 2. Dữ liệu (Dataset)Bộ dữ liệu: FACap (A Large-scale Fashion Dataset for Fine-grained Composed Image Retrieval). 
Văn bản mô tả (captions) gốc tiếng Anh đã được dịch tự động sang tiếng Việt.
Quy mô: Tổng cộng 185.136 mẫu dữ liệu, phân bổ vào 5 danh mục thời trang cốt lõi: Váy liền, Chân váy, Áo thun/Áo kiểu, Quần, Áo khoác.
Cấu trúc đánh giá: Tập kiểm thử (Test set) gồm 5.000 mẫu dưới dạng bộ ba (triplet): Ảnh gốc + Văn bản chỉnh sửa -> Ảnh mục tiêu.
Dataset có kích thước lớn có thể được tải xuống tại [Điền Link Google Drive/Kaggle của bạn]
🧠 3. Kiến trúc Mô hình (Model Architecture)Hệ thống sử dụng kiến trúc lai (Hybrid Architecture) dựa trên cơ chế học đối chiếu (Contrastive Learning) với các thành phần cốt lõi:
Image Encoder: Sử dụng mô hình FashionCLIP, tích hợp kỹ thuật tinh chỉnh tham số hiệu quả (LoRA) để trích xuất đặc trưng thị giác.
Text Encoder: Sử dụng PhoBERT (tích hợp LoRA) để xử lý ngôn ngữ tiếng Việt.
Combiner Module: Sử dụng Attention Gated Combiner (AGC) - mạng nơ-ron đa tầng (MLP) kết hợp cơ chế cổng động để dung hợp đặc trưng ảnh và chữ, quyết định mức độ giữ lại ảnh gốc và phần trăm thay đổi theo text.
Vector Database: Sử dụng Faiss để lập chỉ mục và truy xuất vector tốc độ cao.
Cấu hình huấn luyện (Hyperparameters cho phương pháp LoRA + AGC):Rank LoRA: 32 (Clip/Text) | Alpha LoRA: 64Combiner Dim: 1024 | Tỷ lệ Dropout: 0.1 - 0.3Batch Size: 128 | Total Epochs: 20 (Warmup Combiner: 5 epochs đầu)Trọng số Loss: Triplet (0.3), L2 (0.1)
⚙️ 4. Hướng dẫn Cài đặt và Sử dụng (Installation & Usage)
Các bước cài đặt để khởi chạy hệ thống tìm kiếm đa phương thức
.Bước 1: Clone kho lưu trữBashgit clone https://github.com/NguyenPhuocNam/[Ten-Repo-Cua-Ban].git
cd [Ten-Repo-Cua-Ban]
Bước 2: Cài đặt thư việnBashpip install -r requirements.txt
Bước 3: Chạy ứng dụng web (Giao diện nguyên mẫu)Bash# Đảm bảo bạn đã tải mô hình trọng số (weights) và database Faiss vào đúng thư mục
python main.py
(Mở trình duyệt tại http://localhost:xxxx để trải nghiệm tính năng AI Search).📊 5. Kết quả đạt được (Results)Hệ thống được thực nghiệm và đối sánh qua hai phương pháp tối ưu hóa, đo lường bằng độ đo tiêu chuẩn Recall@K trên tập dữ liệu FACap (tiếng Việt):Phương phápRecall@1Recall@5Recall@10Recall@50Thời gian suy luậnChưng cất tri thức (PhoBERT Unfreeze + MLP)26.20%54.92%66.50%89.78%11.67 ms/mẫuTinh chỉnh tham số (PhoBERT-LoRA + AGC)42.36%75.28%85.70%97.72%156.80 ms/mẫuĐánh giá: Phương pháp tinh chỉnh tham số PhoBERT-LoRA + AGC mang lại độ chính xác áp đảo, thấu hiểu ngữ nghĩa tiếng Việt cực tốt. Trong thử nghiệm thực tế với giao diện Web, mô hình giữ nguyên được phom dáng cốt lõi của ảnh tham chiếu đồng thời áp đặt chính xác các thuộc tính chỉnh sửa mới (như thay đổi màu sắc, họa tiết) từ truy vấn người dùng.
