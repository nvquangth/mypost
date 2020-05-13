Hi everyone, chào mừng các bạn đến với series về Button trong Flutter, bài viết hôm nay mình sẽ giới thiệu về **PopupMenuButton**

1. ButtonBar
2. DropdownButton
3. FlatButton
4. FloatingActionButton
5. IconButton
6. **PopupMenuButton**
7. RaisedButton

![](https://images.viblo.asia/7d929a1a-50ed-4f6c-8de1-c11a0f98c4c2.png)

Popup menu dùng để hiển thị 1 danh sách các item dạng popup, khi 1 item được lựa chọn thì popup menu sẽ biến mất. Giá trị của item được chọn sẽ pass qua callback onSelected().

Điểm qua các thuộc tính của PopupMenuButton



| Thuộc tính | Ý nghĩa |
| -------- | -------- |
|  child    |   Nếu thuộc tính này được cung cấp, thì widget sẽ được sử dụng cho button   |
|  elevation    |   xem thêm tại [đây](https://api.flutter.dev/flutter/material/PopupMenuButton/elevation.html)   |
|  enable    |  Thuộc tính này là non-null, có giá trị mặc định là true, lúc này nó có thể tương tác được. Nếu thuộc tính này được set giá trị là false, popup sẽ ở trạng thái không thể tương tác được, và được set style là disable từ Theme hiện tại đang sử dụng. onSelected, onCanceled, itemBuilder sẽ không được gọi nữa    |
|  icon    |  Nếu thuộc tính này được cung cấp, thì  icon sẽ được sử dụng cho button   |
|  initalValue    |   Khởi tạo giá trị hiển thị mặc định cho popup menu, đồng thời nó sẽ được bôi đậm trong danh sách item khi được show   |
|  itemBuilder    |   callback này được gọi khi click vào button để tạo ra danh sách các item   |
|  offset    |   xem thêm tại [đây](https://api.flutter.dev/flutter/material/PopupMenuButton/offset.html)   |
|  onCanceled    |  callback này được gọi khi popup biến mất mà không select vào item nào    |
|  onSelected    |   call back này được gọi khi popup biến mất đồng thời 1 item được select   |
|  padding    |   thiết lập padding cho icon của button   |
|  tooltip    |   Dùng để định nghĩa mô tả khi có action press vào button. Nó sẽ xuất hiên nếu người dùng long-pressed    |


Trên đây mình đã giới thiệu với các bạn những cái cơ bản nhất về PopupMunuButton. Đến đây chắc bạn cũng đã nắm được phần nào về nó rồi phải không nào. Và sau đây mình sẽ làm 1 số đoạn code nhỏ để minh họa nha.

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  static const String _title = 'Flutter Code Sample';

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: _title,
      home: MyPage(),
    );
  }
}

class MyPage extends StatefulWidget {
  @override
  MyPageState createState() => MyPageState();
}

class MyPageState extends State<MyPage> {
  var _selection;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Popup Button Sample'),
      ),
      body: _buildBody(),
    );
  }

  Widget _buildBody() {
    return Column(
      children: <Widget>[_buildContent(), _buildPopupMenu()],
    );
  }

  Widget _buildPopupMenu() {
    return PopupMenuButton<int>(
      onSelected: (int result) {
        setState(() {
          _selection = result;
        });
      },
      itemBuilder: (BuildContext context) => <PopupMenuEntry<int>>[
            const PopupMenuItem<int>(
              value: 1,
              child: Text('Number one'),
            ),
            const PopupMenuItem<int>(
              value: 2,
              child: Text('Number two'),
            ),
            const PopupMenuItem<int>(
              value: 3,
              child: Text('Number three'),
            ),
            const PopupMenuItem<int>(
              value: 4,
              child: Text('Number four'),
            ),
          ],
    );
  }

  Widget _buildContent() {
    return Center(
      child: Padding(
        padding: const EdgeInsets.all(64.0),
        child: Text(
          _selection.toString(),
          style: TextStyle(fontSize: 64.0, color: Colors.pinkAccent),
        ),
      ),
    );
  }
}

```

![](https://images.viblo.asia/55bfbaf6-6ea3-487e-825a-912764eb7913.gif)

OK, vậy là bài giới thiệu của mình về PopupMenuButton đến đây là kết thúc. Hẹn gặp lại ở các bài viết khác nha. Happy coding :D

Series:

1. ButtonBar
2. DropdownButton
3. FlatButton
4. [FloatingActionButton](https://viblo.asia/p/flutter-button-series-floatingactionbutton-1VgZv1dpKAw)
5. [IconButton](https://viblo.asia/p/flutter-button-series-iconbutton-WAyK84jmKxX)
6. [PopupMenuButton](https://viblo.asia/p/flutter-button-series-popupmenubutton-gAm5yWYVZdb)
7. RaisedButton 

Tài liệu tham khảo:
[PopupMenuButton.class](https://api.flutter.dev/flutter/material/PopupMenuButton-class.html)
