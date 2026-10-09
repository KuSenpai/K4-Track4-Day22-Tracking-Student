# Báo cáo lab: chọn tracker cho 5 video

**Nhóm:** TSV **Thành viên:** Ngô Gia Quốc

Detector cố định: `yolo26n.pt`, ảnh 640 px, Re-ID `osnet_x0_25_msmt17`. Không đổi các mục này trong bài nộp chính.

## 1. Cấu hình đã chọn

Mỗi video: tracker bạn nộp, `conf`, `iou`, điều bạn **nhìn thấy** trên video, và một cấu hình đã thử rồi loại.

Với `video_2`–`video_5` không có nhãn. Các con số "ID", "đứt", "track ngắn" bên dưới đếm từ file kết quả (số ID khác nhau xuất hiện, số lần một ID mất rồi xuất hiện lại, tỉ lệ track dưới 15 frame). Đây chỉ là chỉ báo gián tiếp, không phải độ chính xác: ít ID hơn có thể do tracker giữ tốt hơn, nhưng cũng có thể do bỏ sót người.

| Video | Tracker | conf | iou | Quan sát khi xem video | Đã thử nhưng loại |
|---|---|---|---|---|---|
| video_1 (quảng trường, tĩnh, ban ngày) | botsort | 0.15 | 0.5 | Người ở gần được bám tốt. Người nhỏ ở xa phần lớn không có hộp (detector 640 px trên ảnh 1920×1080), nên chỉ bắt được khoảng 24% số người có nhãn. Theo nhãn có 27 lần đổi ID trong 600 frame. | bytetrack 0.3/0.5: HOTA 26.9, chỉ 5.9 hộp/frame, bỏ sót nhiều hơn. strongsort 0.3/0.5: HOTA 28.7 nhưng 41 lần đổi ID, 79 ID. botsort `conf` 0.5: HOTA 27.2, MOTA 15.3 vì bỏ sót nhiều. |
| video_2 (phố đêm, tĩnh, rất đông) | bytetrack | 0.15 | 0.5 | Người đứng hoặc đi riêng lẻ ở giữa và dưới khung giữ cùng một ID từ khung 300 đến 700 (ví dụ ID 3 và ID 4). Đám đông nhỏ ở phía trên cùng không có hộp. File kết quả có 43 ID, track trung vị 189 frame, chỉ 13 lần đứt. | botsort 0.3/0.5: 67 ID, 172 lần đứt. ocsort 0.3/0.5: 77 ID, 336 lần đứt. bytetrack `conf` 0.5: 282 lần đứt, ít hộp hơn (8.0 so với 10.1 hộp/frame). |
| video_3 (camera di động, ảnh nhỏ) | bytetrack | 0.15 | 0.5 | Ảnh 640×480, người rất to và bị cắt ở mép khung. ID đổi liên tục: ở khung 700 chỉ có vài người trong ảnh nhưng ID đã ở mức hàng trăm. 130 ID, 51% track ngắn hơn 15 frame. Đây là video khó nhất, cả hai tracker đều kém. | botsort 0.3/0.5: 167 ID, 56% track ngắn. ocsort 0.3/0.5: 137 ID, 232 lần đứt. bytetrack `conf` 0.5: 121 lần đứt so với 66. |
| video_4 (trong nhà, camera di chuyển) | bytetrack | 0.15 | 0.5 | Người đi thẳng, rõ nét được bám ổn định. Người ở gần camera có hộp rất lớn. 56 ID, track trung vị 90 frame, 16 lần đứt. | botsort 0.3/0.5: 73 ID, track trung vị 45 frame. ocsort 0.3/0.5: 79 ID, 122 lần đứt. |
| video_5 (trên xe bus, giao lộ đông) | bytetrack | 0.15 | 0.5 | Người rất nhỏ (hộp cao trung vị khoảng 140 px trên ảnh 1080p), chỉ khoảng 2.9 hộp/frame. Đám người ở giao lộ phía xa không có hộp nào. 61 ID, 39% track ngắn hơn 15 frame, chỉ 3 lần đứt. | botsort 0.3/0.5: nhiều hộp hơn (4.1 hộp/frame) nhưng 77 ID, 135 lần đứt. ocsort 0.3/0.5: 99 ID, 205 lần đứt. bytetrack `conf` 0.5: 52% track ngắn, chỉ 2.2 hộp/frame. |

