Hi all, hôm nay mình sẽ tiếp tục chia sẻ với các bạn về 1 chủ đề cũng đã khá quen thuộc với mọi người. Tuy nhiên vì tính chất của nó rất quan trọng nên mình vẫn muốn nhắc lại. Chắc hẳn ai cũng đã từng trải qua quá trình tìm hiểu về vấn đề mà mình sắp nói ở đây. Và biết được nó khó nhằn đến mức nào nhỉ :)))

Nôị dung chính mình trình bày gồm các phần sau:

**1. Hiểu biết cơ bản về Task và Back stack**

**2. Cách định nghĩa và sử dung Launch mode để can thiếp vào việc quản lý task và back stack**

**3. Cách định nghĩa và sử dụng Task Affinities kết hợp với Launch mode**

**4. Clear back stack**

**5. Cách start 1 task**

Ở nội dung bài này mình sẽ trình bày 2 phần đầu tiên, các bạn theo dõi ủng hộ mình nhé.

### 1. Thế nào là Task và Back stack
Theo Android developer thì 1 **task** là 1 tập các activities tương tác với người dùng. Những activities này được sắp xếp trong 1 stack, được gọi là **back stack**.

Khi 1 activity A đang chạy, 1 activity B được khởi chạy từ A, thì A sẽ được add vào back stack. Khi người dùng nhấn vào button back, A sẽ được pop khỏi back stack mà không phải tạo mới 1 activity. Khi ứng dụng ở chế độ multi-screen(Android 7.0 - API 24), thì ở mỗi màn hình hệ thống sẽ quản lý task riêng biệt, mỗi màn hình có thể có nhiều task.

