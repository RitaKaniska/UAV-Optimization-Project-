# UAV Optimization Project — README

> Tối ưu hoá lộ trình & phân bổ UAV nhằm **tối đa hoá mức độ hài lòng của người dùng** và **tối thiểu chi phí bay**, với mô hình **Graph Convolutional Network (GCN)** thích nghi với thay đổi môi trường.

> **Repo**: `RitaKaniska/UAV-Optimization-Project-`

---

## 1) Tổng quan

Dự án đặt bài toán tối ưu cho một tập UAV (Unmanned Aerial Vehicles) phục vụ người dùng/điểm nhu cầu trong không gian. Mục tiêu là tìm chiến lược di chuyển/phân bổ (routing & assignment) giúp **tối đa hoá hài lòng người dùng** (ví dụ: tỷ lệ được phục vụ, độ trễ thấp) trong khi **giảm chi phí bay** (thời gian/ quãng đường/ năng lượng).

Phương pháp chính là **Graph Convolutional Neural Network (GCN)** nhằm khai thác cấu trúc đồ thị giữa UAV–người dùng–môi trường, cho phép mô hình hóa động thái thay đổi nhanh (ví dụ: nhu cầu biến thiên, cản trở môi trường).

> **Ghi chú**: Trong repo hiện tại, phần hiện thực nằm chủ yếu trong **một notebook** `GraphNeuralIdeaFull.ipynb` kèm **hai báo cáo PDF** mô tả ý tưởng/thử nghiệm. Không có `requirements.txt` hay module Python tách riêng thành package. README này chuẩn hoá lại cấu trúc, hướng dẫn chạy, và đề xuất phụ thuộc để bạn dễ tái lập.

---

## 2) Kiến trúc & ý tưởng thuật toán

* **Biểu diễn đồ thị**: các đỉnh có thể gồm *UAV*, *người dùng/điểm dịch vụ*, và *mốc môi trường*; cạnh thể hiện khả năng kết nối, chi phí di chuyển, hoặc tầm phủ sóng.
* **GCN**: trích xuất đặc trưng theo ngữ cảnh đồ thị (neighborhood aggregation) để ước lượng:

  * điểm ưu tiên phục vụ/ghép nối UAV–người dùng (assignment score), hoặc
  * chi phí/lợi ích biên (edge utility) hỗ trợ quyết định lộ trình.
* **Mục tiêu tối ưu**: kết hợp **hài lòng** (service rate, QoS) và **chi phí bay** (thời gian/quãng đường/năng lượng) thành một hàm mục tiêu có thể:
  $\max\; \text{satisfaction} - \lambda\,\text{flying\_cost}$
  với $\lambda$ là siêu tham số cân bằng.
* **Chu trình suy luận** (gợi ý điển hình trong notebook):

  1. Sinh/đọc dữ liệu đồ thị & đặc trưng (vị trí, nhu cầu, trạng thái UAV…).
  2. Lan truyền GCN để lấy embedding/điểm số.
  3. Suy ra quyết định ghép nối hoặc cạnh/lộ trình ưu tiên.
  4. Tính hàm mục tiêu và số liệu đánh giá.

---

## 3) Cấu trúc repo

```
UAV-Optimization-Project-
├── GraphNeuralIdeaFull.ipynb      # Notebook ý tưởng/chứng minh khả thi (PoC)
├── Report_For_UAV_Problem.pdf     # Báo cáo mô tả bài toán & kết quả tóm tắt
└── Report_for_UAV_problem__heuristic_-3.pdf   # Báo cáo hướng heuristic
```

---

## 4) Thiết lập môi trường (đề xuất)

Do repo chưa có file phụ thuộc chính thức, dưới đây là **gợi ý** các thư viện thường dùng cho bài toán GCN trên đồ thị:

* **Python**: 3.9–3.11
* **Core**: `numpy`, `scipy`, `pandas`, `matplotlib`
* **Đồ thị/GCN** (chọn *một* trong hai hướng):

  * *PyTorch + PyTorch Geometric*: `torch`, `torch_geometric`, `torch_scatter`, `torch_sparse`, `networkx`
  * *DGL (tuỳ chọn)*: `dgl`, `torch`

> *Lưu ý*: Tuỳ phiên bản CUDA/CPU, cách cài `torch`/`torch_geometric` sẽ khác. Xem trang cài đặt chính thức của PyTorch/PyG để chọn đúng lệnh `pip`/`conda` theo hệ điều hành & CUDA.

Ví dụ (CPU, chỉ minh hoạ):

```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install numpy scipy pandas matplotlib networkx torch --index-url https://download.pytorch.org/whl/cpu
pip install torch_geometric torch_scatter torch_sparse  # theo hướng dẫn của PyG
```

---

## 5) Cách chạy notebook

1. Mở notebook:

   ```bash
   jupyter lab  # hoặc jupyter notebook
   ```
2. Mở file `GraphNeuralIdeaFull.ipynb` và chạy lần lượt các cell.
3. (Nếu notebook tạo dữ liệu giả lập) điều chỉnh **tham số mô phỏng**: số UAV, số người dùng, bán kính phủ sóng, chi phí di chuyển, hệ số cân bằng $\lambda$, số epoch huấn luyện…
4. Theo dõi:

   * **Loss/Objective** theo epoch.
   * **Chỉ số chất lượng**: *satisfaction*, *flying cost*, và mục tiêu tổng hợp.

---

## 6) Dữ liệu đầu vào

* **Dạng tổng quát**: tọa độ UAV & người dùng, nhu cầu/ưu tiên, ràng buộc vùng cấm/điều kiện môi trường, trạng thái pin/tốc độ UAV…
* **Trong repo**: notebook thường **tự sinh** dữ liệu mô phỏng để kiểm chứng ý tưởng. Nếu bạn muốn dùng dữ liệu thực, hãy chuẩn hoá về cùng định dạng đồ thị (node/edge list + thuộc tính) trước khi nạp vào notebook.

---

## 7) Đánh giá & số liệu (metrics)

* **User Satisfaction**: tỷ lệ phục vụ, độ trễ trung bình, QoS/utility per user…
* **Flying Cost**: tổng quãng đường, thời gian, hoặc ước lượng năng lượng tiêu hao.
* **Mục tiêu tổng hợp**: $J = \text{satisfaction} - \lambda\,\text{cost}$.
* **Thực nghiệm**: xem các bảng/biểu đồ mô tả trong hai báo cáo PDF đi kèm để tham khảo kết quả và cấu hình thí nghiệm.

---


**H: Tại sao kết quả khác trong máy tôi?**
Đ: Do seed ngẫu nhiên, phiên bản thư viện, hay phần cứng khác nhau. Hãy cố định seed, ghi rõ phiên bản phụ thuộc, và kiểm tra lại tham số mô phỏng.
