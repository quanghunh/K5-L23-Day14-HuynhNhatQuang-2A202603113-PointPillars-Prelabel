# Báo cáo Day 14 — Huỳnh Nhật Quang — 2A202603113

> Sao chép file này thành `report/individual/<MSSV>_<HoTen>.md` (không dấu), ví dụ `2A20260000_NguyenVanAn.md`.

- Mã học viên dùng với `--student`: 113
- `seed_tag` trong `frames_A.txt`: `393590736f`

## 1. Dự đoán trước sweep (bước 5b)

Ghi **trước** khi chạy `scripts/pp_sweep.py`, rồi commit và push ngay (lịch sử commit là bằng chứng thời điểm viết). Không sửa phần này sau khi có kết quả.

- Tham số tôi nghĩ giảm công sửa nhiều nhất: `score_thr`
- Vì sao (nhìn ảnh BEV tập A: box thừa nhiều hay box sót nhiều?): Ảnh BEV tập A có nhiều box thừa với score thấp nên nếu tăng tham số này lên thì ngưỡng điểm tin cậy chung sẽ tăng lên dẫn đến các box có điểm tin cậy thấp hơn ngưỡng này sẽ bị loại bỏ.

## 2. Tập A: sửa tay và tune

| Cấu hình | Xóa | Thêm | Sửa | Chi phí |
|---|---:|---:|---:|---:|
| Gốc |47 | 11| 2|23.1 |
| `best_config.json` |22 |11 | 2|18.6 |

- Tham số đã đổi trong `best_config.json`: `score_thr`: '0.4'
- Kết quả leave-one-out (dán các dòng `scripts/pp_sweep.py` in ra): 
Leave-one-out (chọn trên các frame còn lại, chấm trên frame bỏ ra):
  s01f24: cấu hình chọn {'score_thr': '0.5', 'min_points': '0'} → 12.7 (tốt nhất có thể 11.3)
  s02f18: cấu hình chọn {'score_thr': '0.5', 'min_points': '0'} → 4.7 (tốt nhất có thể 3.3)
  s05f31: cấu hình chọn {'score_thr': '0.4', 'min_points': '0'} → 4.0 (tốt nhất có thể 2.2)

- Cấu hình có ổn định trên từng frame không? Có
Vì sao tôi vẫn chọn: Cấu hình cho thấy có thể giảm đáng kể số lượng box thừa bằng cách tăng ngưỡng điểm tin cậy chung, mặc dù vẫn có một số box thừa ở các frame khác nhau. Việc chọn `score_thr` là '0.4' giúp cân bằng giữa việc giảm box thừa và giảm box sót.

## 3. Trước/sau (bước 8)

![so sánh](K5-L23-Day14-HuynhNhatQuang-2A202603113-PointPillars-Prelabel/d14_113/compare/s01f24.jpg)

- `scripts/pp_compare.py` báo giảm 19% công sửa trên tập A.
- Dự đoán ở mục 1 đúng hay sai? Đúng
Giải thích bằng số box xóa/thêm/sửa:  Ở cấu hình số box xóa 12 thêm 9 sửa 1 so với cấu hình mới này số box xóa 6 thêm 9 sửa 1. Số box thừa cần xóa đã giảm đi một nữa cho thấy model đã loại bỏ các box có score thấp qua đó rút ngắn công sửa và chi phí hơn so với cấu hình đầu.

## 4. Tập B

Số đo từ `scripts/pp_prelabel.py --frames d14_113/frames_B.txt --config d14_113/best_config.json --gt d14_113/ref_B.json`:

| Xóa | Thêm | Sửa | Chi phí |
|---:|---:|---:|---:|
|90 |34 |8 |65 |

- Loại box tốn công sửa nhất ở tập B: car

## 5. Giới hạn

- Độ ổn định của cấu hình khi tinh chỉnh trên tập mẫu nhỏ: Bộ tham số tối ưu hiện tại chưa đảm bảo tính khái quát đồng đều trên các khung hình phức tạp. Bằng chứng: Cấu hình chỉ được chọn từ 3 frame (tập A) với chênh lệch leave-one-out lần lượt là 1.4, 1.4 và 1.8; khi chạy trên 12 frame của tập B (tổng chi phí 65.0 gồm 90 xóa, 34 thêm, 8 sửa), sai số tăng đột biến ở các frame đông vật thể như s03f21 (chi phí 14.3 với 11 xóa, 11 thêm).


- Độ lệch miền phân bố lớp (KITTI) và thước đo chi phí quy ước: Mô hình gặp hạn chế về độ phủ lớp vật thể theo chuẩn KITTI và hàm chi phí chưa phản ánh thời gian hậu kiểm thực tế. Bằng chứng: Toàn bộ 12 frame tập B bỏ sót 34 vật thể (chiếm ~52% tổng chi phí), dự đoán chỉ có Car, Cyclist, Pedestrian (0–2 box/frame) mà không có Truck hay Bus, trong khi thao tác sửa chỉ xuất hiện 8 lần và chưa có số đo thời gian xử lý.


