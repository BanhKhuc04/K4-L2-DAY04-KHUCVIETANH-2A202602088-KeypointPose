# Mini guideline - nhóm: CÁ NHÂN  |  người gán: Khúc Việt Anh  |  ngày: 16/09/2026

> File này tổng hợp lại các quyết định đã dùng khi gán 20 ảnh core. Các luật bắt buộc giữ nguyên theo GUIDE của lớp.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Ước lượng tâm khớp hông theo trục thân và hướng đùi. Nếu hông vẫn nằm trong ảnh nhưng vải/quần áo che vị trí giải phẫu thì đặt chấm ước lượng và chọn `v=1`. | Hông là khớp giải phẫu, thường không có một điểm bề mặt nhìn thấy trực tiếp. Cần nhất quán để model không học hông lệch theo mép quần áo. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu còn nhìn đủ phần tai để xác định trực tiếp tâm tai thì `v=2`; nếu vị trí tai còn trong ảnh nhưng bị tóc/mũ che đến mức phải ước lượng thì `v=1`. | Phân biệt “nhìn thấy landmark” với “biết landmark nằm ở đâu nhưng bị che”. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Khớp nào còn trong ảnh thì gán bình thường hoặc `v=1` nếu bị che; khớp thật sự nằm ngoài mép ảnh mới chọn `v=0`. | `v=0` chỉ dành cho Outside, không dùng thay cho Occluded. |
| Cổ tay nằm sau tay lái / sau thân mình | Theo hướng cẳng tay để ước lượng vị trí cổ tay, đặt chấm và chọn `v=1` nếu cổ tay vẫn ở trong khung hình. | Tay lái/thân người là vật che, không làm khớp biến mất khỏi ảnh. |
| Hai người chồng lên nhau | Làm xong toàn bộ 17 điểm của một người rồi mới sang người kế tiếp; bám theo chuỗi vai -> khuỷu -> cổ tay và hông -> gối -> cổ chân của cùng cơ thể. Điểm của người bị che dùng `v=1`. | Tránh lỗi “nhầm người”, đặc biệt ở vùng tay/chân giao nhau. |
| Người nhỏ đến mức nào thì không gán nữa | Trong bộ core này **không tự đặt ngưỡng để bỏ người**. Nếu một người có trong ảnh core thì vẫn gán đủ 17 điểm; ca quá khó được ghi vào phần mơ hồ. | GUIDE cho biết bộ ảnh đã chọn để mọi người đủ lớn để gán; tự bỏ người sẽ gây thiếu skeleton. |

### Ảnh mẫu đã dùng để thống nhất quyết định

- Người quay lưng, mũ bảo hiểm và xe che nhiều khớp: `outputs/vis_train/train_06.jpg`.

  ![train_06 - mũ bảo hiểm và xe che khớp](outputs/vis_train/train_06.jpg)

- Chân bị thùng giấy/xe máy che nhưng vẫn còn trong khung: `outputs/vis_train/train_12.jpg`.

  ![train_12 - chân bị vật che](outputs/vis_train/train_12.jpg)

- Nhiều người đứng sát/chồng vùng tay và thân: `outputs/vis_train/train_13.jpg`.

  ![train_13 - nhiều người và che khuất](outputs/vis_train/train_13.jpg)

> Lưu ý: các ảnh trên là ảnh visualize của chính nhãn đã gán. Nếu giảng viên yêu cầu đúng **screenshot CVAT**, thay ba ảnh minh họa này bằng screenshot CVAT tương ứng trước khi nộp.

## 3. Ba ca mơ hồ đã gặp

### Ca 1 - ảnh `train_12`, người thứ `1`, khớp `left_knee` và `left_ankle`

- Mơ hồ ở chỗ nào: chân trái bị thùng giấy và xe máy che mạnh, ban đầu dễ nhầm là đã ra ngoài ảnh.
- Bạn quyết thế nào: sau rework, đặt lại vị trí ước lượng và chọn `v=1` cho khớp còn trong khung.
- Vì sao: có thể lần theo hướng đùi/cẳng chân và thấy vùng cơ thể tiếp tục phía sau vật che; đây là Occluded, không phải Outside.
- Nếu người khác quyết ngược lại thì model học sai cái gì: dùng `v=0` sẽ xoá khớp khỏi phép học/đánh giá và làm model kém ở tư thế người ngồi xe bị vật che chân.

### Ca 2 - ảnh `train_06`, người thứ `1`, khớp vùng mặt/tai

- Mơ hồ ở chỗ nào: người quay lưng và đội mũ bảo hiểm nên nhiều landmark mặt/tai không nhìn thấy trực tiếp.
- Bạn quyết thế nào: landmark vẫn nằm trong vùng đầu thì đặt chấm ước lượng và chọn `v=1`; chỉ dùng `v=0` nếu khớp thực sự ra ngoài mép ảnh.
- Vì sao: đầu vẫn nằm đầy đủ trong khung, việc không thấy landmark là do hướng quay và mũ che.
- Nếu người khác quyết ngược lại thì model học sai cái gì: model sẽ hiểu nhầm “bị che” thành “không tồn tại/ra khỏi ảnh”, làm visibility không nhất quán.

### Ca 3 - ảnh `train_13`, người thứ `2`, khớp `left_knee`

- Mơ hồ ở chỗ nào: nhiều người đứng gần nhau và phần chân bị che/chồng, nên ban đầu `left_knee` bị đánh Outside.
- Bạn quyết thế nào: rework sang `v=1` và đặt điểm ước lượng theo hướng hông -> gối -> cổ chân của đúng người.
- Vì sao: gold/rework chỉ ra khớp vẫn thuộc phần cơ thể nằm trong khung; khó khăn đến từ che khuất chứ không phải cắt mép ảnh.
- Nếu người khác quyết ngược lại thì model học sai cái gì: model sẽ mất supervision ở gối trong cảnh đông người và dễ nhầm chuỗi chân giữa hai người.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: **chưa có dữ liệu bài của bạn cùng nhóm trong gói đã gửi**.
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: chưa thể kết luận khi chưa có bảng compare.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: cần chạy `visibility_report.py --compare ...` với bài của bạn cùng nhóm rồi điền kết quả thật; không tự bịa số.
