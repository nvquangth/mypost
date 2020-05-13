Hello mọi người, hôm nay mình viết 1 loạt bài chia sẻ về cách tạo 1 ứng dụng Android hoàn chỉnh từ A - Z, từ 0 dòng code đến 1 tỉ dòng code, từ app debug cho đến app chạy được trên CH play, từ không có server đến có server..., nói chung là 1 ứng dụng hoàn chỉnh đúng nghĩa.
Trong loạt bài này mình sẽ dùng đến các kiến thức sau:
1. App
- Flutter
- Bloc Architecture
- Build release

2. Server API
- NodeJs: express
- MongoDb: databasse
- Heroku: deploy

3. CH Play
- Account Developer
- Publish app

4. Design
- Adobe XD
- Photoshop

5. Data
- Crawl data

Ý tưởng của mình là làm 1 ứng dụng về nấu ăn. Ứng dụng này sẽ cung cấp công thức cũng như các bước làm 1 món ăn nào đó, bao gồm rất nhiều thể loại. Dữ liệu sử dụng trong ứng dụng được crawl từ trang [cooky.vn](https://www.cooky.vn). Ứng dụng sẽ bao gồm các màn hình sau:
1. Category: hiển thị danh sách các thể loại món ăn như: bánh ngọt, nước uống, snack, kem.....
2. Category detail: hiển thị danh sách các món ăn dựa theo thể loại tương ứng và 1 số thông tin liên q
3. Recipe detail: hiển thị các thông tin chi tiết cách làm món ăn: thành phần, bước làm, số liệu cụ thể
4. Search: tìm kiếm món ăn dựa theo tên
5. Favorite: thêm các món ăn vào danh mục yêu thích
6. Ngoài ra còn 1 số tính năng khác: rate app, send mail feedback....

Từ ý tưởng, mình đi đến design. Ở đây mình sử dụng phần mềm Adobe XD, kết hợp 1 chút Photoshop để chỉnh sửa ảnh. Nếu bạn nào không dùng phần mềm này có thể dùng bất cứ phần mềm design nào khác đều ok hết, hoặc có thể sử dụng các [tool online](https://marvelapp.com/) để đỡ phải cái đặt. Về việc cài đặt và cách sử dụng thì mình xin phép không trình bày ở đây, vì nó khá tốn thời gian. 

![](https://images.viblo.asia/50f68c03-beaa-49e1-a9a4-8dc3d955c7fc.jpg)
Về cơ bản thì giao diện phần mềm như thế này, khá đẹp, dễ sử dụng và chuyên nghiệp.

Sau 1 thời gian kéo thả, căn chỉnh thì mình được các màn hình cả app như sau:

![](https://images.viblo.asia/0df9a3de-b114-4718-b166-bf50a0c30f2f.png)
1 - Màn hình danh sách các category

![](https://images.viblo.asia/c9b46495-0781-497e-b86f-6656ec9cc732.png)
2 - Màn hình danh sách các món ăn dựa theo category

![](https://images.viblo.asia/ec2a2c31-1582-4271-bdcd-8e7911ca9ad4.png)
3 - Màn hình món ăn chi tiết

![](https://images.viblo.asia/d1ed4dec-b209-4838-a549-c5a6bd2fcd99.png)
4 - Màn hình tìm kiếm

![](https://images.viblo.asia/e170fc32-4453-44ea-9d11-3d2da49d1bf9.png)
5 - Màn hình danh sách yêu thích

![](https://images.viblo.asia/06511090-b426-42d5-9589-32549a6a05d3.png)
6 - Màn hình drawer

Trong design mình có sử dụng bộ icon chuẩn của Android, bạn có thể tìm và tải [tại đây](https://www.xdguru.com/adobe-xd-icons-material/)

Bạn cũng có thể xem qua bản design của mình [tại đây](https://drive.google.com/file/d/142vP-bcNSCL65SQiuaXfD8iClr7gq58L/view?usp=sharing) và [tại đây](https://drive.google.com/drive/folders/1Nsgf9BtmnzLq64R9eFhz26QlSkna4cuu?usp=sharing)

Ok, bài viết hôm nay mình xin dừng ở đây, coi như mình đã giới thiệu xong phần design cho ứng dụng. Phần tiếp theo mình sẽ viết tiếp về phần chuẩn bị dữ liệu và crawl data.