Ghi chú về các tham số khác: đổi `iou` (0.4 / 0.5 / 0.7) gần như không đổi kết quả của ByteTrack ở `video_2`–`video_5` (ví dụ `video_2` giống hệt nhau giữa `iou` 0.5 và 0.7). `conf` ảnh hưởng lớn hơn.

## 2. Số liệu video_1

Bảng do `scripts/evaluate_practice.py` in ra cho bản nộp (`botsort`, `conf` 0.15, `iou` 0.5, đủ 600 frame):

```
HOTA   : HOTA 29.343  DetA 19.236  AssA 45.113  DetRe 19.959  DetPr 75.856  AssRe 48.123  AssPr 81.458  LocA 82.806
CLEAR  : MOTA 20.731  MOTP 80.377  MODA 20.876  CLR_Re 23.594  CLR_Pr 89.671  MT 8  PT 13  ML 41  IDSW 27  Frag 72
         CLR_TP 4384  CLR_FN 14197  CLR_FP 505
Identity: IDF1 29.561  IDR 18.67  IDP 70.955  IDTP 3469  IDFN 15112  IDFP 1420
Count  : Dets 4889  GT_Dets 18581  IDs 55  GT_IDs 62
```

Các cấu hình đã thử trên `video_1` (đủ 600 frame, cùng cách chấm):

| Tracker | conf | iou | HOTA | DetA | AssA | MOTA | IDF1 | Đổi ID |
|---|---|---|---|---|---|---|---|---|
| bytetrack | 0.3 | 0.5 | 26.9 | 15.1 | 48.1 | 17.3 | 25.7 | 12 |
| ocsort | 0.3 | 0.5 | 27.5 | 17.9 | 42.4 | 19.8 | 28.7 | 42 |
| deepocsort | 0.3 | 0.5 | 27.4 | 17.8 | 42.2 | 19.8 | 27.8 | 51 |
| strongsort | 0.3 | 0.5 | 28.7 | 17.7 | 46.6 | 19.7 | 29.9 | 41 |
| botsort | 0.3 | 0.5 | 29.5 | 18.1 | 48.2 | 19.8 | 29.4 | 25 |
| bytetrack | 0.1 | 0.5 | 26.4 | 15.9 | 43.9 | 18.2 | 26.2 | 13 |
| bytetrack | 0.15 | 0.5 | 27.3 | 15.9 | 47.1 | 18.3 | 27.0 | 13 |
| bytetrack | 0.5 | 0.5 | 25.3 | 13.9 | 45.8 | 15.9 | 23.4 | 14 |
| botsort | 0.1 | 0.5 | 29.5 | 19.6 | 45.0 | 21.0 | 30.3 | 23 |
| **botsort (nộp)** | **0.15** | **0.5** | **29.3** | **19.2** | **45.1** | **20.7** | **29.6** | **27** |
| botsort | 0.5 | 0.5 | 27.2 | 14.3 | 51.6 | 15.3 | 24.6 | 10 |
| botsort | 0.15 | 0.4 | 29.1 | 18.5 | 46.0 | 20.5 | 30.0 | 23 |
| botsort | 0.15 | 0.7 | 29.7 | 19.4 | 45.6 | 20.3 | 29.9 | 39 |

Các cấu hình `botsort` với `conf` 0.1–0.3 và `iou` 0.4–0.7 chênh nhau dưới khoảng 0.6 HOTA, nên khó nói cái nào thật sự hơn. Chọn `conf` 0.15, `iou` 0.5 vì nằm trong lưới đề bài gợi ý, có HOTA gần mức cao nhất và ít lần đổi ID hơn `iou` 0.7.

`video_2` đến `video_5` không có nhãn trong gói lab. Không điền số cho các video đó.

## 3. Phân tích

