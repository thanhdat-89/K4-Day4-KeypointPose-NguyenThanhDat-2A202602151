# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: ______   Nhóm: ______   Ngày: ______

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số                         |      Giá trị |
| -------------------------------- | -------------: |
| Số ảnh đã gán               |             20 |
| Số skeleton                     |             28 |
| v=2 / v=1 / v=0                  | 337 / 113 / 26 |
| Thời gian trung bình mỗi ảnh |       10 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. left_ear (57%)
2. right_ear (50%)
3. left_eye (29%) / left_hip (29%

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

<!-- Trả lời 2–4 câu. Phân biệt “hay bị che” với “khó xác định vị trí giải phẫu”; nêu bằng
chứng nhìn thấy thay vì chỉ nêu cảm giác. -->

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số                | Trước rework | Sau rework |
| ----------------------- | -------------: | ---------: |
| OKS trung bình         |          0.890 |      0.900 |
| OKS@0.50                |          0.921 |      0.931 |
| OKS@0.75                |          0.877 |      0.897 |
| Lỗi`dao_trai_phai`   |              1 |          1 |
| Lỗi`nham_nguoi`      |              1 |          1 |
| Lỗi`xoa_khop_bi_che` |             23 |         23 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

<!-- Mỗi dòng phải có: tên ảnh + người thứ mấy + keypoint + thao tác sửa. Không viết “đã sửa
lại một số lỗi”. -->

- train_02.jpg - người #37 - toàn bộ các khớp vai/hông - sửa lỗi đảo trái/phải do gán nhầm hướng.
- train_13.jpg - người #307 - bổ sung skeleton còn thiếu (thiếu hẳn một người).
- train_03.jpg - người #73 - left_hip, right_hip - sửa lỗi nhầm người/chấm sang cơ thể bên cạnh.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Lỗi đảo trái/phải xảy ra ở ảnh train_02, đây là ảnh có góc nhìn tương đối dễ (nhìn chính diện), tuy nhiên do sơ suất trong quá trình xác định hướng bên trái và bên phải của đối tượng (nhìn từ góc nhìn của người trong ảnh thay vì góc nhìn của người gán nhãn) dẫn đến việc gán ngược vị trí các khớp vai và hôn

<!-- Nếu không có lỗi, ghi rõ “Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh.” -->

## 3. Kiểm chéo

Bạn cùng nhóm: ______

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| ----- | ---: | --: | ----: | --------------------------------------- |
|       |      |     |       |                                         |
|       |      |     |       |                                         |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

<!-- Viết một rule kiểm chứng được: điều kiện nhìn thấy/căn cứ vị trí → chọn v=1 hoặc v=0.
Không chỉ ghi “cẩn thận hơn khi gán”. -->

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số       | yolo26n-pose gốc | Sau fine-tune | Chênh |
| -------------- | ----------------: | ------------: | -----: |
| pose_mAP50     |            0.8450 |        0.8450 | 0.0000 |
| pose_mAP50-95  |            0.6853 |        0.6908 | 0.0055 |
| pose_precision |            0.9734 |        0.9792 | 0.0058 |
| pose_recall    |            0.8462 |        0.8462 | 0.0000 |
| box_mAP50-95   |            0.8119 |        0.8041 | 0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model
   điều gì mà COCO chưa dạy, và nó làm hỏng điều gì? pose_mAP50-95 tăng nhẹ +0.0055. Việc huấn luyện trên 20 ảnh riêng giúp model học được các đặc thù bối cảnh cụ thể của tập dữ liệu (ví dụ: cách định nghĩa khớp bị che khuất đặc thù), tuy nhiên quy mô dữ liệu nhỏ cũng khiến độ chính xác của bounding box (box_mAP50-95) giảm nhẹ -0.0078 do overfitting cục bộ.
2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm
   *khớp* dễ hơn? Vì sao? Chỉ số box_mAP cao hơn đáng kể so với pose_mAP (ví dụ box_mAP50-95 khoảng 0.8041 so với pose_mAP50-95 là 0.6908). Model tìm *người* (bounding box) dễ hơn rất nhiều so với việc định vị chính xác từng *khớp* (keypoints), bởi vì việc dự đoán tọa độ khớp đòi hỏi độ chính xác giải phẫu cao cấp hơn và dễ bị ảnh hưởng bởi góc nhìn, độ mờ.
3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43
   (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn): Ảnh `train_02` gặp lỗi **đảo trái/phải** ở vùng vai và hông do nhãn huấn luyện ban đầu có sự nhầm lẫn về hướng. Ảnh `train_02` có OKS thấp nhất (0.347). Ở ảnh này, nhãn của tôi có sự nhầm lẫn về đảo trái/phải, do đó mô hình đúng hơn khi dự đoán đúng hướng cấu trúc cơ thể dựa trên đặc trưng hình ảnh tổng thể.
4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?
5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó
   nói gì về bức ảnh đó? 

1. > Ảnh `train_02` vừa là ảnh có chất lượng gán nhãn thấp nhất (lỗi đảo trái/phải), vừa là ảnh có OKS thấp nhất khi so với model. Điều này cho thấy bức ảnh có độ phức tạp cao về góc nhìn hoặc tư thế, khiến cả người gán nhãn thủ công lẫn mô hình học máy đều dễ mắc sai lầm tương tự.
   >

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.
Đối với khớp `left_wrist` (cổ tay trái) trong ảnh `train_04`, khi cổ tay bị che khuất một phần bởi thân áo nhưng vẫn phân biệt được vị trí khớp xương bên dưới, tôi đã quyết định chọn trạng thái `v=1` (Occluded) thay vì `v=0` (Outside). Bằng chứng nhìn thấy là hình dáng cánh tay vẫn kéo dài đến điểm đó và bị che khuất nhẹ bởi trang phục. Việc chọn `v=1` giúp bảo toàn thông tin cấu trúc xương cho skeleton thay vì loại bỏ hoàn toàn điểm khớp khỏi không gian đánh giá OKS.

<!-- Cấu trúc gợi ý: (1) train_XX + người thứ mấy + keypoint; (2) căn cứ thị giác như phần cơ
thể liền kề, trang phục hoặc vật che; (3) vì sao khớp còn trong khung (v=1) hay đã ra khỏi
khung (v=0). -->
