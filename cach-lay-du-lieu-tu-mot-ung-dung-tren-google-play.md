### Mở đầu
Hello all, trước khi đi vào bài viết mình có 1 câu hỏi cho các bạn. Đã bao giờ bạn làm 1 ứng dụng có sử dụng dữ liệu(offline hoặc online) mà bạn nghĩ đến việc dữ liệu của mình "được" ai đó sử dụng lại Hoặc đơn giản là bạn muốn tò mò xem 1 ứng dụng của người khác sử dụng dữ liệu và tổ chức nó như thế nào?
Việc tò mò trên có thể xem là 1 hành động không được đẹp cho lắm, tuy nhiên không có gì ngăn cản được sự tò mò của bạn khi mà bạn đã thích 1 việc gì đó phải không nào.
Ở bài viết này, mình sẽ chia sẻ với các bạn cách mà mình đã "tò mò" 1 số app trên Google Play như thế nào nha.
### 1. Cách tải file apk từ Google Play
Trước hết bạn cần xác định xem mình muốn tò mò app nào, có thể bạn thấy app đó giao diện rất là đẹp, animation các kiểu, hoặc có dữ liệu khá là hay....và bạn muốn biết điều đó. 
Để làm được điều này trước hết bạn cần phải có được file apk của app đó, apk thực chất cũng chỉ là 1 file nén như các file zip hay jar thông thường và có thêm 1 số tính chất đặc thù của nó.
Vậy làm thế nào để có thể "chôm" được apk từ Google Play? Đơn giản bạn chỉ cần google với từ khóa "download apk online", có rất nhiều trang giúp bạn có thể chôm được.

Ở đây mình sử dụng trang này:
https://apps.evozi.com/apk-downloader/

và ứng dụng mà mình muốn tò mò là app này:
https://play.google.com/store/apps/details?id=com.awabe.englishpronunciation

vì mình thấy app này dữ liệu khá là hay và chưa thấy app nào có.
Công việc lúc này chỉ đơn giản là bạn copy link app và paste vào web này. Đợi 1 chút bạn sẽ có được file apk. Tuy nhiên, mình cũng đã thử lấy file apk của khá nhiều app, thì thấy rằng, không phải app nào cũng get ra được file apk. Mình không biết là do trang này get lỗi hay bên Google play có cơ chế nào đó chặn việc này. Vì vậy, nếu get lỗi thì bạn hãy xem đó là chuyện bình thường nha.
Sau khi get thì mình thu được file apk như này: com.awabe.englishpronunciation.apk
Nếu bạn không get được thì bạn có thể sử dụng file mà mình đã get rồi nha.
Link: https://drive.google.com/drive/folders/1TyHVhSHqu3zSfdGPqakdl6VBHAVWJ6Wp?usp=sharing
### 2. Cách dịch ngược code từ file apk
Bước tiếp theo sau khi bạn có được file apk là giải nén file apk này hay còn gọi là decomplie. Vẫn tiếp tục với việc google search với từ khóa "decomplie apk online", một số trang web có thể giúp bạn decomplie, điển hình là các trang: 

http://www.javadecompilers.com/apk

https://www.apkdecompilers.com/