**video_1 (tĩnh, ban ngày, mật độ vừa).** DetA chỉ khoảng 19%, trong khi DetPr là 76%. Nghĩa là hộp có ra thì đúng người, nhưng detector bỏ sót phần lớn người nhỏ ở xa, và đây là trần của mọi tracker vì detector bị khóa ở 640 px. Giữa các tracker, `botsort` cho HOTA cao nhất (29.3 đến 29.5) và nhiều hộp hơn `bytetrack` (7.6 so với 5.9 hộp/frame ở `conf` 0.3), nên DetA cao hơn. Đổi lại `bytetrack` ít đổi ID hơn (12 lần so với 25) vì chỉ nối hộp theo chuyển động. Hạ `conf` xuống 0.1–0.15 giúp thêm người nhưng chỉ cải thiện nhẹ, còn `conf` 0.5 làm DetA tụt rõ (xuống khoảng 14%) vì bỏ sót.

**video_2 (phố đêm, camera tĩnh, rất đông).** Chọn `bytetrack`. Trên video, người ở giữa và dưới khung giữ cùng ID suốt hàng trăm frame. Ở cùng `conf` 0.3, `bytetrack` có 47 ID và 28 lần đứt, so với 67 ID và 172 lần đứt của `botsort`, 77 ID và 336 lần đứt của `ocsort`. Hạ `conf` xuống 0.15 giảm tiếp còn 43 ID và 13 lần đứt. Camera đứng yên nên chuyển động của mỗi người dễ đoán, ByteTrack nối hộp theo IoU là đủ. Re-ID của `botsort` ở đây không mang lại lợi ích đo được. Tôi đoán ban đêm màu áo bị ánh đèn làm lệch và nhiều người mặc đồ tối giống nhau, nên đặc trưng ngoại hình kém tin cậy, nhưng chưa kiểm chứng. Hạn chế thấy rõ: đám đông nhỏ ở phía trên khung không có hộp nào, tức tracker không giúp gì khi detector không thấy người.

**video_3 (camera di động, ảnh nhỏ, ít fps).** Đây là video tracker thất bại nhiều nhất: 130 ID cho một cảnh luôn chỉ có vài người, nửa số track ngắn hơn 15 frame. Nhiều khả năng camera di chuyển và fps thấp làm người dịch chuyển xa giữa hai frame nên IoU giữa hộp cũ và hộp mới thấp, ID bị cấp mới; người lại rất to và bị cắt ở mép khung nên kích thước hộp thay đổi mạnh. Tôi chưa kiểm chứng nguyên nhân này. Ở cùng `conf` 0.3, `bytetrack` ít tệ hơn `botsort` (127 so với 167 ID) và `ocsort` (137 ID, 232 lần đứt so với 77), có thể vì `ocsort` phụ thuộc mô hình chuyển động mà chuyển động ở đây không ổn định. Tôi chưa thử bù chuyển động camera riêng hoặc tracker khác, nên chưa thể nói tracker nào xử lý được cảnh này.

**video_5 (trên xe bus, rung lắc).** Người nhỏ và ít, chỉ khoảng 3 hộp/frame ở `conf` 0.15. `botsort` ra nhiều hộp hơn (4.1 hộp/frame ở `conf` 0.3) nhưng cũng đứt nhiều hơn (135 so với 10 lần ở cùng `conf`), nên tôi chọn `bytetrack` ở `conf` 0.15 vì ít đứt và ID ổn định hơn. Điều tôi chưa kiểm chứng được là `botsort` có bắt thêm người thật hay chỉ thêm hộp giả, vì không có nhãn.

## 4. Nếu có thêm thời gian

Lưu kết quả detector ra file một lần rồi đưa vào tracker để quét `conf` mịn hơn mà không phải chạy lại detector (mỗi lần chạy hiện tốn 1–8 phút trên CPU). Thử bù chuyển động camera cho `video_3` và `video_5`, và xem từng frame gây đổi ID để biết lỗi do detector hay do tracker. Thử thêm `strongsort` và `deepocsort` trên bốn video không nhãn, vì tôi mới so sánh chúng ở `video_1`.
