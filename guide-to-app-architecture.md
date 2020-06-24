Hướng dẫn này bao gồm các bài luyện tập và những lời khuyên hữu ích về kiến trúc để xây dựng một ứng dụng chất lượng cao. Ở đây chúng tôi giả định rằng bạn đã có những 
kiến thức cơ bản về Android Framework. Nếu bạn là người mới làm quen với Android, vui lòng xem trước các hướng dẫn tại đây để có thể nắm được những khái niệm được đề cập tới
trong hướng dẫn này.

### Trải nghiệm người dùng
Trong hầu hết các trường hợp, các ứng dụng dạng desktop đều có 1 điểm đơn bắt đầu từ desktop hoặc 1 trình khởi chạy, sau đó sẽ chạy như 1 khối. Với các ứng dụng Android thì lại
khác, nó có nhiều cấu trúc phức tạp hơn. Một ứng dụng Android điển hình bao gồm nhiều thành phần như acitivities, fragments, services, content provider và broadcast recievers.

Hầu hết bạn sẽ khai báo các thành phần này trong tệp tin app manifest của ứng dụng. Sau đó hệ điều hành Android sẽ sử dụng tệp tin này để quyết định việc tích hợp các thành phần
trên thành 1 ứng dụng. Giả sử ứng dụng của bạn được tạo nên bởi nhiều thành phần và người dùng sẽ tương tác với nhiều ứng dụng trong 1 khoảng thời gian ngắn, ứng dụng cần tương
thích với nhiều kịch bản sử dụng cũng như các tác vụ khác nhau.

Ví dụ, hãy xem những gì xảy ra khi người dùng chia sẻ 1 hình ảnh lên mạng xã hội như Facebook:
1. Ứng dụng sẽ cần dùng đến camera, hệ điều hành Android sẽ mở ứng dụng camera để xử lý yêu cầu. Tại thời điểm này, người dùng đã rời khỏi ứng dụng mạng xã hội, nhưng trải nghiệm của họ vẫn
đảm bảo liên tục.
2. Ứng dụng cần gọi đến 1 thành phần khác, như cần lựa chọn 1 tệp tin, và có thể cũng sẽ mở 1 ứng dụng khác.
3. Cuối dùng người dùng quay lại mạng xã hội và chia sẻ ảnh

Tại bất kỳ thời điểm nào trong quá trình sử dụng ứng dụng, người dùng có thể bị gián đoạn bởi 1 cuộc gọi hay 1 thông báo... Sau khi thực hiện xong các hoạt động này, người dùng
mong muốn quay trở lại trạng thái trước đó đang sử dụng ạp, ở đây muốn nói đến lại quay lại quá trình chia sẻ ảnh. Đây được xem như 1 hành vi cơ bản của các ứng dụng di động, vì
vậy bạn cần xử lý các trường hợp này một cách đúng đắn.

Hãy luôn nhớ 1 điều rằng tài nguyên ứng dụng bị phụ thuộc vào hệ điều hành, do vậy tại bất kỳ thời điểm nào, hệ điều hành có thể dừng các tiến trình của ứng dụng. Vì vậy, bạn không
nên lưu trữ bất kỳ dữ liệu hoặc trạng thái của các thành phần ứng dụng; và các thành phần ứng dụng không bị phụ thuộc lẫn nhau.

### Nguyên lý chung của kiến trúc

* Tách biệt các thành phần
Điều quan trọng nhất của nguyên lý là tách biệt các thành phần. Đây là 1 sai lầm chung khi bạn viết tất cả mã nguồn vào Activity hoặc Fragment. Những lớp phụ trách UI cơ bản này
chỉ nên chưa các logic xử lý liên quan đến UI và các tương tác của hệ điều hành. Bằng cách giữ cho các lớp này càng gọn càng tốt, bạn có thể sẽ tránh được các vấn đề liên quan
tới vòng đời của chúng.

Hãy nhớ rằng bạn không sở hữu Activity hay Fragment; hơn nữa những thành phần này đại diện cho sự tương tác giữa hệ điều hành Android và ứng dụng của bạn. Chúng có thể bị bởi hệ
điều hành hủy cứ lúc nào, ví dụ như khi bộ nhớ quá tải. Để đảm bảo cung cấp những trải nghiệm tốt nhất cho người dùng và việc bảo trỉ sau này, cách tốt nhất làm giảm sự phụ thuộc
vào chúng.

* Hướng UI tới 1 model
Một nguyên lý quan trọng khác đó là hướng UI tới 1 model. Model là 1 thành phần chịu trách nhiệm xử lý dữ liệu của ứng dụng. Nó độc lập với các đối tượng View và các thành phần
của ứng dụng, vì vậy nó không bị ảnh hưởng vòng đời của ứng dụng và các liên kết khác.

Nguyên lý này bắt nguyền từ những lý do sau:
- Dữ liệu của người dùng không bị mấy khi hệ điều hành hủy ứng dụng khi cần bộ nhớ
- Ứng dụng của bạn vẫn tiếp tục sử dụng trong trường hợp kết nối mạng không ổn định hoặc không khả dụng

Bằng việc phân rõ trách nhiệm trên ứng dụng của bạn, làm nó trở nên dễ dàng kiểm thử và nhất quán hớn

### Đề xuất kiến trúc ứng dụng
Trong phần này, chúng tôi sẽ trình bày cách xây dựng ứng dụng sử dụng các Architecture Componet với 1 use case đầy đủ.

Giả sử chúng tôi xây dựng 1 ứng dụng hiển thị thông tin của người dùng. Chúng tôi sử dụng backend server cung cấp REST API để lấy thông tin người dùng.

* Tổng quan
Để bắt đầu, bạn hãy quan sát lược đồ sau để thấy được các mô-đun trong ứng dụng tương tác với nhau như thế nào.

