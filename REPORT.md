# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Nguyễn Lê Thế AnhAnh <br>
**MSSV:** 2A202602164 <br>
**Hình thức:** Cặp <br>
**Mã cặp:** T01

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp:  f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh:drive_008.jpg  ;drive_022.jpg  ;drive_033.jpg   ;drive_038.jpg
- Số vật thể thực tế: 62
- Mã SHA-256 của gói YOLO của bạn: 8a8ebdeb0830fe8f4a226e847ce85bc61fa2f3869ddf179de64e7868ea72ba2a
- Mã SHA-256 của gói CVAT gốc của bạn:  596e4f1e1825ccf7d34c14a2dfff91d5a630ab314e123cb06d9d5699effce952 
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp:
- Mã SHA-256 của gói đối chiếu: a7be26e907908d89edcf265d7d636e6359b488992f9ac1edef20e72d80a7433a
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu:

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:
Toàn bộ quá trình dán nhãn (vẽ bounding box) và gán các thuộc tính bắt buộc (visibility, boundary, review_state) cho 4 ảnh trong CVAT đều do cá nhân tự thực hiện độc lập, dựa trên quy chuẩn hướng dẫn của bài lab và quan sát thực tế từ hình ảnh thô. Cá nhân hoàn toàn không xem, tham khảo hay sao chép bất kỳ file nhãn, tọa độ hoặc báo cáo nào từ bạn cùng cặp hay bộ tham chiếu trước khi hoàn tất bước xuất dữ liệu và đưa vào tính toán IoU đối chiếu.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_008.jpg | car | tỉ lệ chiều dài, độ cao thân xe với các ô tô | Xe chở người cỡ lớn, có cửa kính dài hai bên thân được phân loại thống nhất là `bus` |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:
Ví dụ phân biệt:
- **Lớp (Class):** Là bản chất/danh tính của đối tượng (Ví dụ: `car` - chiếc ô tô con). Lớp dùng để trả lời cho câu hỏi: *"Đây là vật thể gì?"*.
- **Thuộc tính (Attribute):** Là trạng thái/đặc điểm quan sát được của đối tượng đó trong một ngữ cảnh/bức ảnh cụ thể (Ví dụ: `visibility` = `occluded` - bị xe khác che mất một phần thân, `boundary` = `inside` - nằm hoàn toàn trong khung hình). Thuộc tính dùng để trả lời cho câu hỏi: *"Vật thể đó đang ở trạng thái như thế nào?"*.

 Sự khác biệt cốt lõi: Lớp `car` của chiếc xe là cố định không đổi, nhưng thuộc tính (`visibility`, `boundary`) sẽ thay đổi tùy theo góc chụp, khoảng cách hoặc chướng ngại vật trong từng bức ảnh.
 

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Chọn nhãn `car` cho chiếc xe khách/xe cỡ lớn ở ảnh `drive_008.jpg` | Lớp (Class) | So sánh tỉ lệ chiều dài, độ cao thân xe với các ô tô con xung quanh và đối chiếu với ảnh tham chiếu | Đổi nhãn từ `car` sang `bus`. Quy tắc: Xe chở người cỡ lớn, có cửa kính dài hai bên thân được phân loại thống nhất là `bus`. |

- Số hộp `needs_review` trước và sau khi kiểm: Trước khi kiểm: 2 | Sau khi kiểm: 0
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: 
  Trường hợp một vật thể ở góc xa ảnh `drive_001.jpg` bị mờ nặng và bị mép đường che một phần, không rõ là `car` hay `van`. Cách xử lý: Đánh dấu thuộc tính `review_state` = `needs_review`, gán `visibility` = `unclear`, sau đó gửi hình ảnh cắt (crop) kèm tọa độ vàvà xin hỗ trợ  từ các Lab Coach.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `0 0.531250 0.671296 0.083333 0.111111`
- Tên lớp và tọa độ điểm ảnh `xyxy`: Lớp `car` | Tọa độ pixel `[940, 665, 1100, 785]` 
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?
  Một dòng YOLO đúng định dạng cú pháp chỉ đảm bảo tính đúng đắn về mặt **kỹ thuật/cấu trúc**. Nó vẫn có thể sai về mặt **ngữ nghĩa dữ liệu** do người dán nhãn:
  1. **Sai lớp (Class):** Gán sai nhãn thực tế của vật thể như car nhưng gán van 
  2. **Sai phạm vi (Coverage):** Vẽ khung quá rộng  hoặc quá hẹp 
  3. **Sai hình học (Geometry):** Khung bị lệch tâm

## 5. Huấn luyện và dự đoán thử

Ba mã ảnh huấn luyện: drive_022.jpg, drive_033.jpg, drive_038.jpg

Mã ảnh thẩm định: drive_008.jpg

Mô tả một dự đoán trong detect_result.jpg: Bắt đúng 1 xe car ở giữa đường (conf 0.75), nhưng sót 1 xe truck ở xa góc trái.

Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Cần rà lại quy định dán nhãn xe ở xa/mờ và bổ sung thêm ảnh truck vào tập train.

Minh chứng nào có thể bác bỏ nhận định của bạn? Ảnh phóng to vùng bị sót vẫn thấy rõ cabin và gầm xe bằng mắt thường, chứng tỏ do mô hình chưa học đủ dữ liệu chứ không phải do xe quá mờ.

Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? Tập 4 ảnh quá nhỏ, chỉ dùng để test xem code/pipeline có chạy thông hay không chứ không đánh giá được độ chính xác thực tế của AI.


## 6. Đối chiếu nhãn

- Số hộp ghép được: 61
- IoU trung bình và trung vị: IoU trung bình: 0.8686 (86.86%) | IoU trung vị: 0.8807 (88.07%)
- Mức đồng thuận lớp: 96.72% (0.967213)
- Số hộp phía bạn không ghép được: 1
- Số hộp phía đối chiếu không ghép được: 14
- Một điểm khác biệt cụ thể: Phía bạn làm dán nhãn kỹ hơn các xe nhỏ/mờ ở xa nên thừa 1 hộp không ghép được với đối chiếu, còn phía đối chiếu bị thừa 14 hộp (do vẽ thừa hoặc dán sót ở các khu vực mờ).
- Quy tắc hoặc hành động sửa phát sinh: Thống nhất lại ngưỡng dán nhãn cho các vật thể mờ ở xa (`visibility` = `unclear`) để tránh việc người vẽ quá kỹ, người lại bỏ sót.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? Đồng thuận cao (96.72%) chỉ thể hiện hai người dán nhãn có góc nhìn và cách hiểu quy tắc giống nhau. Hai người vẫn có thể cùng hiểu sai quy tắc chung hoặc cùng bỏ sót một lỗi giống nhau, nên đồng thuận không đồng nghĩa với nhãn đúng tuyệt đối (ground truth).

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

- **Minh chứng mạnh nhất trong bài:** Báo cáo đối chiếu IoU (`comparison_summary.json`) với mức đồng thuận lớp đạt 96.72% và IoU trung vị đạt 0.8808, chứng minh quy trình kiểm thử đường đi dữ liệu (Data Pipeline) từ CVAT sang YOLO và đối chiếu định lượng đã chạy trôi chảy, đúng kỹ thuật.
- **Câu hỏi còn lại cho Lab Coach:** Khi đối chiếu nhãn phát hiện lệch 14 hộp từ phía đối chiếu (do dán thừa hoặc khác ngưỡng nhận diện ở xa), trong dự án thực tế quy trình phân xử (adjudication) sẽ ưu tiên lọc bỏ các hộp có IoU thấp hay bắt buộc cả 2 bên dán lại từ đầu?