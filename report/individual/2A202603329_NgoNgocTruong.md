# Báo cáo cá nhân — Ngày 2 Object Detection

> Bản nháp theo kết quả hiện có. Cần bổ sung phiên bản Torch và xác nhận các điểm chưa rõ trước khi nộp.

## 1. Thông tin học viên

- Họ tên: Ngô Ngọc Trường
- MSSV: 2A202603329
- Email: nntruong79230@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/nnt7923/K5-L2B-Day02-2A202603329-NgoNgocTruong-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/github/nnt7923/K5-L2B-Day02-2A202603329-NgoNgocTruong-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb

## 2. Schema và mini guideline

Chi tiết tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

Schema bắt buộc của đề bài là `car=0`, `truck=1`, `bus=2`, `van=3`. Mỗi xe hợp lệ có một box riêng, bám sát phần nhìn thấy; box dừng tại rìa ảnh nếu vật thể bị cắt khung. Với vật thể bị che nhưng còn nhận diện được lớp, vẫn gán nhãn phần nhìn thấy. Không gán người, xe máy hoặc vật thể ngoài bốn lớp.

Ba ca mơ hồ đã được ghi trong mini guideline:

| Ảnh | Tình huống đã ghi | Quyết định đã ghi | Cách áp dụng / điểm cần làm rõ |
|---|---|---|---|
| `drive_005.jpg` | Đuôi xe con nằm ngoài rìa ảnh; phần còn lại không bị vật khác che. | `car` | Giữ lớp theo loại xe, box kết thúc tại biên ảnh; phân biệt bị cắt khung với bị che khuất. |
| `drive_007.jpg` | Xe con bị xe phía trước và cột biển che một phần. | `car` | Box bao phần xe nhìn thấy nếu đủ căn cứ xác định lớp; không đoán phần khuất. |
| `drive_011.jpg` | Xe đen cạnh taxi vàng và xe nhìn nghiêng dễ nhầm với van. | `car` | Quyết định đã ghi cần đối chiếu lại hình dáng thân xe; phần lý do trong mini guideline hiện chưa mô tả đủ dấu hiệu phân biệt. |

