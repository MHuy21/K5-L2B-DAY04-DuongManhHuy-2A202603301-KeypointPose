# Báo cáo cá nhân — DuongManhHuy-2A202603301

Dữ liệu: 12 ảnh người COCO val2017 (Lin et al., 2014) và 8 ảnh chân dung Wikimedia Commons, chỉ dùng trong lớp.

## 0. Thông tin học viên

- Họ tên: Dương Mạnh Huy
- MSSV: 2A202603301
- Email: manhhuy2104@gmail.com
- Link repository: [github.com/MHuy21/K5-L2B-DAY04-DuongManhHuy-2A202603301-KeypointPose.git](https://github.com/MHuy21/K5-L2B-DAY04-DuongManhHuy-2A202603301-KeypointPose.git)
- Phiên bản CVAT (Help → About): 2.74.1
- Máy: hệ điều hành Windows 11 · RAM 32 GB · SSD 1 TB · chạy CVAT chế độ đầy đủ
- Bộ ảnh (`src/exports/assignment.json` do `lab.py setup` tạo): có
- Giờ bắt đầu: 16:30
- Giờ kết thúc: 18:30

## 1. Kiểm tra label (CP0)

| Label    | Kiểu     | Số điểm | Tên điểm đúng bảng của lab? | Đúng thứ tự? | Số cạnh |
| -------- | -------- | ------: | --------------------------- | ------------ | ------: |
| `person` | Skeleton |      17 | Đúng                        | Đúng         |      19 |
| `face`   | Skeleton |      13 | Đúng                        | Đúng         |      13 |

Mẫu `person` dựng theo người quay mặt về camera (các điểm `left_*` nằm bên phải khung mẫu)? Có

Cách đổi cờ `occluded` / `outside` cho **một điểm** trên phiên bản CVAT này (một câu): Rê chuột vào đúng điểm cần đổi rồi bấm Q để bật occluded (v = 1, điểm thành vòng rỗng) hoặc bấm O để bật outside (v = 0, điểm biến mất)

## 2. Số lượng đã gán và thời gian (CP1, CP2)

| Nhóm ảnh   | Số ảnh | Số skeleton đã gán | Số người dưới 60 px (không gán) | Số poster / gương / màn hình bỏ qua | Thời gian (phút) |
| ---------- | -----: | -----------------: | ------------------------------: | ----------------------------------: | ---------------: |
| `easy`     |      5 |                  5 |                               0 |                                   0 |               15 |
| `occluded` |      3 |                  5 |                               0 |                                   0 |               15 |
| `back`     |      2 |                  4 |                               0 |                                   0 |               12 |
| `small`    |      1 |                  2 |                               5 |                                   0 |                6 |
| `crowd`    |      1 |                  3 |                               0 |                                   0 |               10 |
| `faces`    |      8 |                  8 |                               — |                                   0 |               20 |

- Số ảnh người đã xong ở mốc phút 105: 10\/ 10
- Thời gian thực tế (phút): CP1: **58 · CP2: 20** · CP3: 15
- Ảnh tốn thời gian nhất là ảnh nào, vì sao? : `000000542160.jpg` - ba người đứng sát nhau, tay và chân chồng lên nhau nên phải lần từng chi từ vai/hông để biết điểm nào thuộc người nào và kiểm lại trái/phải của chân (script cũng cảnh báo cặp knee, ankle ở ảnh này).

## 3. Bảng ca khó (CP1, CP2)

Mỗi điểm bạn phân vân một dòng, nhất là phân vân giữa `v = 1` và `v = 0`, hoặc không chắc hướng người.

| Ảnh              | Người / mặt | Điểm                         |         Cờ đã chọn | Phân vân giữa | Lý do chọn                                                                                                                                                                                                                                                     |
| ---------------- | ----------- | ---------------------------- | -----------------: | ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 000000359340.jpg | Người       | nose, left_eye, right_eye    |            `v = 0` | v=1           | Người quay lưng, mặt không thấy; mũ che phần đầu nên ba điểm này có ước lượng không chắc. Tai vẫn thấy viền nên để `v = 2`. Chọn `v = 0` thay vì `v = 1` để model không học vị trí đoán bừa                                                                    |
| 000000542160.jpg | Người       | right_elbow và right_wrist   | `v = 0` và `v = 1` | v=1           | Khuỷu phải bị che hoàn toàn, không thấy đoạn cánh tay trên nên không ước lượng chắc → `v = 0`. Cổ tay phải còn thấy một phần hoặc suy ra được từ vị trí găng → `v = 1`. Đây là ca phân vân nhất: tôi cân nhắc đặt cả hai điểm `v = 0`                          |
| 000000359340.jpg | Người       | 10 điểm bên trái và hai chân |            `v = 0` | v=1/v=0       | Người này bị khung ảnh cắt ở mép, chỉ lộ đầu, vai và một cánh tay (cao khoảng 76 px). Phần còn lại nằm ngoài khung hoặc khuất sau người phía trước, không có đoạn chi nào để suy ra vị trí nên outside, không kéo về mép ảnh. Vẫn gán skeleton vì cao từ 60 px |

## 4. Bảng tự review (CP3)

Đi từ ảnh cuối về ảnh đầu. 5 ảnh người (ít nhất 2 ảnh `back` hoặc `crowd`) và 3 ảnh mặt. Ít nhất 8 dòng, kể cả dòng `ok`. Ghi lỗi trước, sửa sau.

Mã lỗi dùng đúng một trong: `lr_swap`, `vis_flag`, `position`, `missing`, `extra`, `ok`.

| Ảnh                            | Người / mặt                   | Điểm                        | Mã lỗi     | Ghi chú                                                                                                                                        | Đã sửa? |
| ------------------------------ | ----------------------------- | --------------------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | ------- |
| `000000542160.jpg` (crowd)     | Người đứng giữa (#1)          | `right_knee`, `right_ankle` | `lr_swap`  | Script cảnh báo hai điểm này nằm ngược phía so với các cặp còn lại. Ba người đứng sát nhau, chân chồng lên nhau nên chưa chắc là đổi trái/phải | Đã sửa  |
| `000000359340.jpg` (back)      | Người quay lưng mũ đỏ (#1)    | cả skeleton                 | `ok`       | Quay lưng: các điểm `left_*` nằm bên trái ảnh, đúng. Tai thấy viền (`v = 2`), mũi và mắt `v = 0`, `right_wrist` ra ngoài khung nên `v = 0`     | --      |
| `000000349521.jpg` (easy)      | Người trượt tuyết áo cam (#1) | cả skeleton                 | `ok`       | Script không cảnh báo; người quay mặt về camera, `left_*` nằm bên phải ảnh                                                                     | --      |
| `000000220045.jpg` (occluded)  | Người thứ hai phía sau        | cả người                    | `missing`  | Phía sau người áo đen còn một người nữa (thấy mặt, chân và giày, cao hơn 60 px) nhưng ảnh mới có 1 skeleton                                    | Đã sửa  |
| `000000163118.jpg` (back)      | Người nhảy áo cam (#1)        | `right_ankle`, `left_ankle` | `ok`       | Script cảnh báo cổ chân ngược phía, nhưng người đang nhảy trên không, hai chân bắt chéo thật nên giữ nguyên                                    | --      |
| `face_421231.jpg` (thẳng)      | Mặt (#1)                      | cả mặt                      | `ok`       | Mặt nhìn thẳng: `left_eye_*` nằm bên phải ảnh, `*_eye_outer` xa sống mũi hơn `*_eye_inner`                                                     | --      |
| `face_342273.jpg` (kính trong) | Mặt (#1)                      | 8 điểm mắt                  | `vis_flag` | Kính trong, mắt thấy rõ nhưng cả 8 điểm mắt đang là `v = 1`, đúng ra phải `v = 2`                                                              | Đã sửa  |
| `face_219250.jpg` (kính râm)   | Mặt (#1)                      | 8 điểm mắt                  | `vis_flag` | Kính râm tối che mắt nhưng các điểm mắt đang là `v = 2`, đúng ra phải `occluded` (`v = 1`)                                                     | Đã sửa  |

Tổng hợp số lỗi theo mã:

| `lr_swap` | `vis_flag` | `position` | `missing` | `extra` | `ok` |
| --------: | ---------: | ---------: | --------: | ------: | ---: |
|         1 |          2 |          0 |         1 |       0 |    4 |

Điểm còn phân vân giữa `v = 1` và `v = 0` (ghi cả hai lựa chọn và lý do chọn):

- `000000163118.jpg` #2, `left_wrist`: chọn `v = 1`, phân vân `v = 0`. Người quay lưng đang nhảy, tay trái bị thân che nhưng vị trí vẫn suy ra được từ vai và khuỷu, nên đặt `v = 1`. Nếu ba người ước lượng cho ba vị trí tản mát thì phải là `v = 0`.
- `000000163118.jpg` #1, `left_eye` (`v = 1`) và `left_ear` (`v = 0`): hai điểm cùng bên đầu mà cờ khác nhau. Mắt trái còn đoán được từ mắt phải và sống mũi nên `v = 1`; tai trái bị tóc và đầu che hoàn toàn, không có viền nào thấy được nên `v = 0`.
- `000000393909.jpg` #2, `left_elbow`: chọn `v = 1`, phân vân `v = 0`. Khuỷu bị vật khác che nhưng cánh tay trên và cẳng tay hai bên còn thấy, đủ để ước lượng chắc.
- `000000019754.jpg`, `left_ankle`: chọn `v = 1`, phân vân `v = 0`. Cổ chân bị ván che, nhưng ống chân còn thấy nên vẫn đoán được.
- `000000542160.jpg` #1, `right_elbow` (`v = 0`) và `right_wrist` (`v = 1`): xem dòng tương ứng ở mục 3. Đây là cặp tôi chưa chắc nhất; nếu không giải thích được vì sao cổ tay đoán được mà khuỷu thì không, nên đặt cả hai cùng cờ.

## 5. Kiểm file xuất và kết quả tự kiểm (CP4)

Tự kiểm tay trong file JSON:

| File                       | Người / mặt kiểm (ảnh, thứ tự)                         | Số phần tử `keypoints` | Điểm `outside` ra `v = 0`, `occluded` ra `v = 1`?                                            | `num_keypoints` khớp?                              |
| -------------------------- | ------------------------------------------------------ | ---------------------: | -------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| `DuongManhHuy_persons.zip` | `000000163118.jpg`, người #2 (người quay lưng, áo xám) |                     51 | Có: `nose`, `left_eye`, `right_eye` (outside) ra `v = 0`; `left_wrist` (occluded) ra `v = 1` | Có: `num_keypoints` = 14, đếm được 14 điểm `v > 0` |
| `DuongManhHuy_persons.zip` | `000000542160.jpg`, người #1 (người đứng giữa)         |                     51 | Có: `right_elbow` (outside) ra `v = 0`; `right_wrist` (occluded) ra `v = 1`                  | Có: `num_keypoints` = 16, đếm được 16 điểm `v > 0` |
| `DuongManhHuy_faces.zip`   | `face_342273.jpg`, mặt #1                              |                     39 | Có: 8 điểm mắt (occluded) ra `v = 1`; ảnh này không có điểm `v = 0`                          | Có: `num_keypoints` = 13, đếm được 13 điểm `v > 0` |

Sau khi xuất, CVAT ghi `occluded` thành `v = 1` và `outside` thành `v = 0`. Tọa độ của điểm `outside` trong file là tọa độ cũ, không phải `0, 0` (ví dụ `right_elbow` của người #1 ở ảnh `542160` là `261.43, 219.96, 0`; cả 46 điểm `v = 0` của file persons đều mang tọa độ khác `0, 0`).

Hai job đã chuyển sang completed trên CVAT? Có

Dán phần **TÓM TẮT** in ra từ `src/check_submission.py`:

```text
----- TÓM TẮT (dán vào báo cáo mục 5) -----
- File person: DuongManhHuy_persons.zip
  Số ảnh: 12 · số skeleton `person`: 19 · mỗi annotation 51 số
  Skeleton theo ảnh: 000000019754=1, 000000021235=1, 000000057978=1, 000000163118=2, 000000167027=2, 000000194027=1, 000000220045=1, 000000349521=1, 000000359340=2, 000000393909=2, 000000469061=2, 000000542160=3
  Cờ v: v=2 271 (84%) · v=1 6 (2%) · v=0 46 (14%)
  num_keypoints trung bình: 14.6
- File face: DuongManhHuy_faces.zip
  Số ảnh: 8 · số skeleton `face`: 8 · mỗi annotation 39 số
  Skeleton theo ảnh: face_105668=1, face_145751=1, face_219250=1, face_257657=1, face_333134=1, face_342273=1, face_415073=1, face_421231=1
  Cờ v: v=2 96 (92%) · v=1 8 (8%) · v=0 0 (0%)
  num_keypoints trung bình: 13.0
- Ghi chú: 46 điểm v = 0 vẫn mang tọa độ cũ (CVAT 2.x xuất như vậy; chuẩn COCO ghi 0, 0). Không phải lỗi: điểm v = 0 bị bỏ qua khi huấn luyện và khi chấm.
- Số [LỖI]: 0 · số [CẢNH BÁO]: 2
Kết quả: ĐẠT (đọc lại từng cảnh báo ở trên)
```

Các `[CẢNH BÁO]` còn giữ nguyên và lý do:

- [CẢNH BÁO] 000000163118.jpg #1: cặp ankle nằm ngược phía so với các cặp còn lại. Có thể đổi trái/phải một cặp (`lr_swap`), hoặc tay / chân bắt chéo thật: kiểm lại.
- [CẢNH BÁO] 000000542160.jpg #1: cặp knee, ankle nằm ngược phía so với các cặp còn lại. Có thể đổi trái/phải một cặp (`lr_swap`), hoặc tay / chân bắt chéo thật: kiểm lại.

## 6. Ảnh chụp màn hình

1. Label `person` trong label constructor (đủ 17 điểm có tên):

   ![Label person](images/1_label_person.png)

2. Một người quay lưng đã gán, cờ đã đặt:

   ![Người quay lưng](images/2_nguoi_quay_lung.png)

3. Một khuôn mặt đã gán 13 điểm, phóng to vùng mắt:

   ![Khuôn mặt phóng to](images/3_mat_phong_to.png)

4. Một đoạn JSON xuất của một người:

   ![JSON export](images/4_json_export.png)

Nguồn ảnh: COCO val2017 / Wikimedia Commons.

## 7. Tự đánh giá

1. Lỗi bạn mắc nhiều nhất hôm nay, và cách bạn sẽ tránh ở dự án thật:

   Lỗi nhiều nhất là `vis_flag`: ảnh kính trong `face_342273` bị bật `occluded` cho 8 điểm mắt, còn ảnh kính râm `face_219250` lại để `v = 2`, nhiều khả năng do bật nhầm ảnh. Tôi cũng sót một người ở ảnh `220045`. Ở dự án thật tôi sẽ đếm người và ghi loại ảnh trước khi gán, đặt cờ thành một bước riêng, đối chiếu ảnh vẽ lại với loại ảnh.

2. Một quy tắc bạn đề xuất thêm vào guideline (kèm ảnh ví dụ nếu có):

   "Chiều cao 60 px tính từ đầu đến chân của cả người, kể cả phần thân bị che. Người chỉ lộ đầu và chân vẫn phải gán; khớp bị che thì `occluded` nếu đoán chắc, không thì `outside`." Ví dụ: ảnh `000000220045`.

3. Nhãn của bạn đi vào model ở đâu? Chọn một lỗi bạn đã sửa và nói nó sẽ làm model học sai thế nào (heatmap, trọng số trong loss, hay bước lật ảnh):

   Nhãn `v > 0` thành heatmap đích, còn `v = 0` không vào loss. Ở lỗi `missing` của ảnh `220045`, người thứ hai không có nhãn nên heatmap đích ở đó bằng 0: model bị phạt khi đoán đúng và học coi người bị che là nền.

## 8. Khó khăn và lỗi kỹ thuật

- **Phân định người dưới 60 px:** ảnh `000000167027` có khoảng 5 người ngồi trong quán ở xa (cao khoảng 30 px) nên không gán, còn hai người đi bộ bên trái (cao 66 và 68 px) sát ngưỡng nên phải đo lại trước khi quyết định gán.
- **Người bị che một phần:** ảnh `000000220045` có người phía sau chỉ lộ mặt và chân, khó xác định có đủ 60 px để gán hay không.
- **Chân chồng lên nhau:** ở ảnh `000000542160` (ba người đứng sát nhau) và `000000163118` (người đang nhảy), script cảnh báo cặp gối và cổ chân ngược phía, nên phải xét lại là đổi trái/phải hay chân bắt chéo thật.

## Bonus (nếu làm)

### B1 — 3 ảnh người khó thêm

| Ảnh | Số skeleton | Số người dưới 60 px | Ca khó nhất |
| --- | ----------: | ------------------: | ----------- |
|     |             |                     |             |
|     |             |                     |             |
|     |             |                     |             |

Kết quả `src/check_submission.py` cho `HoVaTen_bonus_persons.zip`:

```text
(dán ở đây)
```

### B2 — 5 ảnh khuôn mặt đen trắng / hồng ngoại

Ảnh không màu khó hơn ở điểm nào? Kính râm trên ảnh hồng ngoại có còn là `v = 1` không?

...

### B3 — Model so với nhãn của tôi (không tính điểm)

- Demo đã dùng:
- Ảnh đã thử:

1. Model có trả điểm cho khớp bị che, hay khớp ngoài ảnh không? Confidence ở các điểm đó thế nào?

   ...

2. Với người quay lưng, model có đổi trái/phải không?

   ...

3. Bộ điểm của model có giống 17 điểm COCO không?

   ...

## 9. Tài liệu tham khảo và công cụ AI đã dùng

...