Ở đây mình sử dụng trang thứ nhất. Sau khoảng 1 phút với thao tác upload và đợi decomplie bạn sẽ thu được 1 file zip chứa toàn bộ file code của project đó.
Nếu không decomplie được thì bạn có thể sử dụng file mà mình đã decomplie  rồi nha.
### 3. Tìm hiểu source
Sau khi có được file zip decomplie, thì bạn unzip và thu được project với cấu trúc như sau:
![](https://images.viblo.asia/52bd19b5-8aa3-4a9b-8bb9-61cc102c0e46.png)

Bạn nhìn thấy cấu trúc project chắc là quen nhỉ. Đây chính là cấu trúc thư mục giống như trong Android Studio mà hằng ngày bạn vẫn thường làm đấy. Tuy nhiên, với cấu trúc như vậy bạn không thể mở được project này với Android Studio đâu. (Bạn cứ thử mở là biết nha). Mà thực sự đến lúc này mình cũng chưa nghĩ đến việc mở project lên xem code luôn đâu. Mục đích chính của mình là "đi tìm dữ liệu" có trong app. Vì vậy, cái đầu tiên mình nghĩ tới là app sẽ lưu dữ liệu ở đâu. Mình tìm dữ liệu offline trước và mình sẽ nghĩ đến những nơi có thể lưu trữ dữ liệu offline. Thứ nhất là assets folder nơi có khả năng cao nhất chứa dữ liệu offline nếu có, bao gồm sqlite db, các file json....Đúng như mình mong đợi, tại assets folder mình tìm thấy 1 file là data.zip.
![](https://images.viblo.asia/e1118f8a-3a49-49eb-9ef1-31e002267b91.png)

Sau khi unzip thì bạn nhận được file **english_pronounce_v1.sqlite**
Còn gì hơn nữa khi có được file db rồi, cùng mở lên xem trong có gì nào.
Ở đây mình sử dụng https://sqliteonline.com/ để mở db.
Db gồm 2 bảng: **category và vocabulary**.
Mình mở bảng category trước xem có gì nào
![](https://images.viblo.asia/93578da7-00a3-4b0f-a984-7911ad353936.png)

Như trên hình các bạn có thể thấy có rất nhiều bản ghi, lúc này các bạn đã thấy kích thích chưa, chưa bao giờ lại có thể chôm dữ liệu của người khác dễ vậy đúng không.
Tuy nhiên bạn đừng vội mừng nha, khi dev code họ cũng đã lường trước được trường hợp app sẽ bị decomplie để lấy dữ liệu như này. Nên họ đã mã hóa dữ liệu, xong đó mới lưu vào db. Bạn vừa vui mừng chưa được 10s thì bắt đầu thấy nản rồi phải không? Nhưng "vỏ quýt dày sẽ có móng tay nhọn". Không thể vì mấy cái mã hóa này mà làm khó được bạn phải không. Suy nghĩ 1 chút thì bạn có liên tưởng đến dev sẽ dùng gì đễ mã hóa không? Và cái đầu tiên mình nghĩ đến là base64 (mình chỉ nghĩ theo kinh nghiệm thôi, vì thấy dạng mã này rất phổ biến). Lúc này mình thử lấy 1 đoạn mã trong db để decode thử. Tool decode base64 thì khá nhiều, bạn tự google search nha. Ở đây mình sử dụng https://www.base64decode.org/.
Vậy là mình hí hửng đem 1 đoạn mã đi để decode, nhưng kết quả lại không được như mình nghĩ. Decode nó chẳng ra gì hết :'( Lúc này mình thấy hơi nản chút. Nhưng vẫn không ý định từ bỏ sẽ giải được đoạn mã này. Bây giờ mình mới nhớ đến là mình đang có toàn bộ source code mà. Mình quyết định vào source để search với từ khóa "base64" để xem project có import thư viện base64 không. Nếu có thì chắc chắn đoạn mã này dùng base64 để encode. Đúng vậy, kết quả mình làm mình hài lòng đó là trong source code có import rất nhiều thư viện base64. Vậy lúc này mình lại có lý do để tiếp tục nghĩ rằng dữ liệu trong bảng được mã hóa theo base64. Nhưng có 1 câu hỏi đặt ra là tại sao nếu đây là mã hóa base64 thì mình lại không decode được? Suy nghĩ 1 lúc mình mới nghĩ ra 1 tình huống thế này. Dev code app này họ cũng đã lường trước được trường hơp này, nên họ cũng không đơn giản chỉ là mã hóa đơn thuần như vậy. Mà học sẽ làm cho nó phức tạp thêm 1 tí để đánh lừa người giải mã như mình. Và đây sẽ là lúc bạn sẽ phải đọc hiểu source code thực sự của họ. Thì sau một hồi tìm và đọc source mình đã tìm ra mấu chốt của vấn đề là tại sao mình không decode được. Thì ra dev đã cố tình chèn thêm một số ký tự vào đoạn mã để làm phá vỡ quy tắc mã hóa của base64 (Bạn có thể tự tìm xem đoạn đó chỗ nào nha). Đến lúc này, mọi chuyện dường như đã đơn giản đi khá nhiều. Và đúng như vậy, sau khi loại bỏ 1 số ký tự thừa mà dev thêm vào đoạn mã, mình đã decode thành công đoạn mã có trong db. Thật là không uổng phí mấy phút cuộc đời phải không nào.
### Kết luận
Trên đây, là những chia sẻ của mình về cách lấy dữ liệu từ 1 app trên Google Play. Tuy nhiên, mình cũng khuyên các bạn không nên làm như vậy với các mục đích không tốt, nếu chỉ làm vì thỏa mãn nhu cầu tìm hiểu của cá nhân thì OK. Còn nếu sử dụng với mục đích khác thì mình cũng không biết trước được sẽ như thế nào, nói chung là không nên làm vậy.
Và mình cũng nói thêm là không phải ứng dụng nào bạn cũng có thể decode để lấy data 1 cách dễ dàng như vậy đâu nha. Nó còn phụ thuộc vào rất nhiều yếu tố nữa. Nếu bạn thật sự muốn tìm hiểu, thì cứ thử làm vài app xem kết quả thế nào nhé.

Tóm lại để có thể lấy được data từ các app trên Google Play bạn có thể làm theo các bước sau:

B1: Download được file .apk của ứng dụng

http://apkleecher.com/

https://apps.evozi.com/apk-downloader/

https://apk-dl.com/

B2: Decode được file .apk vừa download dược ở trên.

http://www.javadecompilers.com/apk

B3: Tìm data và đọc hiểu code 1 vài phần nào đó (phụ thuộc vào bạn là chính)

Chúc các bạn thành công.
