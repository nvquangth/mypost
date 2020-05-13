![](https://images.viblo.asia/0a128f26-66d5-410d-bfa4-38dc9696ca1c.png)


Thiết kế trên các thiết bị điện thoại (cả Android và Ios) để có những đường viền thừa (phần này có thể được hiểu là thanh status bar) - còn gọi là system intrusions. Lý do chính cho thiết kế này là các nhà sản xuất luôn cố gắng tìm cách tạo ra những thiết kế mới bằng việc thay đổi những phần tử được đặt ở đây. Vì vậy phần này nó chiếm 1 phần diện tích trong ứng dụng của bạn. Mặc định khi bạn dựng giao diện, thì nó sẽ bị chồng chéo lên phần status bar này, Flutter cung cấp 1 widget giúp bạn có thể giải quyết điều này, đó chính là SafeArea Widget.

### 1. What?
Bạn chỉ cần đơn giản là wrap layout chính của bạn bởi SafeArea widget, thì toàn bộ layout mà bạn viết trong nó sẽ được nằm dưới hay nói cách khác là bắt đầu ngay bên dưới thanh status bar.



|Thuộc tính | Ý nghĩa  |
| -------- | -------- |
| bottom     | Tránh system intrusions ở bottom của màn hình     |
| left |  Tránh system intrusions ở left của màn hình |
| top     | Tránh system intrusions ở top của màn hình   (status bar)  |
| right |  Tránh system intrusions ở right của màn hình |
| child | Chứa các widget con, thường là main layout|
|minimum | padding nhỏ nhất được áp dụng |
|maintainBottomViewPadding | chỉ định xem SafeArea có nên duy trì viewpadding thay vì padding khi được viewInsets của context MediaQuery sử dụng, giá trị mặc định là false|

### 2. Wondering how it workds?
Dưới đây là 1 ví dụ
```dart
class SafeAreaWidget extends StatefulWidget {
  @override
  _SafeAreaWidgetState createState() => _SafeAreaWidgetState();
}

class _SafeAreaWidgetState extends State<SafeAreaWidget> {
  ///Bool value to control the behaviour of SafeArea widget.
  bool _isEnabled = true;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        top: _isEnabled,
        bottom: _isEnabled,
        child: Column(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          mainAxisSize: MainAxisSize.max,
          children: <Widget>[
            Container(
              width: MediaQuery.of(context).size.width,
              color: Colors.blue,
              child: Text(
                "This widget is below safe area. If you remove the SafeArea "
                    "widget then this text will be behind the notch.",
                textAlign: TextAlign.center,
                style: TextStyle(color: Colors.white),
              ),
            ),

            ///Press this button to toggle the value of _isEnabled variable
            RaisedButton(
              textColor: Colors.white,
              color: Colors.indigo,
              onPressed: () => setState(() {
                    _isEnabled == true ? _isEnabled = false : _isEnabled = true;
                  }),
              child: Text(_isEnabled ? "Disable SafeArea" : "Enable SafeArea"),
            ),

            Container(
              width: MediaQuery.of(context).size.width,
              color: Colors.blue,
              child: Text(
                "This widget is above safe area",
                textAlign: TextAlign.center,
                style: TextStyle(color: Colors.white),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```
Và đây là kết quả
![](https://images.viblo.asia/753fc437-214a-46e1-825f-93a1661791cc.gif)

Ví dụ 2:
Trước:

![](https://images.viblo.asia/cbb39993-4208-4d27-85ca-d0d285891025.png)
![](https://images.viblo.asia/421d53f0-b79b-42c6-851c-436959e83a31.png)

Sau: 

![](https://images.viblo.asia/7625e380-dff8-4189-9b8a-de271f3a0ccc.png)
![](https://images.viblo.asia/b5340130-0167-463e-a532-e6e2b90cf64c.png)


### 3. Explanation
Tóm lại, SafeArea đơn giản cũng chỉ để phục vụ duy nhất mục đích như trên, không còn mục đích nào khác, nó quá đơn giản để hiểu và implement phải không nào

Một số ví dụ thêm:

{@embed: https://www.youtube.com/watch?v=lkF0TQJO0bA}

[SafeArea Class](https://api.flutter.dev/flutter/widgets/SafeArea-class.html)
