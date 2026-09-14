# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Nguyễn Lê Thế Anh <br>
**MSSV:** 2A202602164<br>
**Hình thức:** cặp<br>
**Mã cặp:** T01T01

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

Tình huống A — xe buýt hay xe van?
Ảnh và mã vật thể: drive_008.jpg / xe ở dải đường giữa

Dấu hiệu nhìn thấy: Thân xe khá dài nhưng phom tròn, kích thước trung bình, có cửa kính bên thân.

Quy tắc áp dụng: Xe chở người cỡ lớn/dài chọn bus; xe thân hộp nhỏ, ngắn chọn van.

Quyết định: Chọn lớp bus (xe khách/xe buýt nhỏ).

Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu review_state = needs_review và hỏi bạn cùng cặp (hoặc Lab Coach) để thống nhất ngưỡng kích thước giữa bus và van.

Tình huống B — xe tải hay xe van/ô tô con?
Ảnh và mã vật thể: drive_038.jpg / xe sát lề phải

Dấu hiệu nhìn thấy: Phần cabin vuông, phía sau là thùng hàng kín liền khối với cabin.

Quy tắc áp dụng: Khoang hàng tách biệt/có ben chọn truck; thùng kín liền một khối nhỏ chọn van.

Quyết định: Chọn lớp van.

Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Crop hình ảnh phóng to 200% để soi ranh giới giữa cabin và thùng xe; nếu vẫn mờ thì giữ nhãn van và tích needs_review.

Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?
Ảnh và mã vật thể: drive_033.jpg / xe ở góc dưới cùng bên trái

Dấu hiệu nhìn thấy khi phóng 100%: Chỉ thấy được đầu xe và một phần kính lái, phần đuôi xe bị dải phân cách che và thân xe chạm sát mép trái ảnh.

Giá trị visibility: occluded (bị che một phần)

Giá trị boundary: truncated (bị mép ảnh cắt)

Trạng thái review_state: confident

Lý do: Đủ bằng chứng nhận diện phần đầu xe là ô tô con (car), chỉ vẽ sát phần nhìn thấy và đánh dấu thuộc tính bị che/cắt đúng quy định chứ không bỏ qua.

6. Xác nhận tự kiểm tra
[x] Đã rà đủ bốn ảnh.

[x] Đã kiểm vật thể thiếu và trùng.

[x] Đã kiểm lớp và hình học từng hộp.

[x] Mỗi hộp có đủ ba thuộc tính.

[x] Đã xử lý mọi hộp needs_review.

[x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.

[x] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.

[x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.

Số vật thể thực tế: 62 (khớp chính xác với tổng số object_count trong file JSON xuất từ CVAT của bạn).