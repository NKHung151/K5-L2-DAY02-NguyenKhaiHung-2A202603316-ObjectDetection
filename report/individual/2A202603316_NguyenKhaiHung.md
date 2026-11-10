# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Nguyễn Khải Hưng
- MSSV: 2A202603316
- Email: hung1512004kh@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/NKHung151/K5-L2-DAY02-NguyenKhaiHung-2A202603316-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/13D5Ywn3N3l3TnrkofMl2RdIGdhZBjoyW?usp=sharing

## 2. Schema và mini guideline

Chi tiết trong [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Dùng đúng schema bốn lớp `car=0, truck=1, bus=2, van=3`.

Ba ca mơ hồ chính đã quyết định:

1. `drive_001`: xe trắng nhỏ, dáng hộp, mui cao đỗ phía trái, phân vân `car`/`van`. Quyết định: `van`.
2. `drive_042`: xe tải chở hàng bao xếp cao. Quyết định: box chỉ ôm thân xe (cabin và thùng), không gồm khối hàng, lớp `truck`.
3. `drive_030` (và các ảnh xe xếp hàng): mỗi xe tách được một box trên phần nhìn thấy; xe xa chỉ còn mảng nhỏ không xác định lớp thì không vẽ.

Ngoài ra guideline còn bổ sung: xe nhòe do chạy nhanh (`drive_020`, `drive_001`), xe buýt khớp nối `drive_041` (một box `bus`).

Phần cần giảng viên làm rõ: hàng hóa trên xe tải có tính vào box không.

## 3. Annotation và self-QC

- Số file nhãn train: 50 (`dataset/labels/train/drive_001.txt` đến `drive_050.txt`). Tổng 1045 box: car 848, truck 28, bus 104, van 65.
- Kết quả checker: `python -X utf8 tools/check_yolo_labels.py` cho kết quả **ĐẠT cấu trúc: 50/50**.
- Ảnh đã rà bằng mắt: `drive_041` đến `drive_050` (10 ảnh).
- Lỗi phát hiện khi rà và cách sửa: `drive_042`, xe buýt ở rìa dưới ảnh bị gán nhầm `car` (0), đã sửa thành `bus` (2). 
- Commit khóa nhãn: `5ab1c60` ("Khoa nhan ban dau").

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 466 | không rework |
| FP | 579 | không rework |
| FN | 187 | không rework |
| Precision | 0.4459 | không rework |
| Recall | 0.7136 | không rework |
| F1 | 0.5489 | không rework |
| mean_iou_of_matches | 0.8468 | không rework |
| passed | False (theo ngưỡng gốc F1 ≥ 0.80) | không rework |

Kết quả nằm ở `outputs/annotation_evaluation.json`.

## 5. Kết quả YOLO

- Runtime: Google Colab, GPU (`device: "0"` trong `args.yaml`). 
- Tham số so với notebook: không thay đổi (`yolo26n.pt`, 50 epoch, `imgsz=640`, `batch=-1`, `seed=20260909`).
- Best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không nộp file `.pt`).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.407 |
| Recall | 0.234 |
| mAP50 | 0.274 |
| mAP50–95 | 0.202 |

