# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 15.97 khớp có v > 0 mỗi người
- Tổng: v=2 357 | v=1 106 | v=0 30

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 23 | 6 | 0 | 21% |
| 1 | left_eye | 21 | 8 | 0 | 28% |
| 2 | right_eye | 20 | 9 | 0 | 31% |
| 3 | left_ear | 15 | 11 | 3 | 38% |
| 4 | right_ear | 15 | 13 | 1 | 45% |
| 5 | left_shoulder | 27 | 2 | 0 | 7% |
| 6 | right_shoulder | 28 | 1 | 0 | 3% |
| 7 | left_elbow | 25 | 4 | 0 | 14% |
| 8 | right_elbow | 26 | 2 | 1 | 7% |
| 9 | left_wrist | 19 | 9 | 1 | 31% |
| 10 | right_wrist | 21 | 6 | 2 | 21% |
| 11 | left_hip | 20 | 9 | 0 | 31% |
| 12 | right_hip | 19 | 10 | 0 | 34% |
| 13 | left_knee | 23 | 3 | 3 | 10% |
| 14 | right_knee | 24 | 3 | 2 | 10% |
| 15 | left_ankle | 16 | 4 | 9 | 14% |
| 16 | right_ankle | 15 | 6 | 8 | 21% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
