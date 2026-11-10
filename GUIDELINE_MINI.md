# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Ô tô con chở người cỡ nhỏ: sedan, hatchback, SUV/crossover, taxi và xe bán tải cabin nhỏ. | Thân thấp hoặc vừa, mui không cao vượt trội, có phần đầu xe và cốp tách rõ. Taxi (sơn cam/vàng/đỏ) vẫn là `car`.|
| `truck` | Xe tải chở hàng và xe đầu kéo: cabin riêng, thùng hàng hoặc khoang chứa hàng lớn tách khỏi cabin. | Thùng hàng dài/cao, kích thước vượt hẳn xe con. Khác `van` ở chỗ cabin và thùng hàng tách rời. |
| `bus` | Xe buýt và minibus chở khách, kể cả xe buýt hai tầng và xe buýt khớp nối. | Thân rất dài, nhiều cửa sổ dọc hai bên. Khác `van` ở chiều dài và số hàng cửa sổ. |
| `van` | Xe van và minivan chở hàng hoặc chở người, thân liền khối dạng hộp, mui cao. | Cabin và khoang sau liền nhau, kích thước giữa `car` và `bus`. Khác `car` ở dáng hộp, mui cao; khác `bus` ở kích thước nhỏ. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: mép box chạm sát phần thân xe nhìn thấy (đầu, đuôi, hai bên, nóc), không lấy thêm mặt đường, bóng đổ, vệt phản chiếu trên mặt đường ướt hay xe bên cạnh. Mỗi xe một box riêng, kể cả cùng lớp.
- Vật thể bị che khuất: vẫn vẽ box quanh phần nhìn thấy nếu từ phần đó tôi nhận ra được là xe và xác định được lớp. Hai xe chồng lên nhau thì vẽ hai box riêng, mỗi box trên phần nhìn thấy của xe đó, hai box được phép chồng nhau. Xe bị cột đèn hoặc vật nhỏ che ngang thân vẫn vẽ một box trọn quanh xe.
- Vật thể bị cắt bởi rìa ảnh: box kéo đến đúng rìa ảnh, không đoán phần nằm ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: nếu không tự chỉ ra được đó là xe và thuộc lớp nào thì không vẽ box, không đoán, ghi lại vào bảng dưới. Trong hàng xe xa, nhỏ, chồng sát nhau, chỉ vẽ những xe tách được thành box riêng; phần còn lại chỉ là mảng màu thì bỏ.
- Xe bị nhòe do chuyển động nhanh: vẽ box theo thân xe rõ nhất, đặt mép box ở chỗ thân xe còn phân biệt được với nền, không kéo ra hết vệt mờ phía sau.
- Xe có hàng hóa xếp cao: xem ca số 2 bên dưới.
- Xe khớp nối (xe buýt gồm hai thân nối nhau): coi là một xe, một box.
- Không gán: người, xe đạp, xe máy, xe ba bánh chở hàng, biển báo, đèn giao thông, phản chiếu, hình xe trên bảng quảng cáo.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001` | Xe trắng nhỏ, dáng hộp, mui cao đỗ ở phía trái, khó phân biệt `car` hay `van`. | Gán `van`. | Thân dạng hộp, mui cao, cabin và khoang sau liền khối, đúng mô tả xe van/minivan của schema, không giống sedan có cốp tách rời. Ca này còn phân vân giữa `car` và `van`, cần giảng viên xác nhận. |
| `drive_042` | Xe tải chở hàng bao xếp cao hơn nhiều so với thùng xe, không biết có đưa hàng vào box hay không. | Box chỉ ôm thân xe tải (cabin và thùng xe), không gồm khối hàng xếp nhô cao phía trên, lớp `truck`. | Schema và quy tắc box yêu cầu bám sát phần thân xe; hàng hóa là vật chở, không phải thân xe, và hình dạng khối hàng thay đổi theo từng chuyến. Guideline gốc không nói rõ về hàng hóa nên đây là quyết định của riêng tôi, cần giảng viên xác nhận. |
| `drive_030` | Hàng xe xếp hàng trong mưa, xe sát nhau, nhiều xe bị xe phía trước che một phần, xe xa chỉ còn mảng nhỏ. | Vẽ mỗi xe tách được thành một box trên phần nhìn thấy; xe xa chỉ còn mảng nhỏ không xác định lớp thì bỏ. | Theo quy tắc: che một phần vẫn gán nếu nhận diện được lớp; mảnh nhỏ không xác định chắc thì không tự đoán. Không gộp nhiều xe vào một box. |
| `drive_020`, `drive_001` | Xe chạy nhanh nên nhòe (xe trắng và xe đen ở `drive_020`, xe bạc ở `drive_001`), biên xe không rõ. | Vẽ box theo thân xe rõ nhất, không kéo ra hết vệt nhòe. | Box phải bám phần vật thể nhìn thấy; vệt nhòe không phải thân xe. Lớp vẫn xác định được từ hình dáng nên vẫn gán. |
| `drive_041` | Xe buýt khớp nối gồm hai thân nối nhau. | Một box, lớp `bus`. | Đây là một phương tiện duy nhất, định nghĩa `bus` gồm xe buýt chở khách. |
| `drive_041` | Xe trắng có đèn trên mui (dạng xe cứu thương/xe dịch vụ), dáng hộp. | Gán `van`. | Dáng hộp liền khối, kích thước nhỏ, mui cao, không có thùng hàng tách rời nên không phải `truck`. |
