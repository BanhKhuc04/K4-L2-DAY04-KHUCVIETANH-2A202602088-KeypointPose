# Review partner / Self-review

Người gán được kiểm: **Khúc Việt Anh**  
Người kiểm: **Khúc Việt Anh (tự kiểm bài cá nhân)**  
Ngày: **16/09/2026**

> Bài được thực hiện cá nhân, vì vậy phần này ghi lại quá trình **self-review** dựa trên các lần chạy checker, visibility report và chấm với gold trong Terminal.

## 1. Kết quả kiểm định dạng ban đầu

Sau khi convert COCO Keypoints sang YOLO Pose:

- 20/20 file nhãn được đọc.
- Ban đầu có 28 skeleton.
- Visibility lúc đầu: `v=2: 347 | v=1: 83 | v=0: 46`.

Checker phát hiện 4 cảnh báo về việc dùng `v=0` cho người vẫn nằm gọn trong ảnh:

- `train_06`: 7 khớp `v=0`.
- `train_09`: 4 khớp `v=0`.
- `train_11`: 5 khớp `v=0`.
- `train_15`: 5 khớp `v=0`.

Ngoài ra có 6 lỗi bắt buộc sửa do khớp có `v=2` nhưng tọa độ nằm ngoài ảnh:

| Ảnh | Người | Khớp | Lỗi | Cách sửa |
| --- | ---: | --- | --- | --- |
| train_04 | 1 | right_hip | `v=2` nhưng tọa độ ra ngoài ảnh | Kiểm tra lại, nếu ra ngoài khung thì đặt Outside (`v=0`) |
| train_04 | 2 | right_wrist | `v=2` nhưng tọa độ ra ngoài ảnh | Sửa lại visibility / tọa độ |
| train_04 | 2 | right_knee | `v=2` nhưng tọa độ ra ngoài ảnh | Sửa lại visibility / tọa độ |
| train_07 | 1 | left_ankle | `v=2` nhưng tọa độ ra ngoài ảnh | Sửa lại visibility / tọa độ |
| train_13 | 1 | right_ankle | `v=2` nhưng tọa độ ra ngoài ảnh | Sửa lại visibility / tọa độ |
| train_13 | 2 | right_knee | `v=2` nhưng tọa độ ra ngoài ảnh | Sửa lại visibility / tọa độ |

Sau khi sửa, checker báo **ĐẠT định dạng**.

## 2. Kết quả visibility sau khi sửa

Sau các lần sửa và export lại:

- 20 ảnh.
- 29 skeleton.
- Visibility cuối: `v=2: 356 | v=1: 109 | v=0: 28`.
- `check_pose_labels.py`: **ĐẠT định dạng**.

Các khớp có nhiều `v=0` nhất chủ yếu là mắt cá chân, phù hợp với các trường hợp chân ra ngoài mép ảnh hoặc bị cắt khung. Các trường hợp bị che nhưng vẫn nằm trong ảnh đã được ưu tiên chuyển sang `v=1` (Occluded).

## 3. Kết quả chấm với Gold trước rework

Lần chấm đầu:

- **OKS trung bình: 0.913**
- **OKS@0.50: 1.000**
- **OKS@0.75: 1.000**
- Gold: 29 người
- Ghép được: 29 người
- Thiếu: 0
- Thừa: 0
- Mức: **Xuất sắc**

Danh sách lỗi:

- 1 lỗi **Nhầm người**.
- 3 lỗi **Xoá khớp bị che**.
- 10 lỗi **Lệch nhẹ**.
- 60 mục **Cờ khác gold** — không trừ OKS.
- 74 mục **Gold để v=0 ở khớp đã gán** — không trừ OKS.

Ba skeleton được ưu tiên sửa:

| Ảnh | Người | Lỗi |
| --- | ---: | --- |
| train_13 | 2 | `left_knee` bị để Outside trong khi gold là khớp bị che |
| train_12 | 1 | `left_knee`, `left_ankle` bị để Outside trong khi gold là khớp bị che |
| train_04 | 1 | `left_wrist` bị chấm sang cơ thể người khác |

## 4. Kết quả sau rework

Sau khi sửa và export lại:

- **OKS trung bình: 0.920**
- **OKS@0.50: 1.000**
- **OKS@0.75: 1.000**
- Gold: 29 người
- Ghép được: 29 người
- Thiếu: 0
- Thừa: 0
- Mức: **Xuất sắc**

Lỗi còn lại:

- 1 lỗi **Xoá khớp bị che**: `train_13`, người #2, `left_knee`.
- 10 lỗi **Lệch nhẹ**.
- 62 mục **Cờ khác gold** — không trừ OKS.
- 74 mục **Gold để v=0** — không trừ OKS.

## 5. Reviewer checklist

| | Mục kiểm | Kết quả | Ghi chú |
| --- | --- | --- | --- |
| 1 | Đọc đủ toàn bộ file nhãn | ✅ | 20/20 file |
| 2 | Không thiếu/thừa người so với gold | ✅ | 29 gold / 29 ghép / 0 thiếu / 0 thừa |
| 3 | Không còn lỗi định dạng | ✅ | `check_pose_labels.py`: ĐẠT định dạng |
| 4 | Khớp bị che ưu tiên `v=1`, không dùng `v=0` tùy tiện | ✅ sau rework | Các cảnh báo train_06/09/11/15 đã được xử lý |
| 5 | Kiểm lỗi nhầm người | ✅ đã sửa | `train_04` người #1, `left_wrist` |
| 6 | Kiểm lỗi xoá khớp bị che | ⚠️ còn 1 | `train_13` người #2, `left_knee` |
| 7 | Kiểm trái/phải bằng visualize | ✅ | Không thấy lỗi đảo trái/phải hàng loạt |
| 8 | Visibility report đã tạo | ✅ | `outputs/visibility_report.json`, `reports/visibility_report.md` |
| 9 | Đã chấm với gold | ✅ | OKS tăng từ 0.913 lên 0.920 |
| 10 | Lỗi nhẹ đã được ghi nhận | ✅ | 10 lỗi lệch nhẹ, ưu tiên thấp |

## 6. Kết luận

- Lỗi lặp lại nhiều nhất trong quá trình tự kiểm là **nhầm giữa Outside (`v=0`) và Occluded (`v=1`)** ở các khớp bị che.
- Đây chủ yếu là **lỗi thao tác/đánh cờ visibility**, không phải lỗi topology.
- Sau rework, nhãn đạt định dạng, không thiếu/thừa người và OKS tăng từ **0.913 lên 0.920**.
- Còn một ca cần lưu ý: `train_13`, người #2, `left_knee` đang bị đánh Outside trong khi gold coi là khớp bị che.
