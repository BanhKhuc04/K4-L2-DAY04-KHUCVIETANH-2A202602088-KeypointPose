# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Khúc Việt Anh   Nhóm: BÀI CÁ NHÂN   Ngày: 16/09/2026

> Số liệu visibility/gold lấy từ output công cụ trong repo. Số liệu model lấy từ lần chạy Colab đã hoàn thành. Hai mục chưa có dữ liệu gốc là **thời gian gán thực tế** và **kiểm chéo với bạn cùng nhóm**, nên được đánh dấu rõ thay vì tự ước lượng.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 358 / 110 / 25 |
| Thời gian trung bình mỗi ảnh | Chưa có log thời gian thực tế |

Ba khớp có `%v=1` cao nhất (từ `reports/visibility_report.md`):

1. `right_ear`: 45% (`13/29`)
2. `left_ear`: 38% (`11/29`)
3. `right_hip`: 34% (`10/29`)

Các khớp này nhìn chung đúng với những vị trí khó xử lý trong bộ ảnh. Hai tai thường bị tóc, mũ bảo hiểm hoặc góc quay che nên phải dùng `v=1`. Hông có tính chất khác: khó không chỉ vì bị che mà còn vì đây là vị trí giải phẫu phải ước lượng dưới quần áo; vì vậy cần giữ cùng một quy tắc giữa các ảnh.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.913 | 0.9215 |
| OKS@0.50 | 1.000 | 1.000 |
| OKS@0.75 | 1.000 | 1.000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 1 | 0 |
| Lỗi `xoa_khop_bi_che` | 3 | 0 |

**Tôi đã sửa gì giữa hai lần chạy:**

- `train_04.jpg`, người #1, `left_wrist`: kéo điểm từ cơ thể bên cạnh về đúng cổ tay của người #1 để sửa lỗi `nham_nguoi`.
- `train_12.jpg`, người #1, `left_knee` và `left_ankle`: đổi từ Outside (`v=0`) sang Occluded (`v=1`) và đặt chấm ước lượng vì chân vẫn nằm trong ảnh nhưng bị xe/thùng giấy che.
- `train_13.jpg`, người #2, `left_knee`: đổi từ Outside (`v=0`) sang Occluded (`v=1`) và đặt lại điểm theo chuỗi chân của đúng người.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh theo kết quả `eval_vs_gold` trước và sau rework.

Sau rework, 29/29 người được ghép với gold, không thiếu và không thừa. Các findings còn lại là `lech_nhe` (10), `co_khac_gold` (62) và `gold_khong_gan_nhan` (76); hai nhóm cờ khác gold/Gold v=0 không bị trừ OKS theo luật bài.

## 3. Kiểm chéo

Bạn cùng nhóm: **chưa có dữ liệu trong file đã gửi**

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| Chưa có dữ liệu compare | - | - | - | Cần chạy với thư mục nhãn của bạn cùng nhóm |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Chưa thể điền trung thực nếu chưa có bài của bạn cùng nhóm. Cần chạy `visibility_report.py --compare <labels-của-bạn-cùng-nhóm>` rồi cập nhật phần này và `reports/review_partner.md`.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` tăng `0.0055` (từ `0.6853` lên `0.6908`). Mức tăng nhỏ cho thấy fine-tune trên 20 ảnh không làm thay đổi mạnh model nền, nhưng có cải thiện nhẹ khả năng định vị pose trên tập test này. Bộ 20 ảnh có nhiều tình huống người đi xe, che khuất và nhiều người đứng gần nhau nên có thể bổ sung tín hiệu cho các ca tương tự; tuy nhiên 20 ảnh quá ít để kết luận model tổng quát tốt hơn.

2. Sau fine-tune, `box_mAP50-95 = 0.8041` còn `pose_mAP50-95 = 0.6908`, chênh `0.1133`. Model tìm **người** dễ hơn tìm **17 khớp**, vì box chỉ cần bao được đối tượng trong khi pose phải xác định nhiều landmark, kể cả các khớp bị che hoặc ở tư thế khó. Box mAP50-95 giảm nhẹ `0.0078`, trong khi pose mAP50-95 tăng `0.0055`.

3. Ở `test_02`, model tạo thêm một pose nhỏ ở vùng bên trái ảnh nơi không có một người rõ ràng. Tôi gọi đây là lỗi **trượt hẳn**, vì cả pose/detection bị đặt vào vùng không phải cơ thể người thay vì chỉ lệch vài pixel trên một khớp.

4. Ở mục 6, OKS thấp nhất giữa model và nhãn của tôi là `train_13 = 0.576`. Tôi ưu tiên nhãn của mình hơn ở ca này vì nhãn cuối đã được chấm với gold và sau rework không còn lỗi `dao_trai_phai`, `nham_nguoi` hay `xoa_khop_bi_che`; riêng `train_13` vẫn là cảnh nhiều người, che khuất và chồng vùng tay/chân nên model dễ bất đồng. Bằng chứng chính để quyết là gold và hình visualize, không phải chỉ dựa vào OKS model-vs-label.

5. Có. Match tệ nhất của nhãn tôi so với gold cũng nằm ở `train_13` (OKS thấp nhất khoảng `0.8347`), đồng thời `train_13` là ảnh model-vs-label thấp nhất (`0.576`). Điều này cho thấy đây là một ảnh thực sự khó: có nhiều người, khác kích thước, che khuất/chồng nhau nên cả annotator lẫn model đều dễ sai hơn ở việc ghép người và định vị khớp.

## 5. Một rule evidence bạn đã dùng

Ở `train_12`, người #1, tôi phải quyết định `left_knee` và `left_ankle` là `v=1` hay `v=0`. Phần chân bị thùng giấy và xe máy che, nhưng hướng đùi/cẳng chân cho thấy các khớp vẫn nằm **bên trong khung hình** chứ không bị cắt khỏi mép ảnh. Vì vậy sau rework tôi đặt chấm ở vị trí ước lượng và chọn `v=1` (Occluded). Nếu dùng `v=0`, khớp sẽ bị coi là Outside và mất supervision dù cơ thể thực tế vẫn tiếp tục phía sau vật che.
