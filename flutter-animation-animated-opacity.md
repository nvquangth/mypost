Bài viết hôm trước mình có giới thiệu về [Flutter Animation: Animated Containers](https://viblo.asia/p/flutter-animation-animated-containers-LzD5dXjz5jY). Hôm nay mình sẽ tiếp tục giới thiệu thêm về 

> Flutter Animation : Animated Opacity

![](https://images.viblo.asia/2730095d-e4d2-4eef-82fa-d954797bf6e5.gif)

Chắc hẳn đối với những bạn làm việc nhiều với các ứng dụng giao diện, hay các bạn làm designer đã quen thuộc với khai niệm **Opacity** phải không nào? Opacity được hiểu nôm na là 1 thuộc tính/ đối tượng dùng để mô tả sự trong suốt cho 1 đối tương hình ảnh hoặc màu nền. Giá trị của nó nằm trong đoạn **[0, 1]**.

Nếu sử dụng giá trị max của Opacity là 1.0 thì đối tượng sẽ hiển thị đầy đủ.

Nếu sử dụng giá trị min của Opacity là 0.0 thì đối tượng sẽ không nhìn thấy được (trong suốt).

Lý thuyết nhìn chung nó là vậy, Opacity đối với Flutter cũng không có gì khác cả, chỉ là chúng ta cùng xem cách triển khai và sử dụng nó như nào thôi.

### Let's go

Chúng ta sẽ làm 1 ví dụ đơn giản như này nhé. Ứng dụng bao gồm 1 Button #Hashtag, khi click vào button thì Opacity của text sẽ giảm dần từ 1.0 về 0.0

Đầu tiên, tạo giao diện có 1 button như này nhé
```java
Center(
      child: Container(
        alignment: Alignment.center,
        height: 50.0,
        width: 250.0,
        decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(25.0),
          color: Colors.blueGrey
        ),
        child: Text(
          '#Hashtag',
          style: TextStyle(color: Colors.white, fontSize: 20.0),
        ),
      ),
    );
```

Okay, lúc này ta đã có 1 Button phải không nào
![](https://images.viblo.asia/02ba4d5b-c4f8-4dde-a1b1-61371163758c.png)

Tiếp theo, chúng ta sẽ thêm Opacity cho nó, bằng cách wrap Text widget bởi AnimatedOpacity widget, và wrap Container widget trong GestureDetector widget để bắt sự kiện onTap() vào button.

```java
GestureDetector(
        onTap: () {
          setState(() {
            _opacity = _opacity == 0.0 ? 1.0 : 0.0;
          });
        },
        child: Container(
          alignment: Alignment.center,
          height: 50.0,
          width: 250.0,
          decoration: BoxDecoration(
            borderRadius: BorderRadius.circular(25.0),
            color: Colors.blueGrey
          ),
          child: AnimatedOpacity(
            duration: Duration(seconds: 1),
            opacity: _opacity,
            child: Text(
              '#Hashtag',
              style: TextStyle(color: Colors.white, fontSize: 20.0),
            ),
          ),
        ),
      ),
    );
```

OK, đến lúc này reload lại app thì chúng ta sẽ thu được kết quả như sau:
![](https://images.viblo.asia/874d70b2-5542-4848-8a0b-15c251892cf3.gif)

Đến đây, coi như chúng ta đã biết cách sử dụng cơ bản của Animated Opacity rồi đấy.

Vẫn với ví dụ này, mình sẽ kết hợp với Animated Containers (bài viết trước mình đã đề cập ở [đây](https://viblo.asia/p/flutter-animation-animated-containers-LzD5dXjz5jY)) cho nó thêm sinh động.

```java
GestureDetector(
        onTap: () {
          setState(() {
            _opacity = _opacity == 0.0 ? 1.0 : 0.0;
            _width = _width == 50.0 ? 200.0 : 50.0;
          });
        },
        child: AnimatedContainer(
          duration: Duration(milliseconds: 1000),
          alignment: Alignment.center,
          height: 50.0,
          width: _width,
          decoration: BoxDecoration(
            borderRadius: BorderRadius.circular(25.0),
            color: Colors.blueGrey
          ),
          child: AnimatedOpacity(
            duration: Duration(milliseconds: 1000),
            opacity: _opacity,
            child: Text(
              '#Hashtag',
              style: TextStyle(color: Colors.white, fontSize: 20.0),
            ),
          ),
        ),
      ),
    );
```

Và đây là kết quả, cũng khá đơn giản phải không nào

![](https://images.viblo.asia/977db28a-be90-4a9c-8c79-047a79f81832.gif)

Bonus: bạn cũng có thể làm cho demo này lung linh hơn 1 chút nữa bằng việc thêm 1 số logic biến đổi như sau:
```java
class ButtonOpacityState extends State<ButtonOpacity> {
  bool _isAdded = false;

  @override
  Widget build(BuildContext context) {
    return InkWell(
      onTap: () {
        setState(() {
          _isAdded = !_isAdded;
        });
      },
      child: AnimatedContainer(
        duration: Duration(milliseconds: 350),
        curve: Curves.linear,
        decoration: BoxDecoration(
          color: _isAdded ? Colors.transparent : Colors.blue,
          borderRadius: BorderRadius.circular(30),
          border: Border.all(width: 3.0, color: Colors.blue),
        ),
        width: _isAdded ? 160 : 60,
        height: 60,
        child: Stack(
          children: <Widget>[
            Align(
              alignment: Alignment.center,
              child: AnimatedOpacity(
                  duration: Duration(milliseconds: 600),
                  curve: Curves.easeIn,
                  opacity: _isAdded ? 0.0 : 1.0,
                  child: Icon(Icons.person_add, color: Colors.white)),
            ),
            Align(
              alignment: Alignment.center,
              child: AnimatedOpacity(
                duration: Duration(milliseconds: 550),
                opacity: _isAdded ? 1.0 : 0.0,
                child: Text(
                  "MESSAGE",
                  style: TextStyle(
                      fontSize: 18.0,
                      color: Colors.blue,
                      fontWeight: FontWeight.bold),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }

```
Và đây là kết quả

![](https://images.viblo.asia/54ab075f-bcb3-4f56-aa2d-def7eb8249fc.gif)

OK, vậy là bài giới thiệu về Animated Opacity của mình đến đây là hết, chúc các bạn làm được như ý muốn. Happy coding =)

Tài liệu tham khảo:

[AnimatedOpacity class](https://api.flutter.dev/flutter/widgets/AnimatedOpacity-class.html)

[Flutter Animated Series : Animated Opacity](https://medium.com/flutter-community/flutter-animated-series-animated-opacity-c11137883a8d)
