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

Số đo từ `scripts/pp_prelabel.py --frames d14_<ID>/frames_B.txt --config d14_<ID>/best_config.json --gt d14_<ID>/ref_B.json`:

| Xóa | Thêm | Sửa | Chi phí |
|---:|---:|---:|---:|
| | | | |

- Loại box tốn công sửa nhất ở tập B: ...

## 5. Giới hạn

- Nêu ít nhất 2 giới hạn (ví dụ: chỉ 3 frame tune; model KITTI không có Truck/Bus; chi phí là công thức, chưa phải thời gian sửa thật): ...