**Điểm còn chưa nhất quán:** mini guideline hiện xếp pickup vào `truck`, trong khi README của bài xếp xe bán tải vào `car`. Đây là nội dung cần sửa theo schema bài và rà các nhãn liên quan; báo cáo không coi phần này đã được khắc phục. Trường hợp phân biệt minivan/MPV, van chở khách và minibus cần xin giảng viên làm rõ bằng ảnh cụ thể nếu còn mơ hồ.

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 file `.txt`, tương ứng `drive_001.jpg` đến `drive_050.jpg`. Bản nhãn đã kiểm tra có 728 box: 556 `car`, 18 `truck`, 82 `bus`, 72 `van`.
- **Kết quả checker:** đạt cấu trúc 50/50 file nhãn hợp lệ. Chưa ghi nhận lỗi cấu trúc phải sửa trong lần kiểm tra này. Checker chỉ xác nhận định dạng, không xác nhận mọi box/lớp đều đúng.
- **Danh sách ảnh đã rà bằng mắt, theo xác nhận của học viên:** `drive_001.jpg`, `drive_002.jpg`, `drive_003.jpg`, `drive_004.jpg`, `drive_005.jpg`, `drive_006.jpg`, `drive_007.jpg`, `drive_008.jpg`, `drive_009.jpg`, `drive_010.jpg`.
- **Lỗi bỏ sót/box/class phát hiện và cách sửa:** đã rà các ảnh trên nhưng chưa có ghi chép cụ thể về từng lỗi và thao tác sửa. Không khẳng định đã sửa hết lỗi hoặc không còn lỗi.
- **Commit lưu bộ nhãn và kết quả chấm:** [`89d8ccd82c21247538ec9cd27446b172669a8f31`](https://github.com/nnt7923/K5-L2B-Day02-2A202603329-NgoNgocTruong-ObjectDetection/commit/89d8ccd82c21247538ec9cd27446b172669a8f31), lúc 00:25:15 ngày 10/11/2026 (UTC+7). Commit này chứa cả nhãn và kết quả gold QC; chưa có bằng chứng về một commit khóa nhãn riêng trước khi nhận gold, nên không khẳng định đã hoàn thành đúng mốc khóa trước gold.

## 4. Gold QC và rework

Kết quả thực tế tại [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json), ghép box cùng lớp với IoU tối thiểu 0,50.

| Chỉ số | Lần chấm đã lưu, trước rework | Sau sửa nếu có |
|---|---:|---:|
| TP | 386 | Chưa có |
| FP | 342 | Chưa có |
| FN | 267 | Chưa có |
| Precision | 0,5302 | Chưa có |
| Recall | 0,5911 | Chưa có |
| F1 | 0,5590 | Chưa có |
| mean_iou_of_matches | 0,8427 | Chưa có |
| passed | `false` theo ngưỡng mặc định | Chưa có |

**Chưa rework hoặc chấm lại sau khi xem kết quả gold QC**

## 5. Kết quả YOLO

- **Runtime:** Google Colab, Python 3, GPU NVIDIA T4 theo ảnh chụp phiên chạy; cấu hình train dùng GPU `device=0`.
- **Ultralytics:** `8.4.208`, đọc từ metadata của `best.pt` trong ZIP kết quả.
- **Model và tham số:** `yolo26n.pt`, `epochs=50`, `imgsz=640`, `batch=-1`, `seed=20260909`, `plots=True`. Các tham số này trong [args.yaml](assets/yolo26_runs/vehicle_annotation/args.yaml) khớp cell train của notebook; không thấy thay đổi ở những tham số này. [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv) có log đến epoch 50.
- **Best checkpoint trong phiên chạy:** `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`.

Chỉ số sau khi đánh giá best checkpoint trên 10 ảnh eval, lấy từ [model_eval_metrics.json](assets/model_eval_metrics.json):

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0,3156 |
| Recall | 0,3066 |
| mAP50 | 0,2740 |
| mAP50–95 | 0,2006 |

Minh chứng: [10 ảnh dự đoán](assets/eval_predictions/visuals/), [kết quả train](assets/yolo26_runs/vehicle_annotation/), [kết quả đánh giá](assets/yolo26_runs/vehicle_eval/), [confusion matrix](assets/yolo26_runs/vehicle_eval/confusion_matrix_normalized.png), [Recall–Confidence](assets/yolo26_runs/vehicle_eval/BoxR_curve.png).

### 5.1. Lớp nào recall thấp nhất? Ảnh nào minh họa?

Trong [drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg), nhãn eval có một box `bus` ở vùng phía trên bên phải ảnh (tâm chuẩn hóa x≈0,828; y≈0,130), nhưng ảnh dự đoán không hiển thị box `bus` tương ứng. Tập eval chỉ có 2 box được gán `bus`, nên số liệu của lớp này nhạy với từng trường hợp và chưa đại diện cho dữ liệu giao thông nói chung.

### 5.2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?

Nếu mô hình đạt tốt ở IoU 0,50 nhưng giảm mạnh khi yêu cầu IoU cao hơn, cần kiểm tra độ sát và vị trí box: cạnh box có lệch, chứa quá nhiều nền, thiếu phần xe, gộp hai xe hoặc xử lý phần bị che/cắt ảnh không nhất quán hay không. Cũng cần xem kết quả theo kích thước vật thể.

Ở lần chạy này, mAP50=0,2740 và mAP50–95=0,2006; cả hai còn thấp, nên chưa thể mô tả đây là trường hợp “mAP50 cao”. Chênh lệch khoảng 0,0734 có thể gợi ý cần kiểm tra định vị, nhưng không đủ để quy toàn bộ lỗi cho box.

### 5.3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ?

Ví dụ cụ thể là xe con màu đỏ ở tiền cảnh phía dưới của [drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg): model dự đoán `van` với confidence 0,99, trong khi nhãn eval cho vật thể này là `car`. Confidence cao vẫn có thể đi kèm phân lớp sai. Cần rà cách phân biệt thân xe con/MPV với thân van trong guideline và các nhãn train tương ứng.

Confusion matrix chuẩn hóa còn cho thấy 0,80 số `truck` được ghép sang dự đoán `car`, và 0,92 số `van` được ghép sang dự đoán `bus`. Đây là dữ kiện để kiểm tra, không phải bằng chứng rằng mọi lỗi đều do guideline.

Có hai điểm cần làm rõ thêm:

- Mini guideline xếp pickup vào `truck`, khác schema đề bài xếp vào `car`; có nguy cơ gây nhãn không nhất quán, nhưng chưa xác định trường hợp cụ thể trong tập train chịu ảnh hưởng.
- Trong [drive_052.jpg](assets/eval_predictions/visuals/drive_052.jpg), xe hai tầng ở gần giữa ảnh được model gán `bus` 0,47, còn dòng nhãn eval tương ứng dùng ID 3 (`van`, tâm x≈0,427; y≈0,534). Cần hỏi giảng viên về trường hợp này thay vì tự sửa nhãn eval hoặc kết luận model sai chỉ từ confusion matrix.

Tập train cũng mất cân bằng: `car` có 556/728 box, còn `truck` chỉ 18 box. Chất lượng nhãn, số lượng từng lớp và số ảnh ít đều là các yếu tố cần xem xét; chưa có thí nghiệm tách riêng tác động của từng yếu tố.

### 5.4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng là gì?

Tách 10 ảnh eval khỏi dữ liệu cập nhật trọng số giúp đánh giá trên những ảnh không được dùng để học trực tiếp. Đưa chúng vào train sẽ làm rò rỉ dữ liệu và khiến kết quả đánh giá kém ý nghĩa.

Trong notebook, 10 ảnh này đồng thời được dùng làm validation để theo dõi quá trình train và chọn `best.pt`, rồi dùng đánh giá best checkpoint. Vì đã tham gia chọn model, đây không phải test set độc lập. Giữ nguyên split, không chuyển ảnh/nhãn eval vào train.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Gán nhãn sơ bộ | AI có thể bỏ sót hoặc nhầm `car/van/truck/bus`; không thể xem mọi dự đoán là ground truth. | Dùng CVAT xem và chỉnh; đã xác nhận rà `drive_001`–`drive_010`. | Chưa có nhật ký chi tiết lỗi đã sửa; gold QC vẫn có F1 0,5590. |
| Chạy gold QC | Script báo không tìm thấy gold dù đã giải nén. | Kiểm tra thư mục và sửa đối số thành `--gold-labels gold_train_labels/gold_train_labels`. | Đã tìm đủ 50 file gold và xuất JSON chấm. |
| Đọc điều kiện đạt | JSON báo `passed=false`, trong khi học viên nhận thông tin F1>0,50 được chấp nhận. | Đối chiếu F1 thực tế với ngưỡng mới, đồng thời giữ nguyên JSON và ngưỡng mặc định của script. | Cần bổ sung nguồn thông báo chính thức; chưa rework. |
| Phân tích model | Ví dụ xe con trong `drive_051` bị gán `van`; biểu đồ cho thấy nhầm lớp đáng kể. | Đối chiếu ảnh dự đoán, nhãn eval, confusion matrix và guideline. | Đã có ví dụ phân tích; chưa thử sửa nhãn/train lại để kiểm chứng nguyên nhân. |
| Tổng hợp báo cáo | ZIP có metrics nhưng chưa có thông tin phiên bản Torch trong các file đã đọc. | Lấy chỉ số từ JSON, tham số từ args.yaml và Ultralytics từ metadata checkpoint. | Cần lấy phiên bản Torch từ phiên Colab đã train. |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md), chưa tự quy đổi thành điểm:

