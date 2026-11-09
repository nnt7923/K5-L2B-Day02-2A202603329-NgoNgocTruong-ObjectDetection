# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` |Xe con chở người, gồm sedan, hatchback, SUV và MPV gia đình |Thân xe kiểu xe con. Không chuyển SUV thành van chỉ vì xe lớn hoặc có 7 chỗ.|
| `truck` |Xe tải có cabin và khu vực chở hàng riêng biệt; trong quy ước này bao gồm xe bán tải.|Xe tải thùng, xe ben, đầu kéo, pickup. Pickup có thùng hàng phía sau nên xếp truck. |
| `bus` |Xe có thân dạng xe buýt/xe khách, khoang hành khách dài với nhiều hàng ghế. |Xe buýt đô thị, xe khách, minibus có cấu trúc xe buýt. Không chỉ dựa vào kích thước trong ảnh.|
| `van` |Xe có thân dạng hộp, mui cao, khoang chở người hoặc hàng liền với cabin.|Van giao hàng, van chở khách dạng Transit. Phân biệt với xe tải bằng khoang liền thân; với MPV bằng kiểu thân van.|

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Trong bộ quy ước này, vẽ hình chữ nhật nhỏ nhất bao hết phần xe nhìn thấy, gồm bánh và gương nếu nhận diện rõ. Không bao bóng đổ hoặc chừa khoảng trống không cần thiết. Không tự mở rộng box để đoán phần bị che. Nếu dự án yêu cầu box bao cả phần khuất (amodal), phải áp dụng quy tắc đó thống nhất.
- Vật thể bị che khuất: Vẫn gán nhãn khi đủ căn cứ xác định lớp; bật Occluded khi vật khác che một phần xe, kể cả một bánh. Không bật chỉ vì ảnh mờ hoặc vì góc nhìn không cho thấy bánh phía bên kia. Trong CVAT, có thể bật thuộc tính này bằng Q; box sẽ có viền nét đứt
- Vật thể bị cắt bởi rìa ảnh: Vẽ box cho phần còn trong ảnh, dừng tại rìa ảnh. Theo quy ước đề xuất, ghi truncated = true nếu schema có thuộc tính này; không bật Occluded chỉ vì xe bị cắt ở rìa. Nếu đồng thời bị vật khác che thì bật thêm Occluded
- Vật thể rất nhỏ/mơ hồ: Vẫn gán nhãn nếu xác định được lớp và vị trí box đáng tin cậy. Nếu không phân biệt được car, van, truck hay bus, đưa vào danh sách cần kiểm tra, không đoán lớp. Chỉ bỏ qua theo tiêu chí đã thống nhất của dự án

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_005` |Đuôi xe con nằm ngoài rìa ảnh, phần còn lại không bị che.|car |Phân biệt bị cắt bởi biên ảnh với bị vật khác che.|
| `drive_007` |Đầu xe con bị che bởi xe con đằng trước và phần đằng sau bị che 1 ít bởi cột biển. |car |Phân biệt bị che bởi cột biển và xe phía trước |
| `drive_011` |Xe đen bên cạnh xe taxi vàng và 2 xe con nhìn từ nghiêng dễ nhầm với xe van |car |Phân biệt với car bán tải |
