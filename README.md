# Train Colab Classification

Notebook Google Colab dùng để train mô hình phân loại ảnh từ một URL dataset public và gửi kết quả train về backend Label Forge.

## Tổng quan

Repo này chứa workflow train trong file `train_classification_notebook.ipynb`.

Notebook được dùng để:

- Nhận tham số train từ Label Forge.
- Kiểm tra `DATASET_URL` và `CALLBACK_URL` có phải URL public không.
- Cài đặt các thư viện cần thiết.
- Tải và giải nén dataset.
- Train mô hình YOLO bằng Ultralytics.
- Đánh giá mô hình sau khi train.
- Tạo một số ảnh dự đoán mẫu.
- Upload file `best.pt` về backend.
- Gửi metrics và trạng thái train về `CALLBACK_URL`.

## File chính

| File | Chức năng |
| --- | --- |
| `train_classification_notebook.ipynb` | Notebook chính để tải dataset, train, đánh giá, upload model và gửi callback. |

## Yêu cầu

Notebook sẽ tự cài các thư viện Python cần thiết trong Colab:

```bash
ultralytics torch torchvision requests gdown pyyaml
```

Runtime khuyến nghị:

- Google Colab
- Bật GPU runtime
- Dataset URL public
- Backend callback URL public

## Tham số train

Cell đầu tiên của notebook có các tham số chính:

| Tham số | Mô tả |
| --- | --- |
| `JOB_ID` | Mã job train từ backend. |
| `DATASET_URL` | URL public trỏ tới file dataset dạng zip. |
| `CALLBACK_URL` | Endpoint public để nhận kết quả train. |
| `ARCHITECTURE` | Kích thước model YOLO, ví dụ `yolov8n`, `yolov8s`, `yolov8m`, `yolov8l`, hoặc `yolov8x`. |
| `EPOCHS` | Số epoch train. |
| `IMAGE_SIZE` | Kích thước ảnh đầu vào. |
| `BATCH_SIZE` | Batch size khi train. |
| `LEARNING_RATE` | Learning rate ban đầu. |
| `PATIENCE` | Số epoch chờ trước khi early stopping. |
| `DEVICE` | Thiết bị train, thường là `0` nếu dùng GPU Colab. |

Notebook cũng có thêm các tham số optimizer và augmentation như `OPTIMIZER`, `MOSAIC`, `MIXUP`, `FLIPLR`, `HSV_H`, `HSV_S` và một số tham số khác.

## Cấu trúc dataset

Với bài toán classification, sau khi giải nén dataset thường cần có cấu trúc:

```text
dataset/
  train/
    class_a/
      image1.jpg
    class_b/
      image2.jpg
  val/
    class_a/
    class_b/
  test/
    class_a/
    class_b/
```

Nếu dataset dùng thư mục `valid/`, notebook sẽ đổi tên thành `val/` vì Ultralytics classification yêu cầu thư mục validation tên là `val`.

Với dataset detection, notebook sẽ kiểm tra file `data.yaml`.

## Cách chạy

1. Mở `train_classification_notebook.ipynb` bằng Google Colab.
2. Bật GPU runtime.
3. Điền `JOB_ID`, `DATASET_URL` và `CALLBACK_URL`.
4. Điều chỉnh model và tham số train nếu cần.
5. Chạy toàn bộ các cell từ trên xuống dưới.
6. Chờ quá trình train hoàn tất và callback được gửi về backend.

## Yêu cầu URL public

`DATASET_URL` và `CALLBACK_URL` không được là địa chỉ localhost như:

```text
localhost
127.0.0.1
0.0.0.0
```

Nếu backend đang chạy local, hãy expose backend bằng tunnel public, ví dụ Cloudflare Tunnel, rồi dùng URL HTTPS được tạo ra.

## Ghi chú lỗi push Git

Nếu đã xóa commit local bằng `git reset --hard` nhưng GitHub vẫn còn commit đó, khi push có thể gặp lỗi:

```text
rejected non-fast-forward
```

Nếu muốn giữ lịch sử trên GitHub và lấy thay đổi remote về local:

```bash
git pull --rebase origin main
git push origin main
```

Nếu muốn GitHub khớp với branch local sau khi reset:

```bash
git push --force-with-lease origin main
```

Chỉ dùng `--force-with-lease` khi chắc chắn muốn xóa commit mới hơn trên remote.
