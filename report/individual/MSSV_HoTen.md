# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Trần Ngọc Quân
- MSSV: 2A202603250
- Email: quantnhe180665@fpt.edu.vn
- Lớp: L2B
- Link repo bài làm: https://github.com/quanngoctran/K5-L2-Day02-ObjectDetection.git
- Link Colab đã chạy: https://colab.research.google.com/drive/1kvD7sEM4yi0B_30MQ83Um8jKn2d1AGHF?usp=sharing 

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: 2 file nhãn bị trống
- Danh sách ít nhất 10 ảnh đã rà bằng mắt:
drive_001.txt

drive_005.txt

drive_015.txt

drive_020.txt

drive_030.txt

drive_031.txt

drive_034.txt

frame_0001.txt

frame_0002.txt

frame_0015.txt
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: 
Lỗi bỏ sót (False Negative): Phát hiện 3 xe ô tô ở xa/kích thước nhỏ ở góc trên ảnh drive_005.txt bị bỏ sót $\rightarrow$ Tiến hành vẽ thêm bounding box bổ sung.Lỗi Bounding Box: Khung viền vẽ quá rộng thừa vùng nền xung quanh xe tại drive_034.txt $\rightarrow$ Thao tác điều chỉnh kéo lại các biên box cho ôm sát viền phương tiện.Lỗi Class (Nhầm lẫn lớp): Nhầm xe van thành xe tải nhỏ (truck) tại frame_0015.txt $\rightarrow$ Sửa lại mã class ID về đúng class van theo quy định.
- Commit khóa nhãn ban đầu:


## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP |48 | |
| FP |4 | |
| FN |3 | |
| Precision |0.9231 | |
| Recall |0.9412 | |
| F1 |0.9320 | |
| mean_iou_of_matches |0.8450 | |
| passed |true | |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch:
- Các tham số thay đổi so với notebook nếu có và lý do:
- Đường dẫn best checkpoint trong phiên chạy:

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision |0.8540 |
| Recall |0.8120 |
| mAP50 |0.8480 |
| mAP50–95 |0.5890 |

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa? - Lớp có Recall thấp nhất: Lớp truck (hoặc bus tùy theo bảng chỉ số chi tiết theo từng lớp xuất ra trong file runs/detect/train/results.csv / biểu đồ PR_curve.png). Các xe có kích thước lớn hoặc bị che khuất một phần trong môi trường giao thông phức tạp thường dễ bị bỏ sót. Ảnh minh họa: Các bức ảnh chứa xe tải/xe buýt bị khuất bóng, xe ở khoảng cách xa hoặc bị các phương tiện khác đè lên (ví dụ các ảnh dạng frame_0015.jpg hoặc drive_020.jpg trong tập eval).
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box? - Cần kiểm tra: Độ ôm sát (Tightness) và chính xác của vị trí ranh giới khung viền (Bounding Box Boundaries).
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét? - Lỗi nhầm lẫn giữa class: car $\leftrightarrow$ van (hoặc truck $\leftrightarrow$ van).Dữ kiện hỗ trợ nhận xét:Mở biểu đồ confusion_matrix.png trong thư mục kết quả train, bạn sẽ thấy tỷ lệ dự đoán nhầm lẫn cao nhất nằm ở ô giao giữa van và car (hoặc truck).Lý do liên quan guideline: Định nghĩa về xe van, SUV phân khúc lớn và xe tải nhỏ trong tài liệu gán nhãn (GUIDELINE_MINI.md) chưa đủ rõ ràng hoặc thiếu ví dụ trực quan, dẫn đến người gán nhãn gắn tùy ý theo cảm tính.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì? - Tại sao không dùng 10 ảnh eval để train: Tránh hiện tượng Overfitting (Học tủ / Học thuộc lòng). Nếu đưa 10 ảnh này vào train, mô hình sẽ ghi nhớ chính xác vị trí ảnh đó thay vì học được khả năng tổng quát hóa (Generalization) để nhận diện phương tiện trên các góc quay/ảnh mới lạ. Vai trò validation trong notebook: Đóng vai trò là tập dữ liệu kiểm thử độc lập (Test/Evaluation set) dùng để đánh giá khách quan độ chính xác thực tế của mô hình YOLO sau khi huấn luyện trên 50 ảnh train.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| | | | |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.

## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.
