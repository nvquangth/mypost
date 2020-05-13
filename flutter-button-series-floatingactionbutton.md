Hi everyone, chào mừng các bạn đến với series về Button trong Flutter, bài viết hôm nay mình sẽ giới thiệu về **FloatingActionButton**

1. ButtonBar
2. DropdownButton
3. FlatButton
4. **FloatingActionButton**
5. IconButton
6. PopupMenuButton
7. RaisedButton

![](https://images.viblo.asia/0b5cb1c3-0704-4a0b-a79e-8665f59e8a4a.png)

FloatingActionButton là 1 trong số button thuộc material disign. Nó là 1 icon button hình tròn, thường được thiết kế nằm phía trên 1 nội dung nào đó để làm nổi bật 1 tính năng nào đó trong ứng dụng. FloatingActionButton thường xuyên được sử dụng trong [Scaffold](https://api.flutter.dev/flutter/material/Scaffold-class.html) với thuộc tính floatingActionButton

{@embed: https://www.youtube.com/watch?v=2uaoEDOgk_I}

Mỗi màn hình nếu có sử dụng FloatingActionButton thì chỉ sử dụng 1 button duy nhất với các mục đích như "create", "share", "navigate"

Ví dụ như này: 

![](https://images.viblo.asia/85860bc1-3af8-44b2-b7a6-293551249052.png)
![](https://images.viblo.asia/c824663e-edc8-4657-b8e3-d6c59c59a7c2.png)

Điểm qua các thuộc tính của FloatingActionbutton


| Thuộc tính | Ý nghã |
| -------- | -------- |
|   backgroundColor   |   Xác định màu nền cho button, màu mặc định là  [ThemeData.accentColor](https://api.flutter.dev/flutter/material/ThemeData/accentColor.html)   |
|   child  |  Cho phép định nghĩa các widget con bên trong nó    |
|  clipBehavor    |   Nội dung bên trong sẽ bị cắt xén theo clip widget này   | 
|  disabledElevation    |  Xác định kích thước của shadow khi button bị disable (onPressed có giá trị là null). Mặc định nó có cùng giá trị với elevation. Nếu bạn set cho nó giá trị là 0, thì lúc này FloatingActionButton sẽ tương tự như RaisedButton và sẽ mất hiệu ứng "floating". Giá trị của thuộc tính này là không âm.    |
|  elevation    |  Xác định kích thước shadow cho button, giá trị của elevation là không âm và mặc định là 6   | 
|  focusColor    |   Xác định màu sắc của button khi nó được focus, giá trị mặc định của nó là [ThemeData.focusColor](https://api.flutter.dev/flutter/material/FloatingActionButton/focusColor.html)   |
|  focusElevation    |   Xác định kích thước shadow bên dưới floating action button, giá trị  của nó là không âm và mặc định là 8   | 
|  focusNode    |   Xem thêm tại [đây](https://api.flutter.dev/flutter/material/FloatingActionButton/focusNode.html)   | 
|  foregroundColor    |  Xác định màu sắc cho icon và text bên trong button    | 
|  heroTag    |  Được sử dụng với Hero widget, xem thêm tại [đây](https://api.flutter.dev/flutter/material/FloatingActionButton/heroTag.html)    | 
|  highlightElevation    |   Xem thêm tại [đây](https://api.flutter.dev/flutter/material/FloatingActionButton/highlightElevation.html)   |  
|  hoverColor    |   Xác định màu sắc của button khi con trỏ hover vào nó   | 
|  hoverElevation    |   Xem thêm tại [đây](https://api.flutter.dev/flutter/material/FloatingActionButton/hoverElevation.html)   | 
|  isExtended    |   Cho phép 1 widget extend nó hay không   | 
|  materialTabTargetSize    |   Cấu hình kích thước nhỏ khi tapped vào button   | 
|  mini    |   Xác định kích thước của button. Mặc định floating action button là non-mini và có width và height là 56.0 px. Mini floating action button sẽ có width và hight là 40.0 px và thêm 4.0 padding -> width và height là 48.0 px   | 
|  onPressed    |   Đây là callback khi có sự kiện tapped vào button. Nếu nó có giá trị null thì button bị disable   |  
|  shape    |   Xác định hình dạng cho button, splash và highlight của button cũng được cắt theo shape này. Nếu button có elevation thì nó cũng được định nghĩa theo shape này   | 
|  tooltip    |  Dùng để định nghĩa mô tả khi có action press vào button. Nó sẽ xuất hiên nếu người dùng long-pressed   | 


Trên đây mình đã giới thiệu với các bạn những cái cơ bản nhất về FloatingActionButton. Đến đây chắc bạn cũng đã nắm được phần nào về nó rồi phải không nào. Và sau đây mình sẽ làm 1 số đoạn code nhỏ để minh họa nha.

```dart
// Flutter code sample for material.FloatingActionButton.1

// This example shows how to make a simple [FloatingActionButton] in a
// [Scaffold], with a pink [backgroundColor] and a thumbs up [Icon].

import 'package:flutter/material.dart';

void main() => runApp(MyApp());

/// This Widget is the main application widget.
class MyApp extends StatelessWidget {
  static const String _title = 'Flutter Code Sample';

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: _title,
      home: MyStatelessWidget(),
    );
  }
}

/// This is the stateless widget that the main application instantiates.
class MyStatelessWidget extends StatelessWidget {
  MyStatelessWidget({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Floating Action Button Sample'),
      ),
      body: Center(child: Text('Press the button below!')),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          // Add your onPressed code here!
        },
        child: Icon(Icons.thumb_up),
        backgroundColor: Colors.pink,
      ),
    );
  }
}
```

![](https://images.viblo.asia/630b6533-5d51-4492-9396-607b23930176.gif)
OK, vậy là bài giới thiệu của mình về FloatingActionButton đến đây là kết thúc. Hẹn gặp lại ở các bài viết khác nha. Happy coding :D

Series:

1. ButtonBar
2. DropdownButton
3. FlatButton
4. [FloatingActionButton](https://viblo.asia/p/flutter-button-series-floatingactionbutton-1VgZv1dpKAw)
5. [IconButton](https://viblo.asia/p/flutter-button-series-iconbutton-WAyK84jmKxX)
6. PopupMenuButton
7. RaisedButton 

Tài liệu tham khảo: [FloatingActionButton class](https://api.flutter.dev/flutter/material/FloatingActionButton-class.html)