Tài liệu đi kèm: [confusion matrix](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [confusion matrix chuẩn hóa](assets/yolo26_runs/vehicle_eval/confusion_matrix_normalized.png), [log train](assets/yolo26_runs/vehicle_annotation/results.csv), [ảnh dự đoán eval](assets/eval_predictions/visuals/).

**Lưu ý về checkpoint:** trong `results.csv`, epoch tốt nhất theo mAP50–95 là **epoch 1** (0.202); các epoch sau dao động từ 0.11 đến 0.18, và precision/recall thay đổi mạnh giữa các epoch (precision từ 0.66 xuống 0.18). Số liệu trên chính là của epoch 1. Với chỉ 50 ảnh train và 10 ảnh eval, metric rất nhiễu, nên con số này không nên coi là đáng tin cậy.

### Trả lời bốn câu hỏi

**1. Lớp nào recall thấp nhất? Ảnh nào minh họa?**
Từ confusion matrix chuẩn hóa (cột = nhãn thật), recall theo lớp xấp xỉ: car 0.89, truck 0.20, bus 0.50, **van 0.00**. Eval có 94 car, 5 truck, 2 bus, 12 van. Với `van`, cả 12 box thật: 11 bị dự đoán thành `bus`, 1 bị bỏ sót, không có box nào đúng lớp `van`. Ảnh minh họa: [drive_052](assets/eval_predictions/visuals/drive_052.jpg) (chiếc xe buýt hai tầng ở trái, nhãn eval là `van`, model dự đoán `bus`) và [drive_053](assets/eval_predictions/visuals/drive_053.jpg) (xe buýt lớn ở giữa ảnh, nhãn eval là `van`, model dự đoán `bus` với độ tin cậy 0.8). `truck` cũng thấp: 4/5 box thật bị dự đoán là `car` (ví dụ xe tải nhỏ ở xa trong `drive_052`, `drive_053`).

**2. Nếu mAP50 cao nhưng mAP50–95 thấp, box đang gặp vấn đề gì?**
Chênh lệch này nghĩa là box đúng vị trí tương đối (IoU ≥ 0.5) nhưng cạnh box chưa khít (IoU khoảng 0.75 trở lên thì mất điểm), nên cần kiểm tra cạnh box: lỏng hay cắt vào thân xe, xe nhòe, box lấy thêm mặt đường. Trong lần chạy này mAP50 chỉ 0.274 (không cao) và chênh với mAP50–95 (0.202) khoảng 0.07, nên điểm nghẽn chính là phân loại và độ bao phủ, không phải độ khít box. Điều này khớp với mean IoU của nhãn tôi so với gold là 0.847 (box sát).

**3. Lỗi class nào liên quan guideline? Dữ kiện nào hỗ trợ?**
Lỗi lớn nhất là `van` bị dự đoán thành `bus` (11/12 box). Khi xem nhãn eval trong `val_batch0_labels.jpg` ([ảnh](assets/yolo26_runs/vehicle_eval/val_batch0_labels.jpg)), tôi thấy nhiều xe buýt cỡ lớn (ví dụ xe hai tầng ở `drive_052`, xe buýt ở `drive_053`, xe buýt vàng trong `drive_058`) được gán `van` trong nhãn eval. Ngược lại, theo schema trong README và guideline của tôi, xe buýt lớn phải là `bus`; tôi gán đúng như vậy ở tập train (104 box `bus`), nên model học và dự đoán `bus` cho các xe này, bị tính là sai. Như vậy lỗi này nhiều khả năng đến từ **khác biệt quy ước lớp giữa nhãn eval và schema/guideline**, không hẳn do model hoặc do box. Đây mới là nhận định từ việc xem ảnh, tôi **chưa kiểm tra** quy ước này có xuất hiện trong gold của tập train hay không, và cần hỏi giảng viên để xác nhận. Lỗi thứ hai là `truck` bị nhầm `car` (4/5); `truck` chỉ có 28 box trong tập train và 5 box trong eval, quá ít để model học, và các xe tải trong eval thường nhỏ, ở xa nên trông giống xe con.

**4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng?**
Nếu đưa eval vào train thì model đã "thấy" đáp án, các chỉ số trên eval sẽ cao giả tạo và không đo được khả năng tổng quát hóa. Trong notebook này, 10 ảnh eval cũng đóng vai trò validation để theo dõi từng epoch và chọn `best.pt` (đúng như kết quả: best là epoch 1). Vì vậy chúng không phải test set độc lập: số liệu đã bị "chọn" theo chính tập này. Với 10 ảnh thì kết quả cũng rất nhiễu.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Rà nhãn `drive_042` | Xe buýt rìa dưới bị gán `car`| Gán nhãn lại trên CVAT| Đã sửa, số box theo lớp đổi car 849 sang 848, bus 103 sang 104 |
| Gold QC | F1 = 0.549 thấp hơn ngưỡng gốc 0.8 | Lab coach hạ ngưỡng F1 xuống 0.5 | Đi tiếp, không rework; chưa tìm nguyên nhân từng ảnh |
| Train YOLO | Best checkpoint là epoch 1, metric dao động mạnh | Giữ nguyên tham số notebook | Ghi nhận trong mục 5, kết quả không ổn định |

## 7. Tự đánh giá theo rubric

- **Hoàn thành và định dạng YOLO:** đủ 50 file, checker đạt, ID lớp đúng.
- **Đầy đủ vật thể:** có thể chưa tốt; recall so với gold là 0.714.
- **Độ bám sát box:** tốt theo số liệu, mean IoU = 0.847.
- **Phân lớp nhất quán:** cần xác nhận với giảng viên giữa `van`/`bus`.
- **Mini guideline:** có định nghĩa lớp, quy tắc chung và 6 ca mơ hồ.
- **Gold QC:** F1 = 0.549, mean IoU = 0.847 đạt, không rework.
- **Pipeline YOLO:** Colab chạy xong, không dùng eval để train, có metrics, ảnh dự đoán và phân tích; kết quả model thấp và không ổn định.
- **Chưa làm / chưa kiểm được:** rà bằng mắt 40 ảnh đầu, tìm nguyên nhân FP/FN của gold QC.

## 8. Công cụ AI và tài liệu tham khảo

Tôi dùng Claude Code (Anthropic) trong quá trình làm bài, cụ thể: hướng dẫn thứ tự các bước làm; 

Nguồn ngoài repo: không có.
