Hi everyone, chào mừng các bạn đến với series về Button trong Flutter, bài viết hôm nay mình sẽ giới thiệu về **IconButton**

1. ButtonBar
2. DropdownButton
3. FlatButton
4. FloatingActionButton
5. **IconButton**
6. PopupMenuButton
7. RaisedButton

![](https://images.viblo.asia/2b044de8-28c4-4e1a-ad1d-b7fa5ad34d73.png)

IconButton là 1 trong số button thuộc material design

1 IconButton là 1 hình ảnh được định nghĩa sẵn trong [Material](https://api.flutter.dev/flutter/material/Material-class.html) widget, khi người dùng click vào nó sẽ tạo ra hiệu ứng fill with color như hỉnh trên giúp tạo cảm giác tốt về UX.

IconButton hay được sử dụng trong thuộc tính action của [AppBar](https://api.flutter.dev/flutter/material/AppBar-class.html) widget. Tuy nhiên, bạn có thể sử dụng nó với nhiều mục đích tuyệt vời hơn nữa.

Điểm qua các thuộc tính của IconButton



| Thuộc tính| Ý nghĩa |
| ---------------- | -------- |
| alignment     | Xác định vị trí của icon bên trong IconButton, thuộc tính này là non-null, giá trị mặc định là **[Alignment.center](https://api.flutter.dev/flutter/painting/Alignment-class.html#constants)**     |
| color     | Xác định màu sắc của icon bên trong IconButton     |
| disabledColor      | Xác định màu sắc của icon bên trong IconButton nếu nó bị disable. IconButton bị disable khi onPress có giá trị null     |
| focusColor      | Xác định màu sắc của icon bên trong IconButton khi nó được focus     |
| focusNode     | Xác định vị trí của icon bên trong IconButton     |
|   highlightColor  |  Xác định màu sắc của button khi press vào, highlight color được thể hiện bởi 1 lớp màu phủ lên button. Nếu highlight color là trong suốt thì bạn sẽ nhìn thấy màu bên dưới của button. Highlight color sẽ xuất hiện và mất dần như hiệu ứng fade  |
|   hoverColor  |  Xác định màu sắc của button khi con trỏ hover vào nó  |
|  icon   |  Xác định icon hiển thị bên trong button, thuộc tính này là non-null  | 
|  iconSize   |  Xác định kích thước của icon bên trong button, thuộc tính này là non-null và giá trị mặc định là 24.0  |
|   onPressed  |  Đây là callback khi có sự kiện tapped vào button. Nếu nó có giá trị null thì button bị disable  |
|   padding  |  Xác định padding xung quanh icon của button, toàn bộ padding này sẽ được tính vào vùng tapped của button. Thuộc tính này là non-null và giá trị mặc định là 8.0  |
|   splashColor  |  Xác định màu chính của button khi nó ở trạng thái pressed. splashColor thể hiện 1 lớp phủ vòng tròn xuất hiện bên trên lớp phủ của highlightColor. Tâm của lớp phủ chính là điểm mà người dùng chạm vào, nó sẽ tạo ra 1 lớp phủ hình tròn fill hết button nếu người dùng vẫn giữ press trong suốt thời gian đó. Nếu splashColor là trong suốt thì bạn sẽ nhình thấy highlight và color của button  | 
|  tooltip   |  Dùng để định nghĩa mô tả khi có action press vào button. Nó sẽ xuất hiên nếu người dùng long-pressed  | 

Trên đây mình đã giới thiệu với các bạn những cái cơ bản nhất về IconButton. Đến đây chắc bạn cũng đã nắm được phần nào về nó rồi phải không nào. Và sau đây mình sẽ làm 1 số đoạn code nhỏ để minh họa nha.

```dart
// Flutter code sample for material.IconButton.1

// This sample shows an `IconButton` that uses the Material icon "volume_up" to
// increase the volume.

import 'package:flutter/material.dart';

void main() => runApp(MyApp());

/// This Widget is the main application widget.
class MyApp extends StatelessWidget {
  static const String _title = 'Flutter Code Sample';

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: _title,
      home: Scaffold(
        appBar: AppBar(title: const Text(_title)),
        body: MyStatefulWidget(),
      ),
    );
  }
}

double _volume = 0.0;

class MyStatefulWidget extends StatefulWidget {
  MyStatefulWidget({Key key}) : super(key: key);

  @override
  _MyStatefulWidgetState createState() => _MyStatefulWidgetState();
}

class _MyStatefulWidgetState extends State<MyStatefulWidget> {
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: <Widget>[
            IconButton(
              icon: Icon(Icons.volume_up),
              tooltip: 'Increase volume by 10',
              onPressed: () {
                setState(() {
                  _volume += 10;
                });
              },
            ),
            Text('Volume : $_volume')
          ],
        ),
      ),
    );
  }
}
```

![](https://images.viblo.asia/d47f542f-0b1f-4f74-9aff-21d403994b64.gif)
OK, vậy là bài giới thiệu của mình về IconButton đến đây là kết thúc. Hẹn gặp lại ở các bài viết khác nha. Happy coding :D

Series:

1. ButtonBar
2. DropdownButton
3. FlatButton
4. [FloatingActionButton](https://viblo.asia/p/flutter-button-series-floatingactionbutton-1VgZv1dpKAw)
5. [IconButton](https://viblo.asia/p/flutter-button-series-iconbutton-WAyK84jmKxX)
6. PopupMenuButton
7. RaisedButton 

Tài liệu tham khảo:
[IconButton class](https://api.flutter.dev/flutter/material/IconButton-class.html)