| Tiêu chí | Bằng chứng hiện có | Phần còn thiếu / chưa kiểm được |
|---|---|---|
| Hoàn thành và định dạng YOLO | 50/50 file đạt checker, đúng bốn ID lớp. | Checker không xác nhận nội dung box/lớp. |
| Đầy đủ vật thể | Đã xuất 728 box; đã rà 10 ảnh. | Gold QC có FN=267; chưa kiểm từng trường hợp và chưa rework. |
| Độ bám sát và hình học box | Mean IoU của 386 cặp khớp là 0,8427. | Không suy rộng sang các box không ghép được hoặc toàn bộ 50 ảnh. |
| Phân lớp nhất quán | Có schema và ghi chép ba ca mơ hồ. | Quy tắc pickup trong guideline chưa khớp README; có nhầm lớp trong kết quả model. |
| Mini guideline | Có định nghĩa lớp, quy tắc box và ba case. | Cần sửa mâu thuẫn schema, làm rõ lý do ở `drive_011` và xác nhận các ca còn mơ hồ. |
| Gold-set annotation QC | Có JSON thực tế; đạt mean IoU; F1=0,5590. | Chưa đạt ngưỡng F1=0,80 của rubric đang lưu; ngưỡng mới cần dẫn thông báo chính thức. Chưa rework. |
| Pipeline YOLO và phân tích | Có log 50 epoch, metrics, 10 ảnh dự đoán, confusion matrix và phân tích ví dụ. | Cần bổ sung phiên bản Torch, kiểm tra link notebook có lưu output và quyền xem; chưa có test set độc lập. |

Chưa có bằng chứng commit khóa nhãn trước khi nhận gold. Chưa xác nhận toàn bộ tài liệu/ảnh báo cáo đã được push và nộp trên LMS; không coi các bước này đã hoàn tất chỉ vì file có trên máy.

## 8. Công cụ AI và tài liệu tham khảo

- **Phần học viên xác nhận tự kiểm tra:** đã rà bằng mắt `drive_001.jpg`–`drive_010.jpg`; chưa có ghi chép chi tiết về lỗi đã sửa và chưa rework sau gold QC. Các số liệu được lấy từ file kết quả thật; phần phân tích do AI hỗ trợ cần được học viên đọc, đối chiếu và xác nhận trước khi nộp.
- **CVAT:** giao diện gán nhãn và xuất YOLO. **Ultralytics YOLO26n/Google Colab:** huấn luyện và đánh giá theo notebook bài học.

Tài liệu tham khảo:

1. [README của bài](../../README.md), [GUIDE.md](../../GUIDE.md), [RULES.md](../../RULES.md), [SUBMISSION.md](../../SUBMISSION.md), [RUBRIC.md](../../RUBRIC.md).