Hi all, hôm nay mình sẽ giới thiệu với mọi người 1 chút về `Animation` trong `Flutter`. Thực sự mà nói, Animation không phải là 1 cái gì đó dễ dàng với Android native, nhưng với Flutter nó chưa bao giờ dễ đến như vậy, hiệu ứng vừa đẹp, tốc độc emplement lại nhanh. Animation trong Flutter rất đa dạng và phong phú, từ implicit animations đến explicit animation. Bài này mình sẽ giới thiệu về cái cơ bản nhất là `Animated Containers`.
> Animated Containers are implicit animated widgets.

Đợi chút nào, bạn hiểu thế nào về câu nói trên?
Thông thường để tạo ra và sử dụng Animation bạn cần phải sử dụng đến 2 object là `Animtaion` và `AnimationContainer`, Đôi khi bạn còn phải sử dụng đến các object phức tạp hơn để tạo animation như `TickerProviderMixin`. Và bạn phải start, end và thực hiện các thao tác 1 cách thủ công các action đó. Với cách làm như vậy người ta gọi là explicit animations, tức là bạn phải thực hiện quản lý animations 1 cách thủ công. Còn với `Animated Containers` thì bạn không phải làm việc này thủ công nữa, mà đối tượng này đã empliment sẵn cho bạn các theo tác thủ công đó rồi. Bạn chỉ việc truyền vào các giá trị cho các tham số như `start`, `end`, `duration`... Chính vì vậy người ta gọi Animated Containers là 1 implicit animated. Đến đây bạn đã hiểu ý nghĩa của câu nói trên rồi phải không.

![](https://images.viblo.asia/ba4e1693-3020-4ba3-85e0-6b6bfa871180.gif)

### Ví dụ 1: Bar Chart

Hãy tưởng tượng bạn cần xậy dựng 1 ứng dụng có sử dụng đến biểu đồ cột, và cần 1 số hiệu ứng tăng giảm chiều cao cột đồ thị khi giá trị thay đổi.

![](https://images.viblo.asia/1dcbc675-ebda-4b14-bb5e-dc56bd68491e.gif)

Trước tiên, bạn cần tạo ra 1 cột đơn giản sử dụng `Container widget` với chiều cao là `40.0`, và đoạn code sẽ đơn giản như sau:

```
Container(
    width: 60.0,
    height: height,
    color: Color(0xff14ff65),
  ),
```

Và bạn có 1 `Button` khi nhấn vào thì chiều cao cột của đồ thị sẽ thay đổi từ `40.0` lên `320.0`. Chỗ này bạn chỉ đơn giản là gọi phương thức `setState()` trong `onPressed` của Button để rebuilt lại UI.

```
onPressed: (){
  setState(() {
    height = 320.0;
  });
},
```

![](https://images.viblo.asia/2f8350d9-0c76-473d-953c-14e94d44c0de.gif)

Như bạn thấy đấy, output thì hoàn toàn đúng, chiều cao của cột đã thay đổi, nhưng nó quá là thô, chẳng smoth tí nào cả. Quả này mà chấm điểm là 1/10 thôi. 

Để khác phục điều này, Flutter cung cấp cho ta 1 widget là `AnimatedContainer`. Widget này kế thừa từ Container nên đương nhiên nó cũng có các thuộc tính `height`, `color`, ngoài ra nó còn có thêm `duration` để làm cho hiệu ứng được đẹp hơn. Bây giờ, code sẽ được update lại như sau: 

```
AnimatedContainer(
  duration: Duration(seconds: 5),
  width: 60.0,
  height: height,
  color: Color(0xff14ff65),
)
```

![](https://images.viblo.asia/c291277f-689a-4fe7-b9ff-ff8495ae2421.gif)

Kết quả thật đáng kinh ngạc phải không nào, thật là vi diệu đó.

### Ví dụ 2: Take Flight

Vẫn tiếp tục với widget `AnimatedContainer`, bạn có thể mở rộng sâu hơn với các thuộc tính khác của nó. Ở đây, mình muốn sử dụng `alignment`. Và `AnimatedContainer`có child là 1 `Container`. Ý tưởng của mình là sẽ làm di chuyển Container child sử dụng thuộc tính `alignment`.

Đây là đoạn code xử lý và kết quả:

``` java
import 'package:flutter/material.dart';

class FlightExample extends StatefulWidget {
  @override
  FlightExampleState createState() {
    return new FlightExampleState();
  }
}

class FlightExampleState extends State<FlightExample> {

  var _alignment = Alignment.bottomCenter;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: AnimatedContainer(
        padding: EdgeInsets.all(10.0),
        duration: Duration(seconds: 2),
        alignment: _alignment,
        child: Container(
          height: 50.0,
          child: Icon(Icons.airplanemode_active, size: 50.0, color: Colors.blueAccent,),
        ),
      ),
      floatingActionButtonLocation: FloatingActionButtonLocation.centerFloat,
      floatingActionButton: FloatingActionButton.extended(
        backgroundColor: Colors.blueAccent,
          onPressed: (){
            setState(() {
              _alignment = Alignment.center;
            });
          },
          icon: Icon(Icons.airplanemode_active),
          label: Text("Take Flight")),
    );
  }
}
```

![](https://images.viblo.asia/c9d67309-5696-4edd-9106-961e41b98b1e.gif)

Bắt mắt phải không nào các bạn =))

### Ví dụ 3 - Playing with lots of colors

Không phải 1 màu và rất nhiều màu. Và tiếp theo, chúng ta sẽ làm animation với thuộc tính `gradient`

Đây là đoạn code xử lý và kết quả:

```java
import 'package:flutter/material.dart';

class GradientTransform extends StatefulWidget {
  @override
  GradientTransformState createState() {
    return new GradientTransformState();
  }
}

class GradientTransformState extends State<GradientTransform> {

  var top = FractionalOffset.topCenter;
  var bottom = FractionalOffset.bottomCenter;
  var list = [
    Colors.lightGreen,
    Colors.redAccent,
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(

      body: Center(
        child: AnimatedContainer(
          height: 300.0,
          width: 300.0,
          duration: Duration(seconds: 1),
          decoration: BoxDecoration(
              borderRadius: BorderRadius.circular(10.0),
              gradient: new LinearGradient(
                begin: top,
                end: bottom,
                colors: list,
                stops: [0.0, 1.0],
              ),
              color: Colors.lightGreen
          ),
        ),
      ),

      floatingActionButtonLocation: FloatingActionButtonLocation.centerFloat,
      floatingActionButton: FloatingActionButton.extended(
          onPressed: (){
            setState(() {
              top = FractionalOffset.bottomLeft;
              bottom = FractionalOffset.topRight;
              list = [
                Colors.blueAccent, Colors.yellowAccent
              ];
            });
          },
          icon: Icon(Icons.update),
          label: Text("Transform")),
    );
  }
}
```

![](https://images.viblo.asia/5be0ed90-f0e9-4138-b6d1-3f15611c626f.gif)

Mọi thứ vẫn trong tầm kiểm soát phải không nào các bạn.

Hy vọng với 3 ví dụ trên bạn sẽ có cái nhìn cơ bản nhất về Animation trong Flutter. và **Fall in Love with Flutter**

Phần tiếp theo mình sẽ trình bày về `AnimatedOpacity`

[Tài liệu tham khảo tại đây](https://medium.com/flutter-community/flutter-animated-series-animated-containers-52a5d52c0ad3)
