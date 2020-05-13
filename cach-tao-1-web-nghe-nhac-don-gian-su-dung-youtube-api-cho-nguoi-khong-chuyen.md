Hi all, hôm nay mình sẽ hướng dẫn các bạn làm 1 trang web nghe nhạc đơn giản sử dụng Youtube API. Ý tưởng xuất phát từ lúc mình đang là sinh viên và chung phòng trọ với 4 đứa bạn nữa. Cả phòng đứa nào cũng dùng laptop và đều thích nghe nhạc. Vấn đề bắt đầu nảy sinh vào 1 buổi tối, cả 5 đứa cùng ngồi lap và đều mở nhạc, đứa thì bật nhạc Sơn Tùng, đứa nữa thì lại Bolero, ...Mà thằng nào cũng bật gần như maximum và không đeo tai nghe, thế là mình được nghe nhạc remix miễn phí. Câu chuyện vẫn cứ tiếp diễn như vậy khoảng 1 tuần sau. Và tình cờ 1 hôm mình bắt gặp 1 bối cảnh tương tự như phòng mình tại văn phòng công ty [Genify](https://genify.vn/). Tức là cả văn phòng ai cũng thích nghe nhạc nhưng lại không thích dùng tai nghe. Và mọi người ở đây đã nghĩ ra ý tưởng làm 1 trang web để mọi người nghe nhạc chung. Tức là chỉ cần 1 máy bật và cắm loa, và mọi người sẽ vào web để request nhạc chung ở máy này. Vậy là mình đã về nói với mấy đứa cùng phòng, bọn nó đều OK, thế là mình bắt tay vào làm riêng cho phòng 1 web như vậy. Web có chức năng chính là tiếp nhận request bài hát từ client, ai request bài nào trước thì bài đó được phát trước.

Kiến thức mình sử dụng ở đây không có gì ngoài: HTML, CSS, JavaScript thuần, JQuery, Youtube API. Ở đây mình không dùng framework có sẵn như Boostrap mục đích để rèn luyện 1 chút kỹ năng cho bản thân. Vì mình không phải dev web nên code có phần gà mờ mong các bạn gạch đá nhẹ tay.

Giao diện đơn giản sẽ như thế này:

![](https://images.viblo.asia/fb31576a-39ae-476b-a12a-6fd867aedb4f.png)

*Giao diện chính của web*

Ý tưởng là vậy đó các bạn, mình cùng bắt tay vào làm nha.

### 1. Vẽ giao diện
Giao diện khá đơn giản với vài thẻ form và div. Web được chia thành 2 phần chính, nửa bên trái là khung tìm kiếm bài hát, nửa bên phải là khung phát nhạc và danh sách bài hát request.

Đối với nửa bên trái, bạn tạo 1 form để cho người dùng nhập tên bài hát, bên cạnh thì làm luôn nút Clear để người dùng có thể xóa Tên bài hát sau khi nhập cho nhanh, đỡ phải nhấn backspace nhiều chi cho lâu. Bên dưới form nhập tên để tìm kiếm là 1 div để hiển thị danh sách kết quả tìm kiếm. Ở mỗi bài hát trong danh sách có 1 nút Add để thực hiện request bài nhạc mà bạn muốn nghe.

Đối với nửa bên phải, bạn tạo 1 div để Play video, bên dưới video sẽ là 1 div khác để chứa danh sách bài hát được request đang đợi để phát. Ở mỗi bài hát trong danh sách có 2 nút: 1 để Play(phát luôn bài này, không phải đợi đến lượt), 1 nút để Remove(xóa bài này nếu không muốn nó nằm trong danh sách request nữa).

Cơ bản là cái body chỉ có vài dòng code như nè:

```html
<main>
     <div id="main-left">
        <form id="frm-search" action="#">
            <input type="text" name="keyword" id="keyword" placeholder="Type something and enter" autocomplete="off" autofocus>
            <input type="button" value="CLEAR" id="clear">
        </form>
        <div id="result-search"></div>
     </div>

     <div id="main-right">
        <div id="video"></div>
        <p id="title-queue"></p>
        <div id="queue-play"></div>
     </div>

     <div class="clear-fix"></div>
</main>
```

và sau đó sơn cho nó tí CSS nữa là ngon ngay.

### 2. Xử lý request
Đây được xem như phần linh hồn của web, phần này nhiệm vụ chính là xử lý tìm kiếm bài hát, lấy kết quả, hiển thị, xử lý các action của người dùng add/remove/play bài hát. Trước hết mình đi vào phần xử lý tìm kiếm trước nha.

Để có thể sử dụng Youtuve API để tìm kiếm, bạn cần có 1 API_KEY, để có được key bạn vào develop của Youtube đăng ký nhé https://developers.google.com/youtube/. 

```javascript
API_KEY = "AIzaSy.......................xNqYjs"
```

Khi đã có key rồi bạn tiến hành xử lý search video, ở đây mình dùng AJAX để get data:

```javascript
$.get("https://www.googleapis.com/youtube/v3/search", {
            part: 'snippet,id',
            q: keyword,
            maxResults: 50,
            type: 'video',
            key: apiKey
        },
        function(data) {
        // handle result
        }
    });
```
Ở đây mình chỉ sử dụng các tham số cơ bản thôi: maxResult - giới hạn kết quả tìm kiếm(để tránh request search quá lâu), type - xác định loại video cần tìm kiếm. Và còn khá nhiều tham số nữa, bạn có thể tham khảo [tại đây](https://developers.google.com/youtube/v3/docs/search/list).

Và kết quả API trả về như sau với từ khóa "Người hãy quên em đi":

![](https://images.viblo.asia/690f4d66-138b-4e91-bdcf-e61b9568fc73.png)
*data result*

Có khá nhiều key-value phải không nào, nhưng mình chỉ quan tâm đến các key-value cần thiết cho bài toán của mình thôi, đó là: **videoId**, **title**, **thumbnails**, **channelTitle**.

Việc còn lại khá đơn giản là parse json và hiển thị kết quả thôi nên mình không show ở đây. Nhưng có 1 điều đáng nói ở đây là trong data trả về không có 1 số thông tin như: độ dài, số lượt view của video. Mình cũng không hiểu tại sao API lại không trả về những thông tin này. Và để lấy được các thông tin này bạn cần tốn thêm 1 request nữa. Ở đây mình chỉ request để lấy độ dài video thôi, còn lượt view thì các bạn có thể tham khảo thêm nha.

Với request này cũng cũng dùng AJAX thôi, như này nè:
```javascript
var url = "https://www.googleapis.com/youtube/v3/videos?id=" + videoID + "&key=" + apiKey + "&part=snippet,contentDetails";
$.ajax({
    async: false,
    type: 'GET',
    url: url,
    success: function(data) {
        // handle result
    }
});
```
Trong kết quả trả về cũng có rất nhiều key-value, bạn chỉ cần quan tâm đến key-value là: 
```JSON
"duration": "PT3M52S"
```

Tuy nhiên API lại không trả ra cho bạn trực tiếp độ dài của video theo dạng milliseconds hoặc string biểu diễn độ dài như "04:56" mà đây là chuẩn [Date and time format - ISO 8601](http://support.sas.com/documentation/cdl/en/lrdict/64316/HTML/default/viewer.htm#a003169814.htm). Và bạn cần thêm 1 bước nữa để convert từ dạng này sang chuỗi "04:56" mong muốn.

Đây là đoạn convert duration:
```javascript
function convert_time(duration) {
    var a = duration.match(/\d+/g);

    if (duration.indexOf('M') >= 0 && duration.indexOf('H') == -1 && duration.indexOf('S') == -1) {
        a = [0, a[0], 0];
    }
    if (duration.indexOf('H') >= 0 && duration.indexOf('M') == -1) {
        a = [a[0], 0, a[1]];
    }
    if (duration.indexOf('H') >= 0 && duration.indexOf('M') == -1 && duration.indexOf('S') == -1) {
        a = [a[0], 0, 0];
    }

    duration = 0;

    if (a.length == 3) {
        duration = duration + parseInt(a[0]) * 3600;
        duration = duration + parseInt(a[1]) * 60;
        duration = duration + parseInt(a[2]);
    }
    if (a.length == 2) {
        duration = duration + parseInt(a[0]) * 60;
        duration = duration + parseInt(a[1]);
    }
    if (a.length == 1) {
        duration = duration + parseInt(a[0]);
    }
    var h = Math.floor(duration / 3600);
    var m = Math.floor(duration % 3600 / 60);
    var s = Math.floor(duration % 3600 % 60);
    return [h, m, s];
}
```

OK, đến bước này bạn đã có được các thông tin cần thiết để dùng cho bài toán rồi đó.

### 3. Xử lý play và hàng đợi request
Việc xử lý Play khá đơn giản, đó là bạn chỉ việc dùng đối tượng Player của API đã cung cấp, tham số truyền vào chỉ là **videoId** mà bạn lấy được ở trên

```javascript
function play(videoID) {
    // nếu player đã được khởi tạo rồi thì chỉ việc gọi .loadViedeoById là được
    if(player != null){
        player.loadVideoById({'videoId': videoID,
           'startSeconds': 0,
           'suggestedQuality': 'large'});

    } else { // khởi tạo player
        player = new YT.Player('video', {
          height: '390',
          width: '640',
          videoId: videoID,
          events: {
            'onReady': // handle onReady,
            'onStateChange': // handle onStateChange
          }
        });         
    }
}
```
Việc thứ 2 là xử lý nhiều request từ client, ở đây mình cần lưu lại các request này và xử lý các request theo cơ chế Hàng đợi First In First Out(vào trước ra trước). Vì muốn nhanh gọn nên mình không dùng cơ sở dữ liệu gì ở chỗ này cả, mà chỉ dùng **localStorage** của JavaScript để lưu thôi. Các bạn có thể extend function này với các hệ quản trị cơ sở dữ liệu như **MySQL**. Như đã nói lúc đầu, mình chỉ code chay JavaScript nên việc mình cũng không sử dụng Queue có sẵn mà sẽ tự tạo 1 Queue bằng mảng để dùng. Hôm trước mình cũng có viết 1 bài cách tạo cách tạo 1 Hàng đợi bằng mảng [tại đây](https://viblo.asia/p/cach-xay-dung-cau-truc-du-lieu-stack-va-queue-L4x5xwwalBM), các bạn vào xem nhé.

Ngoài việc thực hiện các request tuần tự, thì bạn cần xử lý thêm các action khác như: play/remove 1 bài hát trực tiếp mà không phải đợi. Các thao tác này cũng đơn giản là theo tác trên mảng, nên mình nghĩ m.n đều thực hiện dễ dàng ấy mà. Nếu không được, bạn hãy view source code của mình để làm theo nha.

Như vậy đến đây mình đã hướng dẫn các bạn tạo được 1 web nghe nhạc đơn giản rồi đấy.
### 4. Kết luận

Trên đây là toàn bộ chia sẻ của mình về ý tưởng cũng như cách làm 1 web nghe nhạc sử dụng Youtube API. 

Đây là source tham khảo: https://github.com/nvquangth/search-video-youtube

Tuy source code còn khá nhiều hạn chế về công nghệ, nhưng mình hy vọng nó có ích cho mọi người. Thanks all.
