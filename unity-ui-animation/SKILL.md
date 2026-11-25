---
name: unity-ui-animation
description: Khảo sát UI Unity, nhận bàn giao màn từ unity-ui, đề xuất và ghép animation mở, đóng, chuyển tab, chọn item và hiệu ứng nhấn cho popup, HUD và màn kết quả.
---

# Unity UI Animation

## Tiếp nhận và setup project

Khi được gọi để mở luồng Animation, xác định project hiện tại. Nếu có unity-ui, ưu tiên tìm phiên Animation của skill đó bằng danh sách chat và thông tin bàn giao. Xác minh đường dẫn project và nội dung chat trước khi chọn. Dùng lại phiên phù hợp, kể cả khi người dùng gọi từ một chat khác. Chỉ tạo phiên Animation khi chưa có phiên phù hợp; có thể đổi tên chat hiện tại nếu người dùng chọn cách đó. Khi chuyển chat, chuyển cả yêu cầu và tham chiếu màn, tránh xử lý hai lần. Nếu môi trường thiếu công cụ quản lý chat, tiếp tục trong chat hiện tại và nói rõ.

Đọc một luồng UI thật từ nút mở, bind dữ liệu, show, hide đến lúc đóng hoặc trả về pool. Báo ngắn tên file, class và hàm có thể gắn show/hide/loop. Kiểm tra cách project xử lý input, time scale, backdrop và tween đang chạy.

Ở lần setup đầu, đọc cả gameplay và UI. Báo cho người dùng **style nhận diện, dấu hiệu, nhịp chuyển động và hiệu ứng nền đề xuất**. Hỏi họ chỉnh hoặc chốt trước khi áp dụng. Giữ quyết định trong phiên Animation cho các màn tiếp theo.

- Hiện thực: ưu tiên fade, quãng trượt ngắn và giảm tốc tự nhiên.
- Cartoon: có thể lấy đà, vượt đích và nảy lại; điều chỉnh biên độ theo nhịp chơi.
- Pixel: ưu tiên cạnh sắc, có thể dùng pixel reveal hoặc lóe viền. Chữ xáo ngẫu nhiên tùy nội dung và style.
- Dim là mặc định đề xuất. Với pixel hoặc blur, tận dụng backdrop có sẵn. Trên mobile ưu tiên render nền ở độ phân giải thấp; nếu cảnh đứng yên, tái sử dụng ảnh chụp. UI có thể bắt đầu hiện khi pixel hóa đạt khoảng một nửa đến hai phần ba.

Báo phiên đã sẵn sàng nhận màn. Khi phối hợp cùng unity-ui, chuyển màn sau khi ghép UI xong, hoặc sau khi nối xong logic nếu có bước đó. Gói bàn giao gồm project, prefab/script, điểm mở/đóng, sự kiện đã nối và ý đồ hiệu ứng. Nếu công cụ gửi chat khả dụng, gửi vào phiên đã xác minh; chỉ xác nhận bàn giao khi gửi thành công. Skill này cũng nhận yêu cầu trực tiếp.

## Đọc màn và đề xuất

Báo đã nhận tên màn. Đọc hierarchy, prefab và script; đối chiếu Game View hoặc ảnh tham chiếu để nhận diện hình dạng thực. Với content trống ở prefab, kiểm tra nơi sinh và bố trí item lúc chạy. Nhận grid qua các hàng/cột nhìn thấy, kể cả khi đặt tay.

Trước khi ghép, nêu theo từng vùng thật của màn: tên cụm, sự kiện kích hoạt, hướng chuyển động, nhịp bắt đầu và điểm nhấn. Nếu có yêu cầu chờ hoặc duyệt, dừng đúng mốc người dùng chỉ định. Với cảnh đặc biệt có diễn biến riêng, lấy mô tả, ảnh hoặc video tham chiếu từ người dùng. AI tự chọn nhịp trang trí và chi tiết chuyển động theo ý đồ đã có.

