Hello mọi người lại là mình đây, tiếp tục với loạt bài học cách xây dựng 1 ứng dụng hoàn chỉnh, phần 2 này mình sẽ giới thiệu phần chuẩn bị dữ liệu bằng cách crawl data. Nhưng trước khi viết tiếp mình muốn nói 1 điều ở đây là việc crawl dữ liệu cũng có nhiều mặt trái khác nhau, và mình khuyên bạn hãy tìm hiểu và sử dụng sao cho hợp lý và có ý nghĩa.

Ngôn ngữ sử dụng để crawl là Java

Trang web cần crawl là [cooky.vn](http://cooky.vn/)

Library hỗ trợ việc parse HTML là [jsoup](https://jsoup.org/)

Library hỗ trợ việc parse JSON là [gson](https://github.com/google/gson)

Tool hỗ trợ việc call API là [Postman](https://www.postman.com/)

Một số kỹ năng nên biết:

- Cơ bản cấu trúc các thẻ HTML
- Sử dụng dev tool trên browser (F12 Chrome)
- Call API và parse JSON
- Đọc ghi file

Trang cooky thì có rất nhiều dữ liệu và mình chỉ cần lấy các dữ liệu sau:

- Danh sách danh mục món ăn
- Danh sách các món ăn theo danh mục
- Thông tin chi tiết về món ăn

### 1. Danh sách danh mục món ăn

bạn vào đường dẫn này: https://www.cooky.vn/cach-lam và nhấn F12 để bật dev tool của trình duyệt, vào phần Network, chọn Fillter là XHR

![](https://images.viblo.asia/4c62defa-437d-4244-925f-5353ea66c0b5.png)

bạn chú ý những phần mình khoanh đỏ trong hình và các mục 1, 2, 3. Mục 3 chính là danh sách danh mục món ăn mình cần lấy, khoảng chừng 30 tên. Vì chỉ đơn giản là text nên bạn có thể tự ghi lại bằng tay, nhưng để việc crawl được chuyên nghiệp hơn thì bạn hãy xem mục 1, 2. Đây là nơi bạn có thể tìm thấy API của trang web dùng để get ra list danh mục này. Như hình bạn cũng đã thấy có 1 API tên `getfillter` và bên cạnh là response trả về. Đọc sơ qua response thì thấy nó chứa tên của các danh mục mình cần lấy, vậy không còn bàn cãi gì nữa đây chính là API cần tìm cho việc get danh sách danh mục món ăn rồi. Để xem chi tiết các thông tin như url, request method, param... bạn chuyển sang tab Headers (cạnh tab Preview ấy). Sau khi có các thông tin cần thiết thì bạn có thể dùng postman để gọi thử và việc còn lại là viết code để get data thôi. Nếu bạn không thấy API `getfillter` thì hãy F5 để refresh lại trang web nhé.

Mình sẽ hướng dẫn bạn cách chạy API bằng postman cho trực quan. Đầu tiên hãy chuyển qua tab Headers và để ý các thông số: Request URL, Request Method, Request Headers, Request Payload

![](https://images.viblo.asia/58af61d1-b925-44bd-98d3-596c553dbef6.png)

như hình thì mình sẽ có được các thông tin cần thiết cho 1 request:

- url: https://www.cooky.vn/directory/getfilter
- mothod: POST
- headers: Content-Type: application/json
- body gửi lên (dạng JSON): {"Keyword":"","Type":"course","SelectedIds":[],"PageSize":200,"PageIndex":1}

với những thông số trên bạn config vào postman để thấy rõ kết quả trả về từ API.

![](https://images.viblo.asia/eb4006de-23ed-4544-a534-ff3e9dd6af98.png)
![](https://images.viblo.asia/7ebfab65-c74f-4785-ae78-03996188c68c.png)
![](https://images.viblo.asia/91246d1a-f789-44c8-ae15-f233ed434c78.png)

kết quả trả về đã có dữ liệu mình cần, nhưng chưa phải dữ liệu mình mong muốn. Ở đây mình chỉ cần quan tâm đến 2 trường đó là `Id`, `Name`. Vậy bước tiếp theo bạn cần làm sau khi load được dữ liệu này về từ API là trích xuất, bóc tách 2 thuộc tính này từ JSON và lưu vào file riêng của mình. Sau 1 vài đoạn code phân tách mình sẽ được dữ liệu mong muốn như sau:

![](https://images.viblo.asia/fa113d2b-7e49-4bd9-adc7-6981bfddf933.png)

### 2. Danh sách món ăn theo danh mục
Bước khởi đầu lúc nào cũng gian nan phải không nào, sau khi bạn đã quen với việc crawl rồi thì với các bước sau sẽ trở nên dễ dàng hơn rất nhiều.

bạn tiếp tục vào đường dẫn này https://www.cooky.vn/cach-lam , click vào từng danh mục để xem có thấy API nào mình cần tìm không. 

![](https://images.viblo.asia/c74bef33-9ee6-4c9e-be47-3f1f240e5a98.png)

Bạn để ý dưới phần log thì có API search và dữ liệu bên cạnh + xem UI của trang web thì đây đúng là API mình có thể dùng được rồi. Vẫn với các bước tương tự như ở phần 1, mình sẽ xem phần header rồi param request ở header của dev tool và thử chạy trên postman để xem kết quả trực quan.

![](https://images.viblo.asia/352f2970-ea81-48da-974b-51ed600ecccd.png)

bạn cần config postman như sau để call được API này:

- url: https://www.cooky.vn/directory/search
- method: GET
- params: dt = 140

và đây là kết quả

![](https://images.viblo.asia/18246d7c-6014-404a-9023-a05ed6a70f61.png)

mình giải thích 1 chút về config này:

- số lượng param: mặc dù trên dev tool API này cần rất nhiều param gửi lên như `dt, q, st, page...` nhưng ở bài viết này mình chỉ truyền lên mỗi param `dt`. Vì có những param không cần thiết và mình cũng chẳng biết nó là gì, nên chỉ lấy cái nào mình cần thui. Còn tại sao lại cần param `dt`? vì khi bạn click vào từng loại danh mục như sacks, lẩu, sữa chua... thì bạn sẽ thấy param này thay đổi giá trị. Đây có thể là id của danh mục này, dựa vào đây để request server trả về loại món ăn tương ứng với danh mục. Còn nhiều cái hay ho hơn nữa bạn có thể tự tìm hiểu thêm, ở đây mình chỉ nói như vậy để các bạn hiểu cho dễ.
- giá trị của param dt: để lấy được giá trị của param này thì như mình nói ở trên, bạn cứ click lần lượt vào các danh mục sẽ thấy dt thay đổi giá trị: `122,123,124,125,126,127,128,129,130,131,132,133,135,136,137,138,139,140,104,105,106,107,108,109,110,111,112,113,114,115,117,118,119,120,121`

Vẫn như API trước, mình cũng chỉ quan tâm đến các dữ liệu mình cần như `name, totaltime, level, img, ingredient...` . Bạn lại tiếp tục sử dụng kỹ năng call API và phân tách JSON để lấy được các dữ liệu mình mong muốn.

![](https://images.viblo.asia/867a04ce-3743-47b9-898c-0d5d1f98d5ae.png)

Đây là danh sách các món ăn của 1 danh mục, với các danh mục khác bạn cũng làm tương tự như vậy. 1 vòng for là ổn phải không nào =))

### 3. Thông tin chi tiết về món ăn
Về phần thông tin chi tiết của 1 món ăn bạn click vào 1 món ăn bất kỳ để xem các thông tin cần lấy tiếp theo là gì. Ở đây mình chỉ lấy thêm 1 số thông tin về các bước làm + hình ảnh đi kèm với các bước làm tương ứng. Ngoài ra bạn có thể lấy thêm thông tin như: số lượt like, user....

Vẫn là bước làm quen thuộc là xem dev tool xem có lấy được gì không? nhưng lần này ở tab XHR mình không tìm được API mình cần về thông tin món ăn. Vậy có lẽ ở phần này trang web sẽ không call API như ở 2 phần trước nữa. Vậy mình sẽ chuyển sang tab All để tìm lần lượt, chắc chắn là sẽ có trong này, chỉ là nó nằm ở reques nào thôi.

Ví dụ ở đây mình vào link món ăn này: https://www.cooky.vn/cong-thuc/song-so-nuong-giay-bac-52495

thì ở ngay request đầu tiên của tab All mình thấy thông tin cần lấy nằm ở đây, nhưng nó không phải dạng JSON như 2 phần trên mà là server sẽ trả về 1 page dạng HTML. Các thông tin mình cần lấy sẽ nằm trong các thẻ HTML, lúc này sẽ là lúc mình dùng đến thư viện Jsoup để bóc tách các thẻ HTML và lấy thôn tin mình cần.

![](https://images.viblo.asia/3c6f608c-c06f-43f9-b793-ad76ac665236.png)

Để xem được nội dung file HTML, bạn chỉ cần chuột phải và chọn View page source (Ctrl + U). Bạn sẽ thấy 1 file HTML cả hàng nghìn dòng code, nhưng đừng vội hoang mang bạn chỉ cần quan tâm đến phần nội dung mình cần lấy, và cấu trúc các thẻ HTML chứa nội dung của mình cần. Ở đây mình chỉ trích dẫn đoạn HTML chứa nội dung các bước làm thôi nha.

``` HTML
<div class="panel-group description" id="accordionDirection" >
        <div class="panel panel-default clearfix" id="step-290136">
            <div class="panel-heading">
                <h4 class="panel-title">
                    <a data-toggle="collapse" href="#collapseDirection1" target="_self">
                        Bước <span class="num">1</span>
                    </a>
                    <a href="javascript:void(0)" class="tick-active"><span class="fa fa-circle-o"></span></a>
                </h4>
            </div>
            <div id="collapseDirection1" class="panel-collapse collapse in">
                <div class="panel-body">
                    <div class="step-desc">
                        Bóc vỏ tỏi, cắt thành những lát mỏng. Rải đều các lát tỏi lên khay giấy bạc đã chuẩn bị sẵn. Thái hạt lựu cà rốt, thái nhỏ bông cải xanh và nấm linh chi nâu.
                    </div>
                                                                                                    <div class="step-photos">
                        </div>
                    <div class="comment-widget-box step-comments-box">
                        <cooky-like-comment-widget ref-root-id="52495" ref-id="290136" ref-type="11" environment="'recipestep'"></cooky-like-comment-widget>
                    </div>
                </div>
            </div>
        </div>
        <div class="panel panel-default clearfix" id="step-290137">
            <div class="panel-heading">
                <h4 class="panel-title">
                    <a data-toggle="collapse" href="#collapseDirection2" target="_self">
                        Bước <span class="num">2</span>
                    </a>
                    <a href="javascript:void(0)" class="tick-active"><span class="fa fa-circle-o"></span></a>
                </h4>
            </div>
            <div id="collapseDirection2" class="panel-collapse collapse in">
                <div class="panel-body">
                    <div class="step-desc">
                        Rửa sạch cồi sò điệp tươi và thịt sò lông tươi. Xếp cồi sò điệp và sò lông lên trên tỏi. Tiếp đó là rải đều lên cà rốt, bông cải xanh và nấm linh chi nâu.
                    </div>
                                                                                                    <div class="step-photos">
                        </div>
                    <div class="comment-widget-box step-comments-box">
                        <cooky-like-comment-widget ref-root-id="52495" ref-id="290137" ref-type="11" environment="'recipestep'"></cooky-like-comment-widget>
                    </div>
                </div>
            </div>
        </div>
        <div class="panel panel-default clearfix" id="step-290138">
            <div class="panel-heading">
                <h4 class="panel-title">
                    <a data-toggle="collapse" href="#collapseDirection3" target="_self">
                        Bước <span class="num">3</span>
                    </a>
                    <a href="javascript:void(0)" class="tick-active"><span class="fa fa-circle-o"></span></a>
                </h4>
            </div>
            <div id="collapseDirection3" class="panel-collapse collapse in">
                <div class="panel-body">
                    <div class="step-desc">
                        Mở trước lò nướng ở 200 độ C trong 5 phút. Nêm vào 1 muỗng cà phê hạt nêm, 1 muỗng canh dầu ăn và 10g bơ. Gói giấy bạc bọc kín các nguyên liệu lại. Cho vào lò nướng khoảng 15 phút là hoàn tất.
                    </div>
                                                                                                    <div class="step-photos">
                        </div>
                    <div class="comment-widget-box step-comments-box">
                        <cooky-like-comment-widget ref-root-id="52495" ref-id="290138" ref-type="11" environment="'recipestep'"></cooky-like-comment-widget>
                    </div>
                </div>
            </div>
        </div>
        <div class="panel panel-default clearfix" id="step-290139">
            <div class="panel-heading">
                <h4 class="panel-title">
                    <a data-toggle="collapse" href="#collapseDirection4" target="_self">
                        Bước <span class="num">4</span>
                    </a>
                    <a href="javascript:void(0)" class="tick-active"><span class="fa fa-circle-o"></span></a>
                </h4>
            </div>
            <div id="collapseDirection4" class="panel-collapse collapse in">
                <div class="panel-body has-photo">
                    <div class="step-desc">
                        Dùng kéo cắt mở miệng giấy bạc, cẩn thận hơi nóng món sò nướng giấy bạc dễ gây bỏng nhé. Bạn có thể chuẩn bị thêm chén nướng tương cay thơm để chấm hoặc rưới lên ăn kèm cho vừa miệng hơn.
                    </div>
                                                                                                    <div class="step-photos">
                                    <a class="cooky-photo one-photo" href="javascript:void(0)" data-id="496582" data-group="1" data-dimen=" 0x0">
                                        <img data-src='https://media.cooky.vn/recipe/g6/52495/s480x480/recipe52495-cook-step4-637194521306384598.jpg' data-original='https://media.cooky.vn/recipe/g6/52495/s480x480/recipe52495-cook-step4-637194521306384598.jpg' data-lazy='https://media.cooky.vn/recipe/g6/52495/s480x480/recipe52495-cook-step4-637194521306384598.jpg' alt='Song sò nướng giấy bạc' class='lazy' src='https://www.cooky.vn/imgs/blank-img/400x400.jpg'  />
                                    </a>
                        </div>
                    <div class="comment-widget-box step-comments-box">
                        <cooky-like-comment-widget ref-root-id="52495" ref-id="290139" ref-type="11" environment="'recipestep'"></cooky-like-comment-widget>
                    </div>
                </div>
            </div>
        </div>
</div>
```
bạn sẽ thực hiện bóc tách dữ liệu trong đoạn HTML này. Nhưng nếu bạn đọc kỹ hơn file HTML của cả trang, bạn sẽ thấy 1 đoạn HTML cũng chưa thông tin bạn cần nhưng đơn giản hơn đoạn HTML trên rất nhiều. Mình tìm được như sau:

``` HTML
<ul class="instructions" style="display:none">
        <li>bước 1: bóc vỏ tỏi, cắt thành những lát mỏng. rải đều các lát tỏi lên khay giấy bạc đã chuẩn bị sẵn. thái hạt lựu cà rốt, thái nhỏ bông cải xanh và nấm linh chi nâu.</li>
        <li>bước 2: rửa sạch cồi sò điệp tươi và thịt sò lông tươi. xếp cồi sò điệp và sò lông lên trên tỏi. tiếp đó là rải đều lên cà rốt, bông cải xanh và nấm linh chi nâu.</li>
        <li>bước 3: mở trước lò nướng ở 200 độ c trong 5 phút. nêm vào 1 muỗng cà phê hạt nêm, 1 muỗng canh dầu ăn và 10g bơ. gói giấy bạc bọc kín các nguyên liệu lại. cho vào lò nướng khoảng 15 phút là hoàn tất.</li>
        <li>bước 4: dùng kéo cắt mở miệng giấy bạc, cẩn thận hơi nóng món sò nướng giấy bạc dễ gây bỏng nhé. bạn có thể chuẩn bị thêm chén nướng tương cay thơm để chấm hoặc rưới lên ăn kèm cho vừa miệng hơn.</li>
</ul>
```

Bây giờ bạn đã thấy dễ hơn chưa nào. Ở đây mình xin phép không viết đoạn code parse HTML ở đây, 1 phần vì cấu trúc HTML của trang web có thể thay đổi liên tục, 2 là để các bạn tự mày mò phần nào.

Đến đây mình đã có toàn bộ dữ liệu của 3 phần mình cần, ví dụ 1 món ăn như sau:

```
{
  "name": "Lòng heo nướng chua ngọt",
  "time": 20,
  "img": "https://media.cooky.vn/recipe/g3/29522/s555x240/cooky-recipe-636585680901866090.jpg",
  "level": "Dễ",
  "title": "Cách làm lòng heo nướng chua ngọt",
  "des": "<p style=\"text-align: justify;\">M&oacute;n n&agrave;y quen thuộc với ch&uacute;ng ta nhưng ăn ở ngo&agrave;i th&igrave; mất vệ sinh, h&atilde;y c&ugrave;ng thực hiện n&oacute; ở nh&agrave; v&agrave; thưởng thức c&ugrave;ng Hằng nh&eacute;, Dễ l&agrave;m th&ocirc;i c&aacute;c bạn ah, m&agrave; ăn th&igrave; bao đ&atilde;</p>\r\n",
  "serving": 3,
  "components": [
    {
      "name": "Lòng heo",
      "quantity": "500",
      "unit": "Gr"
    },
    {
      "name": "Dưa leo",
      "quantity": "2",
      "unit": "Quả"
    },
    {
      "name": "Chanh",
      "quantity": "1",
      "unit": "Trái"
    },
    {
      "name": "Nước mắm",
      "quantity": "100",
      "unit": "ml"
    },
    {
      "name": "Muối",
      "quantity": "1",
      "unit": "Chén"
    },
    {
      "name": "Hành tím",
      "quantity": "3",
      "unit": "Củ"
    },
    {
      "name": "Bột ngũ vị hương",
      "quantity": "1 <sup>1</sup>/<sub>2</sub>",
      "unit": "Muỗng cà phê"
    },
    {
      "name": "Đường",
      "quantity": "1 <sup>1</sup>/<sub>2</sub>",
      "unit": "Muỗng cà phê"
    },
    {
      "name": "Hạt nêm",
      "quantity": "1",
      "unit": "Muỗng cà phê"
    },
    {
      "name": "Dầu điều",
      "quantity": "2",
      "unit": "Muỗng cà phê"
    }
  ],
  "type": "",
  "cook_steps": [
    {
      "step": 1,
      "des": "Lòng heo khi mua về các bạn rửa thật sạch với muối và nước. Sau đó cho vào chảo nóng cùng 1 muỗng canh nước mắm, đảo nhanh tay,  đem ra sửa lại với nước, làm vậy lòng heo sẽ sạch hơn cũng như giảm được mùi hôi của lòng, để ráo nước. Hành tím băm nhỏ.",
      "pictures": [
        "https://media.cooky.vn/recipe/g3/29522/s480x480/cooky-recipe-636585693736632633.jpg"
      ]
    },
    {
      "step": 2,
      "des": "Ướp lòng: cho lòng vào 1 cái tô lớn, cho tất cả nguyên liệu hành tím, hạt nêm, đường, dầu điều, bột ngũ vị hương, lượng nước mắm còn lại, dùng tay trộn đều hỗn hợp ướp lòng cho lòng được thấm hơn. Để 30 phút cho lòng thấm gia vị.",
      "pictures": [
        "https://media.cooky.vn/recipe/g3/29522/s300x300/cooky-recipe-636585693999961095.png",
        "https://media.cooky.vn/recipe/g3/29522/s300x300/cooky-recipe-636585694008853111.png",
        "https://media.cooky.vn/recipe/g3/29522/s300x300/cooky-recipe-636585694026481142.png",
        "https://media.cooky.vn/recipe/g3/29522/s300x300/cooky-recipe-636585694029757148.png"
      ]
    },
    {
      "step": 3,
      "des": "Chuẩn bị 1 cái bếp than hồng, cho lòng lên nướng chín đều 2 mặt, các bạn canh trở đều 2 mặt nhé cho lòng khỏi bị cháy.",
      "pictures": [
        "https://media.cooky.vn/recipe/g3/29522/s300x300/cooky-recipe-636585694131937327.png",
        "https://media.cooky.vn/recipe/g3/29522/s300x300/cooky-recipe-636585694131001326.png"
      ]
    },
    {
      "step": 4,
      "des": "Vậy là món ăn đã hoàn thành rồi. Mình sẽ ăn kèm với dưa leo, muối tiêu chanh nhé các bạn. Chúc các bạn thành công.",
      "pictures": [
        "https://media.cooky.vn/recipe/g3/29522/s480x480/recipe29522-prepare-step4-636585724752451109.jpg"
      ]
    }
  ]
}
```

nếu bạn nào không tự crawl được dữ liệu thì có thể dùng các file mình đã crawl trước đỏ, [tại đây](https://drive.google.com/drive/folders/1DQaNwbmrZGBYkKfzfwVsQwjxgNt1wSP1?usp=sharing).

Dữ liệu sau khi lấy được mình đều đưa hết về dạng JSON để sau này làm API cho dễ. Việc cấu trúc JSON thì tùy thuộc vào mỗi người, việc crawl được dữ liệu mà mỗi người mong muốn cũng giống như việc bạn chỉnh sửa ảnh vậy, chỉnh đến lúc nào mình thấy ảnh đẹp thì thôi.

Hẹn gặp các bạn ở các phần sau.
