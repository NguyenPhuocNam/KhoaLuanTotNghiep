# 🎓 Xây Dựng Công Cụ Tìm Kiếm Sản Phẩm Thời Trang Kết Hợp Ngữ Nghĩa Hình Ảnh Và Văn Bản

**Sinh viên thực hiện:** Nguyễn Phước Nam - 22T1020252.
**Giảng viên hướng dẫn:** TS. Đoàn Thị Hồng Phước

## 📖 1. Mô tả bài toán (Introduction)

* **Bối cảnh:** Mua sắm thời trang trực tuyến đang bùng nổ, nhưng người dùng thường gặp khó khăn khi muốn tìm một sản phẩm kết hợp giữa hình ảnh tham chiếu và các ý định tinh chỉnh trong đầu. Các công cụ truyền thống chỉ hỗ trợ tìm bằng văn bản hoặc ảnh tĩnh, không xử lý được các nhu cầu linh hoạt (ví dụ: "thích kiểu áo này nhưng đổi sang màu đen và tay dài").
* **Mục tiêu:** Xây dựng hệ thống truy xuất hình ảnh đa phương thức (Composed Image Retrieval - CIR) chuyên biệt cho ngành thương mại điện tử thời trang. Trọng tâm của đề tài là áp dụng phương pháp **Chưng cất tri thức (Knowledge Distillation)** để giúp hệ thống thấu hiểu trực tiếp ngôn ngữ tiếng Việt mà không cần tài nguyên huấn luyện khổng lồ.
* **Đầu vào (Input):** Hình ảnh sản phẩm tham chiếu + Văn bản mô tả yêu cầu chỉnh sửa bằng tiếng Việt.
* **Đầu ra (Output):** Danh sách Top-K sản phẩm phù hợp nhất với ý định tìm kiếm.

## 🗂️ 2. Dữ liệu (Dataset)

* **Bộ dữ liệu:** FACap (A Large-scale Fashion Dataset for Fine-grained Composed Image Retrieval). Văn bản mô tả gốc tiếng Anh đã được chuyển ngữ sang tiếng Việt.
* **Quy mô:** 185.136 mẫu dữ liệu thuộc 5 danh mục cốt lõi (Váy liền, Chân váy, Áo thun/Áo kiểu, Quần, Áo khoác).
* **Cấu trúc đánh giá:** Tập kiểm thử (Test set) gồm 5.000 mẫu dưới dạng bộ ba: `Ảnh gốc + Văn bản chỉnh sửa -> Ảnh mục tiêu`.

> **Lưu ý:** Dataset có kích thước lớn có thể được tải xuống tại [Điền Link Google Drive/Kaggle của bạn]

## 🧠 3. Kiến trúc Mô hình (Model Architecture)

Hệ thống sử dụng kiến trúc lai kết hợp cơ chế **Teacher-Student** để đồng bộ hóa không gian vector ngữ nghĩa Anh - Việt:

* **Mô hình Giáo viên (Teacher):** Image Encoder và Text Encoder của FashionCLIP (đóng băng hoàn toàn trọng số), đóng vai trò chuẩn mực ngữ nghĩa.
* **Mô hình Học sinh (Student - Đề xuất):** Mô hình ngôn ngữ PhoBERT (Unfreeze) kết hợp lớp chiếu MLP Projector.
* **Hàm mất mát chưng cất (Objective Function):** Tối ưu hóa đồng thời độ lớn và hướng của vector thông qua sự kết hợp giữa MSE Loss và Cosine Similarity Loss.
* **Combiner Module:** Sử dụng mạng phi tuyến MLP Combiner để dung hợp vector đặc trưng ảnh và chữ thành một vector truy vấn duy nhất.

### ⚙️ Cấu hình huấn luyện (Hyperparameters)
* **Kích thước lô (Batch Size):** 128
* **Số chu kỳ (Epochs):** 3
* **Tốc độ học (Learning Rate):** 2e-5
* **Hệ số điều chuẩn (Weight Decay):** 0.01
* **Ngưỡng cắt dốc (Gradient Clipping):** 1.0