- Cụm phụ có thể trượt từ hướng neo kèm fade. Nội dung chính có nhịp riêng theo ý nghĩa, chẳng hạn tiêu đề dậm đúng lúc xác nhận chiến thắng.
- Grid dày có thể hiện so le theo hàng; danh sách dọc theo thứ tự đọc; dải ngang theo chiều đọc. Một biến thể cho thẻ nổi bật là scale 0.5, lóe sáng, bật lên 1.1 rồi hạ về 1 đồng thời lớp sáng tan và hình thật hiện rõ.
- Icon và thông tin đi theo cặp. Số lượng hoặc mô tả chỉ bắt đầu hiện cùng lúc hoặc sau khi icon bắt đầu hiện. Khi đếm số và tween thanh tiến độ, đồng bộ nhịp và giá trị cuối. Chọn hiển thị trực tiếp nếu dễ đọc hơn.
- Khi đổi tab, nội dung cũ/mới trượt theo chiều thao tác. Khi đổi lựa chọn, tập trung phản hồi ở ô được chọn và phần chi tiết thay đổi.
- Dùng nhún, lóe hoặc beam đúng thời điểm chạm nếu hợp game. Loop chỉ thêm khi có chủ đích. Phối hợp nhịp kết với màn kế tiếp để tránh hụt hình.

## Hỏi và chốt từng màn

Khi nhận nhiều màn, bắt đầu sau khi logic của tất cả các màn đã nối xong. Một màn lẻ dùng cùng quy trình với một lượt hỏi.

1. Chụp lại từng màn trong Game View, soạn đề xuất show và hide riêng cho mỗi màn, rồi báo người dùng là bắt đầu phần animation.
2. Đi lần lượt từng màn: cho xem ảnh chụp và đề xuất ngắn (vùng chuyển động, hướng, thời lượng, điểm nhấn). Hỏi **show** và **hide** tách riêng; mỗi câu có ba lựa chọn:
   - **Làm như đề xuất**: ghi nhận đề xuất.
   - **Chỉnh sửa**: đóng hộp hỏi và chờ người dùng mô tả trong khung chat. Nhắc lại ý đã hiểu trong một câu rồi đi tiếp.
   - **Không làm**: giữ nguyên phần đó.
3. Có đủ câu trả lời của màn hiện tại mới sang màn kế tiếp. Hỏi hết các màn rồi mới ghép, để Unity chỉ bận một đợt.

Nếu môi trường không có hộp lựa chọn, hỏi bằng tin nhắn với đúng ba lựa chọn đó. Nếu có phiên điều phối (ví dụ UI_Skill của unity-ui), xin lượt dùng Unity ở đó trước khi vào Play, biên dịch hoặc sửa prefab; xong thì trả lượt. Báo về phiên đó khi bắt đầu, khi hỏi xong và khi hoàn tất, kể cả khi người dùng chat trực tiếp tại đây.

## Ghép và kiểm tra

Tách trình diễn vào component hoặc lớp animation riêng theo kiến trúc hiện có. Logic nghiệp vụ sở hữu dữ liệu, phần thưởng và lựa chọn; tween chỉ đọc giá trị đích và điều khiển hình hiển thị. Dùng sự kiện và điểm hoàn tất show/hide sẵn có. Giữ callback điều hướng đúng một lần, kể cả khi tween bị ngắt. Với UI trong lúc pause, dùng thời gian unscaled.

Filter UI chụp hình chữ vào texture (như UIFX FilterStack) có thể giữ bản chụp lúc scale gần 0 và làm chữ mờ. Khi phóng từ 0, bật cập nhật theo transform của filter đó hoặc bắt đầu từ scale lớn hơn 0; kiểm tra lại chữ sau khi tween kết thúc.

Khôi phục trạng thái hiển thị khi mở lại, dọn tween/loop và tránh lấy transform đang dở làm vị trí gốc. Kiểm tra qua luồng thật: mở, đóng, mở lại, đổi nhanh và đóng giữa chừng. Đối chiếu kết quả gameplay khi hiệu ứng chạy hết và khi bị bỏ qua. Báo rõ phần đã quan sát trong Play Mode và phần mới kiểm tra bằng code.