Màn hình chính của thiết bị là nơi bắt đầu của hầu hết các task. Khi người dùng bắt đầu mở ứng dụng bằng cách click vào icon launcher hoặc shortcut ở màn hình chính, thì lúc này task của ứng dụng bắt đầu ở trạng thái foreground. Nếu hiện tại ứng dụng không có bất kì task nào (ứng dụng không tương tác với người dùng), thì một task mới sẽ được tạo và activity main của ứng dụng được mở thì activity đó được xem là root activity của task.
Khi ở activity hiện tại mà mở 1 activity khác, thì activity mới này sẽ được đưa vào đỉnh ngăn xếp và ở trạng thái được forcus. Activity trước đó thì được giữ lại trong stack, nhưng ở trạng thái stopped. Khi 1 activity stop thì nó sẽ được hệ thống giữ lại trạng thái hiện tại mà đang tương tác với người dùng. Khi người dùng nhấn **Back**, activity hiện tại sẽ bị đẩy ra khỏi đỉnh stack và bị destroy, đồng thời activity trước đó sẽ được resumes (trạng thái trước đó của activity sẽ được restored). Tập các activities trong stack không hề được sắp xếp, mà chúng chỉ đơn giản là được đưa vào và lấy ra khỏi stack. Stack thì như bạn biết đó nó hoạt động theo cơ chế "Last In First Out". Hình ảnh dưới đây sẽ cho bạn cái nhìn rõ hơn về việc đưa vào lấy ra activity từ stack:

 ![](https://images.viblo.asia/ec82cf5b-7d3c-4df7-9ac8-5dc3a5da0937.png)
 
 Hình 1: Cách mà mỗi activity được đẩy vào stack. Khi người dùng nhấn nút Back, activity hiện tại bị destroy đồng thời activity trước đó được resume
 
Nếu người dùng tiếp tục nhấn nút **Back**, thì các activity trong stack sẽ được lấy ra hết cho đến khi nào người dùng trở về đến màn hình Home. Và khi tất cả activities bị removed khởi stack, thì stack không còn tồn tại nữa.

![](https://images.viblo.asia/a21971ed-a4d3-4d4c-a913-37be171205a3.png)

Hình 2: Có 2 task: Task B nhận tương tác của người dùng ở trạng thái foreground, trong khi task A ở background đang đợi để được resume.

**Chú ý:** Tại 1 thời điểm có thể có nhiều task chạy ở background. Chính vì thế hệ thống có thể kill bất cứ task nào trong trường hợp hệ thống thiếu bộ nhớ, điều này đồng nghĩa với việc trạng thái của activity được lưu trữ trước đó cũng mất theo.

Bởi vì các activity nằm trong stack không bao giờ được sắp xếp theo thứ tự, nếu ứng dụng của bạn cho phép người dùng start 1 activity từ 1 activity khác, thì 1 instance mới của activity đó sẽ được tạo và đẩy vào trong stack. Tức là tại 1 thời điểm trong 1 task có thể tồn tại nhiều instance của 1 activity. Nếu bạn muốn điều chỉnh việc này thì ngay sau đây mình sẽ hướng dẫn bạn làm nha. Hình dưới đây mô tả 1 task chứa nhiều instance của activity tại 1 thời điểm: 

![](https://images.viblo.asia/85b577b0-2b97-48a6-9e78-ba98d04cfca0.png)

Hình 3: 1 activity có nhiều instance tại 1 thời điểm trong task

Tổng kết lại phía trên thì có vài ý như này:

    - Khi activity A start activity B, activity A sẽ stopped, nhưng sẽ được hệ thống giữ lại trạng thái(vị trí scroll, text input). Và khi đang ở activity B mà nhấn Back, activity A sẽ resumed và trạng thái trước đó sẽ được restore.
    - Khi người dùng nhấn vào nút Home, activity hiện tại sẽ bị stop, và task sẽ đi vào trạng thái background đồng thời trạng thái của các activity cũng được giữ lại. Sau đó, nếu người dùng select launch icon, task sẽ trở lại trạng thái foreground, đồng thời state của các activity cũng được restore.
    - Nếu người dùng nhấn Back, activity hiện sẽ bị destroy và popped khỏi stack.
    - Tại 1 thời điểm, activity có thể có nhiều instance và ở nhiều task khác nhau.


### 2. Quản lý các task
Như đã mô tả ở trên, thì việc quản lý các task trong stack được thự hiện bởi hệ thống Android và bạn không cần phải quan tâm đến việc quản lý và truy cập đến các task như thế nào. Tuy nhiên, có thể can thiệp vào việc đó nếu bạn muốn. Chẳng hạn, mặc định các activity sẽ được khởi chạy trong cùng 1 task và bạn có thể can thiệp vào để chúng có thể chạy trên các task khác nhau. Hoặc bạn muốn khi 1 activity được khởi chạy thì tất cả các instance của activity trước nó bị xóa hết. Bạn hoàn toàn có thể làm được điều này và có thể làm nhiều điều hơn thế nữa thông qua các thuộc tính của thẻ `<activity>` được định nghĩa trong file **AndroidManifest**.

Để làm được những điều đã nói ở trên bạn cần quan tâm đến các thuộc tính sau củ thẻ `<activity>`:
- taskAffinity
- launchMode
- allowTaskReparenting
- clearTaskOnLaunch
- alwaysRetainTaskState
- finishOnTaskLaunch

Và các cờ quan trọng sau:
- FLAG_ACTIVITY_NEW_TASK
- FLAG_ACTIVITY_CLEAR_TOP
- FLAG_ACTIVITY_SINGLE_TOP

Trong phần này, bạn sẽ hiểu được cách sử dụng các thuộc tính của Manifest và các cờ để điều khiển việc truy cập instance của activity như thế nào và một số thao tác khác trọng back stack.

Trước khi đi vào phần tiếp theo, các developer của Android khuyến cáo như sau: Hầu hết các ứng dụng Android đề sử dụng cách quản lý mặc định của activity và stack. Nếu bạn thực sự cần thiết can thiệp vào việc quản lý này thì hãy đảm bảo rằng ứng dụng của bạn nên được test kĩ nhiều trường hợp trong quá trình sử dụng ứng dụng và khi nhấn nút Back để test hoạt động của stack. Vì khi bạn can thiệp vào quá trình quản lý mặc định rất có thể xảy ra những lỗi mà bạn chưa lường trước được. Good luck for you.

#### 2.1. Định nghĩa launch modes

Launch modes cung cấp cách để 1 instance của activity được truy cập ở task hiện tại. Bạn có thể định nghĩ nó theo 2 cách sau:

- Sử dụng Manifest: Khi khai báo trong manifest, thì bạn sẽ xác định cách mà activity được truy cập trong task khi nó được start.
- Sử dụng Intent flags: Khi 1 activity được start bởi startActivity(), bạn có thể set flag cho intent để xác định cách mà activity được truy cập trong task hiện tại.

##### 2.1.1. Sử dụng Manifest
Khi bạn khai báo activity trong file Manifest, bạn có thể xác định được cách mà activity được truy cập như thế nào bằng cách sử dụng thuộc tính `launchMode` của thẻ `<activity>`. Có 4 giá trị khác nhau của launchMode như sau:
- standard: default mode, tạo mới 1 instance của activity khi mà nó được gọi.
- singleTop: nếu 1 instance của activity đang tồn tại ở top của current task, hệ thống sẽ điều hướng đến instance đó thông qua onNewIntent(). Không thì sẽ tạo mới 1 instance

**Ví dụ:** 1 task bao gồm activity root là A, và các activity B, C, D (sắp xếp theo thứ thự A-B-C-D, trong đó D ở top của stack). Tại activity D, có 1 itent gọi đến chính D để start. Nếu activity D có launch mode mặc định là **standard**, thì 1 instance mới của D sẽ được tạo và đây vào stack. Lúc này thứ tự các activity trong stack sẽ là A-B-C-D-D. Tuy nhiền, nếu activity D có launch mode là **singleTop**, thì hệ thống sẽ không tạo mới instance của D. Thay vào đó hệ thống sẽ điều hướng đến instance D đã tồn tại ở top của stack và chạy vào hàm onNewIntent(). Lúc này, thứ tự các activity trong stack vẫn được giữ nguyên là A-B-C-D. Nhưng nếu cũng với launch mode này nhưng lại khai báo cho activity B và gọi start activity B, thì 1 instance mới của B vẫn được tạo như bình thường và đẩy vào stack. Bởi vì instance của B không ở top của stack. Hãy nhớ rẳng với kiểu launch mode này bạn chỉ cần quan tâm đến instance ở top của stack có cùng kiểu với activity được gọi start hay không.

**Chú ý:** khi 1 instatnce của activity được khởi tạo, người dùng có thể nhấn nút Back để trở lại activity trước đó. Nhưng khi 1 instance của activity đã tồn tại và được điều hướng tới thông qua onNewIntent(), thì người dùng không thể nhấn nút Back để trở lại trạng thái trước đó của activity.

- singleTask: Nếu activity chưa tồn tại instance, thì sẽ tạo 1 task mới và khởi tạo 1 instance của activity ở task này. Nếu activity đã tồn tại 1 instance ở 1 task rồi, thì hệ thống sẽ điều hướng đến instance đó thông qua onNewIntent(), và clear hết các activity ở trên nó.

**Chú ý:** mặc dùng activity được start ở 1 task mới, nhưng khi người dùng nhấn nút Back thì hệ thống vẫn điều hướng về activivty trước đó.

- singleInstance: Tương tự như singleTask, ngoại trừ việc task chỉ chứa duy nhất instance của activity đó mà không có bất cứ 1 instance của activity nào khác.

##### 2.1.2. Sử dụng intent flags
Khi 1 activity được start bởi startActivity(), bạn có thể set flag cho intent để xác định cách mà activity được truy cập trong task hiện tại. Có 3 loại flag bạn có thể sụng dụng như sau:
- FLAG_ACTIVITY_NEW_TASK: ý nghĩa sử dụng tương tự singleTask
- FLAG_ACTIVITY_SINGLE_TOP: ý nghĩa sử dụng tương tự singleTop
- FLAG_ACTIVITY_CLEAR_TOP: nếu activity đã tồn tại 1 instance ở task hiện tại, thì hệ thống sẽ điều hướng đến instance thông qua onNewIntent(), đồng thời clear hết các instance khác ở phía trên nó, và lúc này activity sẽ ở top của task. Nếu activity chưa tồn tại instance nào, nó sẽ tạo mới instance và ở top của task.

Phần 1 mình xin được dừng ở đây, phần tiếp theo mình sẽ trình bày 3 phần còn lại của bài viết là: 

**3. Cách định nghĩa và sử dụng Task Affinities kết hợp với Launch mode**

**4. Clear back stack**

**5. Cách start 1 task**

Để tổng kết lại phần 1 và giới thiệu cho phần 2, các bạn có thể xem qua video này để hiểu trực quan và sinh động hơn nha.

{@embed: https://www.youtube.com/watch?v=MvIlVsXxXmY}

Các bạn chú ý đón đọc bài tiếp theo trong seri của mình nha. Thanks all.

Bài viết được dịch từ trang Android Developer: [Understand Tasks and Back Stack](https://developer.android.com/guide/components/activities/tasks-and-back-stack)
