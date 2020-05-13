![](https://user-images.githubusercontent.com/22972057/70401088-bf7c5300-1a60-11ea-99f9-78160a6cc4f3.gif)

Hi all, hôm nay tôi sẽ hướng dẫn các bạn tạo 1 UI đẹp + đơn giản như hình trên với Flutter (design được lấy [tại đây](https://dribbble.com/shots/1956586-Filter-Menu)), các kiến thức cơ bản sử dụng bao gồm:
1. FloatingActionButton
2.  AnimatedListView
3.  GestureDetector

Để làm được UI như trên, tôi chia thành các bước nhỏ để hoàn thành:
1. The clipped image on top
2. Header
3. Profile view
4. List header
5. List of items
6. Animating items filltering
7. Animated FloatingActionButton

## 0. Starting point
Đầu tiên tôi tạo 1 ứng dụng đơn giản với Stack, dùng Stack làm container để chứa tất cả các Widget sử dụng trong app.
```dart
import 'package:flutter/material.dart';

void main() => runApp(new MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Flutter Demo',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new MainPage(),
    );
  }
}

class MainPage extends StatefulWidget {
  MainPage({Key key}) : super(key: key);

  @override
  _MainPageState createState() => new _MainPageState();
}

class _MainPageState extends State<MainPage> {
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      body: new Stack(
        children: <Widget>[],
      ),
    );
  }
}
```
## 1. The clipped image on top
Tôi để sẵn 1 i[mage ](https://raw.githubusercontent.com/nvquangth/beautiful-UI-1/master/assets/images/birds.jpg)trong assets dùng làm background top, việc còn lại đơn giản là hiển thị image lên UI.
```dart
class _MainPageState extends State<MainPage> {
  double _imageHeight = 256.0;
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      body: new Stack(
        children: <Widget>[
          _buildIamge()
        ],
      ),
    );
  }

  Widget _buildIamge() {
    return new Image.asset(
        'images/birds.jpg',
        fit: BoxFit.fitHeight,
        height: _imageHeight,
    );
  }
}
```
![](https://fidev.io/wp-content/uploads/2018/05/ff_1.png)
Bây giờ tôi sẽ thực hiện clipped image này với CustomClipper
```dart
class DialogonalClipper extends CustomClipper<Path> {
  @override
  Path getClip(Size size) {
    Path path = new Path();
    path.lineTo(0.0, size.height - 60.0);
    path.lineTo(size.width, size.height);
    path.lineTo(size.width, 0.0);
    path.close();
    return path;
  }

  @override
  bool shouldReclip(CustomClipper<Path> oldClipper) => true;
}
```
p/s: nếu bạn nào đã từng làm việc với path trong java hay bất kì ngôn ngữ nào khác, thì với Flutter path nó cũng như vậy, cơ chế hoạt động, rồi các function như lineTo() đề như thế cả.

Class mới extends từ CustomClipper< Path> và sẽ override 2 method `shouldReclip` - sẽ cho framework biết khi nào clipper được render, trong trường hợp này tôi luôn để là true và `getClip` - trả về 1 path mô tả khu vực visible với UI.

Sau khi đã định nghĩa clipper, tôi sẽ sửa lại function `_buildImage()`
```dart
Widget _buildIamge() {
  return new ClipPath(
    clipper: new DialogonalClipper(),
    child: new Image.asset(
        'images/birds.jpg',
        fit: BoxFit.fitHeight,
        height: _imageHeight,
    ),
  );
}
```
Theo như design thì bức ảnh có vẻ tối hơn, hãy sửa thêm 1 chút code để sao cho UI giống nhất có thể bằng việc sử dụng color blending
```dart
new Image.asset(
  'images/birds.jpg',
  fit: BoxFit.fitHeight,
  height: _imageHeight,
  colorBlendMode: BlendMode.srcOver,
  color: new Color.fromARGB(120, 20, 10, 40),
),
```
![](https://fidev.io/wp-content/uploads/2018/05/ff_3.png)
## 2. Header
Phần này thì tôi không đặt độ ưu tiên cao cho nó vì đây không phải là trọng tâm trong bài viết này của tôi, nên tôi chỉ sử dụng 1 vài widget đơn giản để làm như: row, icons, text
```dart
Widget _buildTopHeader() {
  return new Padding(
    padding: const EdgeInsets.symmetric(horizontal: 8.0, vertical: 32.0),
    child: new Row(
      children: <Widget>[
        new Icon(Icons.menu, size: 32.0, color: Colors.white),
        new Expanded(
          child: new Padding(
            padding: const EdgeInsets.only(left: 8.0),
            child: new Text(
              "Timeline",
              style: new TextStyle(
                  fontSize: 20.0,
                  color: Colors.white,
                  fontWeight: FontWeight.w300),
            ),
          ),
        ),
        new Icon(Icons.linear_scale, color: Colors.white),
      ],
    ),
  );
}
```
Đến đây cấu trúc app bởi stack sẽ như thế này
```dart
@override
Widget build(BuildContext context) {
  return new Scaffold(
    body: new Stack(
      children: <Widget>[
        _buildIamge(),
        _buildTopHeader(),
      ],
    ),
  );
}
```
![](https://fidev.io/wp-content/uploads/2018/05/ff_4.png)
## 3. Profile view
Cũng như header view thì đây cũng không phải là trọng tâm nên tôi cũng xin lướt qua nhanh phần này với các widget hết sức đơn giản: image, padding
```dart
Widget _buildProfileRow() {
  return new Padding(
    padding: new EdgeInsets.only(left: 16.0, top: _imageHeight / 2.5),
    child: new Row(
      children: <Widget>[
        new CircleAvatar(
          minRadius: 28.0,
          maxRadius: 28.0,
          backgroundImage: new AssetImage('images/avatar.jpg'),
        ),
        new Padding(
          padding: const EdgeInsets.only(left: 16.0),
          child: new Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            mainAxisSize: MainAxisSize.min,
            children: <Widget>[
              new Text(
                'Ryan Barnes',
                style: new TextStyle(
                    fontSize: 26.0,
                    color: Colors.white,
                    fontWeight: FontWeight.w400),
              ),
              new Text(
                'Product designer',
                style: new TextStyle(
                    fontSize: 14.0,
                    color: Colors.white,
                    fontWeight: FontWeight.w300),
              ),
            ],
          ),
        ),
      ],
    ),
  );
}
```
Lúc này cấu trúc stack như sau
```dart
new Stack(
  children: <Widget>[
    _buildIamge(),
    _buildTopHeader(),
    _buildProfileRow(),
  ],
),
```
![](https://fidev.io/wp-content/uploads/2018/05/ff_5.png)
## 4. List header
Phần này bắt đầu có mức độ cao hơn, và tôi đang dần dần hoàn thiện Ui theo như design. Phần tiêu đề và danh sách luôn ở dưới hình ảnh, tôi sẽ định nghĩa 1 function `_buildBottomPart()` - mọi thứ của header sẽ được xây dựng trong function này.
```dart
Widget _buildBottomPart() {
  return new Padding(
    padding: new EdgeInsets.only(top: _imageHeight),
    child: new Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: <Widget>[
        _buildMyTasksHeader(),
        _buildTasksList(),
      ],
    ),
  );
}

//TODO
Widget _buildTasksList() {
  return new Container();
}

Widget _buildMyTasksHeader() {
  return new Padding(
    padding: new EdgeInsets.only(left: 64.0),
    child: new Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: <Widget>[
        new Text(
          'My Tasks',
          style: new TextStyle(fontSize: 34.0),
        ),
        new Text(
          'FEBRUARY 8, 2015',
          style: new TextStyle(color: Colors.grey, fontSize: 12.0),
        ),
      ],
    ),
  );
}
```
Đây là cấu trúc của stack
```dart
new Stack(
  children: <Widget>[
    _buildIamge(),
    _buildTopHeader(),
    _buildProfileRow(),
    _buildBottomPart(),
  ],
),
```
![](https://fidev.io/wp-content/uploads/2018/05/ff_6.png)
## 5. List of items
Đầu tiên hãy tạo 1 model là Task sẽ đại diện cho mỗi item trong task list. Task bao gồm các thuộc tính như name, category, time, color và task có thể lọc theo tiêu chí là hoàn thành hay chưa hoàn thành, do vậy sẽ có thêm thuộc tính completed.
```dart
class Task {
  final String name;
  final String category;
  final String time;
  final Color color;
  final bool completed;

  Task({this.name, this.category, this.time, this.color, this.completed});
}
```
Dữ liệu về task tôi tạm thời fix cứng trong code như thế này
```dart
List<Task> getTask = [
  Task(
      name: "Catch up with Brian",
      category: "Mobile Project",
      time: "5pm",
      color: Colors.orange,
      completed: false),
  Task(
      name: "Make new icons",
      category: "Web App",
      time: "3pm",
      color: Colors.cyan,
      completed: true),
  Task(
      name: "Design explorations",
      category: "Company Website",
      time: "2pm",
      color: Colors.pink,
      completed: false),
  Task(
      name: "Lunch with Mary",
      category: "Grill House",
      time: "12pm",
      color: Colors.lightGreenAccent,
      completed: true),
  Task(
      name: "Teem Meeting",
      category: "Hangouts",
      time: "10am",
      color: Colors.redAccent,
      completed: true),
  Task(
      name: "Catch up with Brian",
      category: "Mobile Project",
      time: "5pm",
      color: Colors.yellowAccent,
      completed: false),
  Task(
      name: "Make new icons",
      category: "Web App",
      time: "3pm",
      color: Colors.blueAccent,
      completed: true),
  Task(
      name: "Design explorations",
      category: "Company Website",
      time: "2pm",
      color: Colors.pinkAccent,
      completed: false),
  Task(
      name: "Lunch with Mary",
      category: "Grill House",
      time: "12pm",
      color: Colors.lightBlue,
      completed: true),
  Task(
      name: "Teem Meeting",
      category: "Hangouts",
      time: "10am",
      color: Colors.purple,
      completed: true),
  Task(
      name: "Catch up with Brian",
      category: "Mobile Project",
      time: "5pm",
      color: Colors.orange,
      completed: false),
  Task(
      name: "Make new icons",
      category: "Web App",
      time: "3pm",
      color: Colors.cyan,
      completed: true),
  Task(
      name: "Design explorations",
      category: "Company Website",
      time: "2pm",
      color: Colors.pink,
      completed: false),
  Task(
      name: "Lunch with Mary",
      category: "Grill House",
      time: "12pm",
      color: Colors.lightGreenAccent,
      completed: true),
  Task(
      name: "Teem Meeting",
      category: "Hangouts",
      time: "10am",
      color: Colors.indigoAccent,
      completed: true)
];
```
Trong design có 1 đường line màu xám, chiều dọc chạy theo danh sách task, để tạo được cái này chỉ cần 1 đoạn code đơn giản như sau:
```dart
Widget _buildTimeline() {
  return new Positioned(
    top: 0.0,
    bottom: 0.0,
    left: 32.0,
    child: new Container(
      width: 1.0,
      color: Colors.grey[300],
    ),
  );
}
```
Sau khi thêm nó vào cuối của stack, tôi sẽ tạo UI cho mỗi item của task
```dart
class TaskRow extends StatefulWidget {
  final Task task;
  final double dotSize = 12.0;

  const TaskRow({Key key, this.task}) : super(key: key);

  @override
  State<StatefulWidget> createState() {
    return new TaskRowState();
  }
}

class TaskRowState extends State<TaskRow> {
  @override
  Widget build(BuildContext context) {
    return new Padding(
      padding: const EdgeInsets.symmetric(vertical: 16.0),
      child: new Row(
        children: <Widget>[
          new Padding(
            padding: new EdgeInsets.symmetric(horizontal: 32.0 - widget.dotSize / 2),
            child: new Container(
              height: widget.dotSize,
              width: widget.dotSize,
              decoration: new BoxDecoration(shape: BoxShape.circle, color: widget.task.color),
            ),
          ),
          new Expanded(
            child: new Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: <Widget>[
                new Text(
                  widget.task.name,
                  style: new TextStyle(fontSize: 18.0),
                ),
                new Text(
                  widget.task.category,
                  style: new TextStyle(fontSize: 12.0, color: Colors.grey),
                )
              ],
            ),
          ),
          new Padding(
            padding: const EdgeInsets.only(right: 16.0),
            child: new Text(
              widget.task.time,
              style: new TextStyle(fontSize: 12.0, color: Colors.grey),
            ),
          ),
        ],
      ),
    );
  }
}
```
item task có 3 phần tử nhỏ: 1 dấu chấm được tạo bởi Container + boxDecoration, 1 column với 2 text và 1 text biểu diễn thời gian của task. Sau khi đã tạo xong item cho task thì việc còn lại chỉ đơn giản là sử dụng ListView để tạo list
```dart
Widget _buildTasksList() {
  return new Expanded(
    child: new ListView(
      children: tasks.map((task) => new TaskRow(task: task)).toList(),
    ),
  );
}
```
## 6. Animating items filltering
Cố gắng nào, tôi đã đi được hơn nửa chặng đường rồi. Bây giờ tôi sẽ thay đổi ListView thành AnimatedList để giúp tôi có thể tạo và dử dụng animation với list 1 cách dễ dàng hơn. Tôi sẽ update lại function `_buildTasksList ()`
```dart
class _MainPageState extends State<MainPage> {
  final GlobalKey<AnimatedListState> _listKey = new GlobalKey<AnimatedListState>();

  ...

  Widget _buildTasksList() {
    return new Expanded(
      child: new AnimatedList(
        initialItemCount: tasks.length,
        key: _listKey,
        itemBuilder: (context, index, animation) {
          return new TaskRow(
            task: tasks[index],
          );
        },
      ),
    );
  }
}
```
AnimatedList sẽ cung cấp 2 method: insert(index) và remove(index,builder). Để giữ danh sách task được đồng bộ tôi sẽ tạo 1 class là ListMode
```dart
class ListModel {
  ListModel(this.listKey, items) : this.items = new List.of(items);

  final GlobalKey<AnimatedListState> listKey;
  final List<Task> items;

  AnimatedListState get _animatedList => listKey.currentState;

  void insert(int index, Task item) {
    items.insert(index, item);
    _animatedList.insertItem(index);
  }

  Task removeAt(int index) {
    final Task removedItem = items.removeAt(index);
    if (removedItem != null) {
      _animatedList.removeItem(
        index,
        (context, animation) => new Container(),
      );
    }
    return removedItem;
  }

  int get length => items.length;

  Task operator [](int index) => items[index];

  int indexOf(Task item) => items.indexOf(item);
}
```
Cái quan trọng nhất của class này là 2 function insert và removeAt. Insert dùng để thêm 1 task vào list và AnimatedList và removeAt sẽ làm điều ngược lại.
Lúc này code sẽ được update như sau
```dart
class _MainPageState extends State<MainPage> {
  final GlobalKey<AnimatedListState> _listKey = new GlobalKey<AnimatedListState>();
  ListModel listModel;

  @override
  void initState() {
    super.initState();
    listModel = new ListModel(_listKey, tasks);
  }

  Widget _buildTasksList() {
    return new Expanded(
      child: new AnimatedList(
        initialItemCount: tasks.length,
        key: _listKey,
        itemBuilder: (context, index, animation) {
          return new TaskRow(
            task: listModel[index],
          );
        },
      ),
    );
  }
}
```
Sau khi add ListMode vào MainPageState, hãy chú ý đến chứ năng fillter, lúc này tôi sử dụng 1 biến là showOnlyCompleted, biến này sẽ thay đổi khi người dùng click vào FloatingActionButton
```dart
class _MainPageState extends State<MainPage> {
  ListModel listModel;
  bool showOnlyCompleted = false;


  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      body: new Stack(
        children: <Widget>[
          ...
          _buildFab(),
        ],
      ),
    );
  }

  Widget _buildFab() {
    return new Positioned(
      top: _imageHeight - 36.0,
      right: 16.0,
      child: new FloatingActionButton(
        onPressed: _changeFilterState,
        backgroundColor: Colors.pink,
        child: new Icon(Icons.filter_list),
      ),
    );
  }

  void _changeFilterState() {
    showOnlyCompleted = !showOnlyCompleted;
    tasks.where((task) => !task.completed).forEach((task) {
      if (showOnlyCompleted) {
        listModel.removeAt(listModel.indexOf(task));
      } else {
        listModel.insert(tasks.indexOf(task), task);
      }
    });
  }
}
```
Ok đến đây về logic có vẻ đã ổn, tôi sẽ sửa lại 1 chút về animation cho nó thêm lung linh, tôi sử dụng SizeTransition  và FadeTransition khi row co lại và mở rộng
```dart
class TaskRow extends StatelessWidget {
  final Animation<double> animation;

  const TaskRow({Key key, this.task, this.animation}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return new FadeTransition(
      opacity: animation,
      child: new SizeTransition(
        sizeFactor: animation,
        child: ...
      ),
    );
  }
}
```
Bây giờ tôi cần pass transition tới 2 function insert và removeAt
```dart
class ListModel {
  Task removeAt(int index) {
    final Task removedItem = items.removeAt(index);
    if (removedItem != null) {
      _animatedList.removeItem(
        index,
        (context, animation) => new TaskRow(
              task: removedItem,
              animation: animation,
            ),
      );
    }
    return removedItem;
  }
}
class _MainPageState extends State<MainPage> {

  Widget _buildTasksList() {
    return new Expanded(
      child: new AnimatedList(
        initialItemCount: tasks.length,
        key: _listKey,
        itemBuilder: (context, index, animation) {
          return new TaskRow(
            task: listModel[index],
            animation: animation,
          );
        },
      ),
    );
  }

}
```
và 1 chút update nhỏ ở class ListMode
```dart
class ListModel {

  void insert(int index, Task item) {
    items.insert(index, item);
    _animatedList.insertItem(index, duration: new Duration(milliseconds: 150));
  }

  Task removeAt(int index) {
    ...
      _animatedList.removeItem(
        index,
        (context, animation) => new TaskRow(
              task: removedItem,
              animation: animation,
            ),
        duration: new Duration(milliseconds: (150 + 150*(index/length)).toInt())
      );
    }
    ...
  }

}
```
## 7. Animated FloatingActionButton
Và cuối cùng là tạo animation cho FloatingActionButton, tôi tạo 1 class mới có tên AnimatedFab
```dart
class AnimatedFab extends StatefulWidget {
  final VoidCallback onClick;

  const AnimatedFab({Key key, this.onClick}) : super(key: key);

  @override
  _AnimatedFabState createState() => new _AnimatedFabState();
}

class _AnimatedFabState extends State<AnimatedFab> {

  @override
  Widget build(BuildContext context) {
    return _buildFabCore();
  }

  Widget _buildFabCore() {
    return new FloatingActionButton(
      onPressed: widget.onClick,
      child: new Icon(Icons.filter_list),
      backgroundColor: Colors.pink,
    );
  }
}
```
và thay thế FloatingActionButton hiện có bằng class này. Theo như design thì chỗ này có 2 transition là: 1 là change fillter icon to close icon, 2 là change color. Để làm được điều này tôi sử dụng AnimationController, khi click vào Fab sẽ không gọi tới `_onClick()` nữa mà sẽ open hoặc cloce button.
```dart
class _AnimatedFabState extends State<AnimatedFab> with SingleTickerProviderStateMixin {
  AnimationController _animationController;
  Animation<Color> _colorAnimation;

  @override
  void initState() {
    super.initState();
    _animationController = new AnimationController(vsync: this, duration: Duration(milliseconds: 200));
    _colorAnimation = new ColorTween(begin: Colors.pink, end: Colors.pink[800])
        .animate(_animationController);
  }

  @override
  void dispose() {
    _animationController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return new AnimatedBuilder(
      animation: _animationController,
      builder: (BuildContext context, Widget child) {
        return _buildFabCore();
      },
    );
  }

  Widget _buildFabCore() {
    return new FloatingActionButton(
      onPressed: _onFabTap,
      child: new Icon(Icons.filter_list),
      backgroundColor: _colorAnimation.value,
    );
  }

  open() {
    if (_animationController.isDismissed) {
      _animationController.forward();
    }
  }

  close() {
    if (_animationController.isCompleted) {
      _animationController.reverse();
    }
  }

  _onFabTap() {
    if (_animationController.isDismissed) {
      open();
    } else {
      close();
    }
  }
}
```
Tôi sẽ tạo 2 animator. AnimationController sẽ là lớp chính để làm việc này và ColorAnimation là lớp dẫn xuất để cũng cấp màu sắc khi thay đổi và mọi thứ sẽ được wrap trong AnimatedBuilder 
```dart
Widget _buildFabCore() {
  double scaleFactor = 2 * (_animationController.value - 0.5).abs();
  return new FloatingActionButton(
    onPressed: _onFabTap,
    child: new Transform(
        alignment: Alignment.center,
        transform: new Matrix4.identity()..scale(1.0, scaleFactor),
        child: new Icon(
          _animationController.value > 0.5 ? Icons.close : Icons.filter_list,
          color: Colors.white,
          size: 26.0,
        ),
      ),
    backgroundColor: _colorAnimation.value,
  );
}
```

```dart
class _AnimatedFabState extends State<AnimatedFab> with SingleTickerProviderStateMixin {
  final double expandedSize = 180.0;

  @override
  Widget build(BuildContext context) {
    return new SizedBox(
      width: expandedSize,
      height: expandedSize,
      child: new AnimatedBuilder(
        animation: _animationController,
        builder: (BuildContext context, Widget child) {
          return new Stack(
            alignment: Alignment.center,
            children: <Widget>[
              _buildFabCore(),
            ],
          );
        },
      ),
    );
  }
}
```

``` dart
class _MainPageState extends State<MainPage> {
  Widget _buildFab() {
    return new Positioned(
      top: _imageHeight - 100.0,
      right: -40.0,
      child:new AnimatedFab(
        onClick: _changeFilterState,
      )
    );
  }
}
```

Bây giờ tôi sẽ add thêm 1 background nó sẽ thay đổi kích thước dựa trên giá trị của animation
```dart
class _AnimatedFabState extends State<AnimatedFab> with SingleTickerProviderStateMixin {
  final double expandedSize = 180.0;
  final double hiddenSize = 20.0;

  @override
  Widget build(BuildContext context) {
    return new SizedBox(
      ...
          return new Stack(
            alignment: Alignment.center,
            children: <Widget>[
              _buildExpandedBackground(),
              _buildFabCore(),
            ],
          );
  }

  Widget _buildExpandedBackground() {
    double size = hiddenSize + (expandedSize - hiddenSize) * _animationController.value;
    return new Container(
      height: size,
      width: size,
      decoration: new BoxDecoration(shape: BoxShape.circle, color: Colors.pink),
    );
  }
}
```
Tôi đã thêm `hiddenSize`. Nếu tôi chỉ nhân `expandedSize` với `_animationController.value` thì nó sẽ hoạt động về mặt lý thuyết nhưng ban đầu nó sẽ bi che đi bởi Fab.

Đến đây coi như đã xong được 90% rồi đâ, việc còn lại là tôi sẽ thêm các icon khi Fab được mở rộng. Ý tưởng của tôi là đầu tiên hãy đặt các icon ở vị trí cố định sau đó sử dụng Transform.rotate để dịch chuyển chúng vào các vị trí tương ứng của còng tròn mở rộng 
```dart
import 'dart:math' as math;
class _AnimatedFabState extends State<AnimatedFab>with SingleTickerProviderStateMixin {

  @override
  Widget build(BuildContext context) {
    return new SizedBox(
      ...
          return new Stack(
            alignment: Alignment.center,
            children: <Widget>[
              _buildExpandedBackground(),
              _buildOption(Icons.check_circle, 0.0),
              _buildOption(Icons.flash_on, -math.pi / 3),
              _buildOption(Icons.access_time, -2 * math.pi / 3),
              _buildOption(Icons.error_outline, math.pi),
              _buildFabCore(),
            ],

  }
  Widget _buildOption(IconData icon, double angle) {
    return new Transform.rotate(
      angle: angle,
      child: new Align(
        alignment: Alignment.topCenter,
        child: new Padding(
          padding: new EdgeInsets.only(top: 8.0),
          child: new IconButton(
            onPressed: null,
            icon: new Transform.rotate(
              angle: -angle,
              child: new Icon(
                icon,
                color: Colors.white,
              ),
            ),
            iconSize: 26.0,
            alignment: Alignment.center,
            padding: new EdgeInsets.all(0.0),
          ),
        ),
      ),
    );
  }
}
```
![](https://fidev.io/wp-content/uploads/2018/05/ff_14.png)

```dart
Widget _buildOption(IconData icon, double angle) {
  double iconSize = 0.0;
  if (_animationController.value > 0.8) {
    iconSize = 26.0 * (_animationController.value - 0.8) * 5;
  }
  return new Transform.rotate(
    ...
        child: new IconButton(
          ...
          iconSize: iconSize,
        ),
  );
}
```
Và cuối dùng tôi sẽ update function 
```dart
_onIconClick() {
  widget.onClick();
  close();
}
```

## 8. Tổng kết

p/s: source code và hình ảnh tôi có thể update liên tục nên có phần khác nhau

![](https://user-images.githubusercontent.com/22972057/70401094-c5723400-1a60-11ea-93d4-9cae1f6ee77f.png)
![](https://user-images.githubusercontent.com/22972057/70401102-c7d48e00-1a60-11ea-9219-73b6a836d7b6.png)

Ok, mọi thứ đều ổn phải không nào. Bạn hãy làm thử theo hướng dẫn và có thể view source t[ại đây ](https://github.com/nvquangth/beautiful-UI-1)

Nếu thấy căng thẳng quá có thể dừng lại ngắm các hot girl trong bài viết rồi tiếp tục code

![](https://raw.githubusercontent.com/nvquangth/beautiful-UI-1/master/assets/images/girl.jpg)
![](https://raw.githubusercontent.com/nvquangth/beautiful-UI-1/master/assets/images/girl2.jpg)

Hoặc lên GameTvPlus làm mấy ván AOE ranking với tôi.

Good luck!
