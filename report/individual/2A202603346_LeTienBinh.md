# Báo cáo Day 14 — Lê Tiến Bình — 2A202603346

- Mã học viên dùng với `--student`: `2A202603346`
- `seed_tag` trong `frames_A.txt`: `393590736f`

## 1. Dự đoán trước sweep (bước 5b)

Ghi **trước** khi chạy `scripts/pp_sweep.py`, rồi commit và push ngay (lịch sử commit là bằng chứng thời điểm viết). Không sửa phần này sau khi có kết quả.

> Ghi chú: phần dự đoán này do trợ lý AI (Claude) soạn theo yêu cầu của tôi, sau khi tôi đã xin và được mentor đồng ý. Số liệu lấy từ nhãn `ref_A.json` do tôi tự sửa trên CVAT.

- Tham số tôi nghĩ giảm công sửa nhiều nhất: **không tăng `score_thr`**, giữ ở mức thấp (`score_thr` = 0.1–0.2), `min_points` = 0–5. Tôi dự đoán tăng `score_thr` lên 0.3–0.5 sẽ làm công sửa **tăng** chứ không giảm; `min_points` gần như không ảnh hưởng.
- Vì sao (nhìn ảnh BEV tập A: box thừa nhiều hay box sót nhiều?):
  - Ở bước 5, cấu hình gốc phải xóa 37 hộp, chỉ thêm 2 hộp (chi phí 13.1). Tập A thừa hộp nhiều hơn sót, nên thoạt nhìn tăng `score_thr` để bớt rác có vẻ hợp lý.
  - Nhưng score của hộp rác và hộp đúng chồng lấn nhau: hộp rác (Car nằm trên bụi cây, Cyclist lơ lửng trên bồn cây ở hai dải phân cách trong ảnh BEV) phần lớn có score 0.3–0.55, trong khi nhiều hộp đúng cũng chỉ có score 0.13–0.47. Dưới 0.4 có 24 hộp rác nhưng cũng có 15 hộp đúng; dưới 0.3 có 5 hộp rác và 9 hộp đúng.
  - Mỗi hộp rác bị lọc chỉ tiết kiệm 0.3, còn mỗi hộp đúng bị lọc mất phải vẽ lại tốn 1.0. Ví dụ với `score_thr` = 0.4: tiết kiệm khoảng 24 × 0.3 = 7.2 nhưng tốn thêm khoảng 15 × 1.0 = 15, nên chi phí tăng.
  - Hộp rác nằm trên bụi cây và hàng rào nên có nhiều điểm LiDAR (chỉ 1/37 hộp bị xóa có dưới 10 điểm), vì vậy `min_points` không lọc được chúng.

## 2. Tập A: sửa tay và tune

| Cấu hình | Xóa | Thêm | Sửa | Chi phí |
|---|---:|---:|---:|---:|
| Gốc | 37 | 2 | 0 | 13.1 |
| `best_config.json` | | | | |

- Tham số đã đổi trong `best_config.json`: ...
- Kết quả leave-one-out (dán các dòng `scripts/pp_sweep.py` in ra): ...
- Cấu hình có ổn định trên từng frame không? Vì sao tôi vẫn chọn (hoặc không chọn) nó: ...

## 3. Trước/sau (bước 8)

![so sánh](../../d14_<ID>/compare/<frame>.jpg)

- `scripts/pp_compare.py` báo giảm (hoặc tăng) ...% công sửa trên tập A.
- Dự đoán ở mục 1 đúng hay sai? Giải thích bằng số box xóa/thêm/sửa: ...

## 4. Tập B

Số đo từ `scripts/pp_prelabel.py --frames d14_<ID>/frames_B.txt --config d14_<ID>/best_config.json --gt d14_<ID>/ref_B.json`:

| Xóa | Thêm | Sửa | Chi phí |
|---:|---:|---:|---:|
| | | | |

- Loại box tốn công sửa nhất ở tập B: ...

## 5. Giới hạn

- Nêu ít nhất 2 giới hạn (ví dụ: chỉ 3 frame tune; model KITTI không có Truck/Bus; chi phí là công thức, chưa phải thời gian sửa thật): ...
