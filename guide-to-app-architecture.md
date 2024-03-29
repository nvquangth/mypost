Hướng dẫn này bao gồm các bài luyện tập và những lời khuyên hữu ích về kiến trúc để xây dựng một ứng dụng chất lượng cao. Ở đây chúng tôi giả định rằng bạn đã có những 
kiến thức cơ bản về `Android Framework`. Nếu bạn là người mới làm quen với Android, vui lòng xem trước các hướng dẫn [tại đây](https://developer.android.com/guide) để có thể nắm được những khái niệm được đề cập tới
trong hướng dẫn này.

### Trải nghiệm người dùng
Trong hầu hết các trường hợp, các ứng dụng dạng desktop đều có 1 điểm đơn bắt đầu từ desktop hoặc 1 trình khởi chạy, sau đó sẽ chạy như 1 khối. Với các ứng dụng Android thì lại
khác, nó có nhiều cấu trúc phức tạp hơn. Một ứng dụng Android điển hình bao gồm nhiều thành phần như `acitivities`, `fragments`, `services`, `content provider` và `broadcast recievers`.

Hầu hết bạn sẽ khai báo các thành phần này trong tệp tin app `manifest` của ứng dụng. Sau đó hệ điều hành Android sẽ sử dụng tệp tin này để quyết định việc tích hợp các thành phần
trên thành 1 ứng dụng. Giả sử ứng dụng của bạn được tạo nên bởi nhiều thành phần và người dùng sẽ tương tác với nhiều ứng dụng trong 1 khoảng thời gian ngắn, ứng dụng cần tương
thích với nhiều kịch bản sử dụng cũng như các tác vụ khác nhau.

Ví dụ, hãy xem những gì xảy ra khi người dùng chia sẻ 1 hình ảnh lên mạng xã hội như Facebook:
1. Ứng dụng sẽ cần dùng đến camera, hệ điều hành Android sẽ mở ứng dụng camera để xử lý yêu cầu. Tại thời điểm này, người dùng đã rời khỏi ứng dụng mạng xã hội, nhưng trải nghiệm của họ vẫn
đảm bảo liên tục.
2. Ứng dụng cần gọi đến 1 thành phần khác, như cần lựa chọn 1 tệp tin, và có thể cũng sẽ mở 1 ứng dụng khác.
3. Cuối dùng người dùng quay lại mạng xã hội và chia sẻ ảnh

Tại bất kỳ thời điểm nào trong quá trình sử dụng ứng dụng, người dùng có thể bị gián đoạn bởi 1 cuộc gọi hay 1 thông báo... Sau khi thực hiện xong các hoạt động này, người dùng
mong muốn quay trở lại trạng thái trước đó đang sử dụng ạp, ở đây muốn nói đến lại quay lại quá trình chia sẻ ảnh. Đây được xem như 1 hành vi cơ bản của các ứng dụng di động, vì
vậy bạn cần xử lý các trường hợp này một cách đúng đắn.

Hãy luôn nhớ 1 điều rằng tài nguyên ứng dụng bị phụ thuộc vào hệ điều hành, do vậy tại bất kỳ thời điểm nào, hệ điều hành có thể dừng các tiến trình của ứng dụng. Vì vậy, bạn không
nên lưu trữ bất kỳ dữ liệu hoặc trạng thái của các thành phần ứng dụng; và các thành phần ứng dụng không bị phụ thuộc lẫn nhau.

### Nguyên lý chung của kiến trúc

**Tách biệt các thành phần

Điều quan trọng nhất của nguyên lý là tách biệt các thành phần. Đây là 1 sai lầm chung khi bạn viết tất cả mã nguồn vào `Activity` hoặc `Fragment`. Những lớp phụ trách UI cơ bản này
chỉ nên chưa các logic xử lý liên quan đến UI và các tương tác của hệ điều hành. Bằng cách giữ cho các lớp này càng gọn càng tốt, bạn có thể sẽ tránh được các vấn đề liên quan
tới vòng đời của chúng.

Hãy nhớ rằng bạn không sở hữu `Activity` hay `Fragment`; hơn nữa những thành phần này đại diện cho sự tương tác giữa hệ điều hành Android và ứng dụng của bạn. Chúng có thể bị bởi hệ
điều hành hủy cứ lúc nào, ví dụ như khi bộ nhớ quá tải. Để đảm bảo cung cấp những trải nghiệm tốt nhất cho người dùng và việc bảo trỉ sau này, cách tốt nhất làm giảm sự phụ thuộc
vào chúng.

**Hướng UI tới 1 model

Một nguyên lý quan trọng khác đó là hướng UI tới 1 model. Model là 1 thành phần chịu trách nhiệm xử lý dữ liệu của ứng dụng. Nó độc lập với các đối tượng View và các thành phần
của ứng dụng, vì vậy nó không bị ảnh hưởng vòng đời của ứng dụng và các liên kết khác.

Nguyên lý này bắt nguyền từ những lý do sau:
- Dữ liệu của người dùng không bị mấy khi hệ điều hành hủy ứng dụng khi cần bộ nhớ
- Ứng dụng của bạn vẫn tiếp tục sử dụng trong trường hợp kết nối mạng không ổn định hoặc không khả dụng

Bằng việc phân rõ trách nhiệm trên ứng dụng của bạn, làm nó trở nên dễ dàng kiểm thử và nhất quán hớn

### Đề xuất kiến trúc ứng dụng
Trong phần này, chúng tôi sẽ trình bày cách xây dựng ứng dụng sử dụng các [Architecture Componet](https://developer.android.com/jetpack) với 1 use case đầy đủ.

Giả sử chúng tôi xây dựng 1 ứng dụng hiển thị thông tin của người dùng. Chúng tôi sử dụng backend server cung cấp `REST API` để lấy thông tin người dùng.

* Tổng quan
Để bắt đầu, bạn hãy quan sát lược đồ sau để thấy được các mô-đun trong ứng dụng tương tác với nhau như thế nào.

![](https://developer.android.com/topic/libraries/architecture/images/final-architecture.png)

**Lưu ý**: mỗi thành phần chỉ phụ thuộc vào 1 thành phần cấp dưới. Ví dụ, `activities` và `fragments` chỉ phụ thuộc vào `ViewModel`. `Repositories` là thành phần duy nhất phụ thuộc vào nhiều thành phần khác. Ở đây nó phụ thuộc vào dữ liệu `remote` và `local`.

Thiết kế này phù hợp với trải nghiệm người dùng. Bất kể khi nào người dùng quay trở lại ứng dụng sau một vài phút hoặc vài ngày, họ ngay lập tức vẫn xem được thống tin vì nó đã được lưu trữ ở local. Nếu dữ liệu này cũ, `repository` sẽ cập nhật lại dữ liệu.

* Xây dựng giao diện người dùng
Giao diện bao gồm 1 fragment, `UserProfileFragment`, layout tưng ứng của nó là `user_profile_layout.xml`

Về phần model sẽ nắm giữ dữ liệu theo luồng sau:
- `User ID`: định danh của user, đây là 1 cách tốt để truyền thông tin giữa `fragment` và `fragment` thông qua `arguments`. Nếu hệ điều hành Android hủy tiến trình đang chạy, thông tin
này vẫn sẽ được lưu giữ, vì vậy việc sử dụng ID là hợp lý
- `User object`: 1 class nắm giữ thông tin user

Chúng tôi sử dụng `UserProfileViewModel`, kế thừa từ `ViewModel` - 1 thành phần thuộc `Android Jetpack`, để nắm giữ thông tin.

1 đối tượng `ViewModel` cung cấp dữ liệu cho 1 thành phần UI nhất định, như `fragment` hoặc `activity` và chứa dữ liệu xử lý logic nghiệp vụ để kết nối với model. Ví dụ, ViewModel có
thể gọi các thành phần khác để tải dữ liệu và nó có thể là cầu nối chuyển tiếp yêu cầu của người dùng thay đổi dữ liệu. ViewModel không biết đến các thành phần của UI, vì vậy
nó không bị ảnh hưởng khi cấu hình thay đổi như khi thiết bị xoay màn hình.

Bây giờ chúng tôi sẽ định nghĩa các tệp tin:
- `user_profile.xml`: layout hiển thị giao diện
- `UserProfileFragment`: UI điều khiển hiển thị dữ liệu
- `UserProfileViewModel`: chịu trách nhiệm chuẩn bị dữ liệu cho View và nhận tương tác từ người dùng

Hãy quan sát code bên dưới:

`UserProfileViewModel`

```java
class UserProfileViewModel : ViewModel() {
   val userId : String = TODO()
   val user : User = TODO()
}
```

`UserProfileFragment`
```java
class UserProfileFragment : Fragment() {
      // To use the viewModels() extension function, include
      // "androidx.fragment:fragment-ktx:latest-version" in your app
      // module's build.gradle file.
      private val viewModel: UserProfileViewModel by viewModels()

      override fun onCreateView(
          inflater: LayoutInflater, container: ViewGroup?,
          savedInstanceState: Bundle?
      ): View {
          return inflater.inflate(R.layout.main_fragment, container, false)
      }
   }
```

Bây giờ, chúng ta đã có các mô-đun trên, vậy chúng sẽ kết hợp với nhau như thế nào? Sau tất cả, khi trường user được thiếp lập trong `UserProfileViewModel`. Chúng tôi cần 1 cách 
để thông báo cho UI.

Để có được `usser`, ViewModel cần truy cập vào argument của Fragment. Chúng ta hoặc có thể truyền nó qua từ fragment, hoặc tốt hơn, sử dụng SaveState module.

```
// UserProfileViewModel
class UserProfileViewModel(
   savedStateHandle: SavedStateHandle
) : ViewModel() {
   val userId : String = savedStateHandle["uid"] ?:
          throw IllegalArgumentException("missing user id")
   val user : User = TODO()
}

// UserProfileFragment
private val viewModel: UserProfileViewModel by viewModels(
   factoryProducer = { SavedStateVMFactory(this) }
   ...
)
```

Bây giờ, chúng ta cần thông báo cho UI biết khi đối tượng user có dữ liệu. Hãy để LiveData giúp bạn làm điều này.

LiveData nắm giữ dữ liệu có thể quan sát được. LiveData tuân theo vòng đời của các thành phần như activity, fragment, service, bao gồm cả việc dọn dẹp logic để tránh rò rỉ bộ
nhớ.

Để kết hợp LiveData vào ứng dụng của chúng ta, chúng ta sẽ thay đổi kiểu dữ liệu của biến trong UserProfileViewModel thành `LiveData<User>`. Bây gờ UserProflieFragment sẽ được
thông báo khi có dữ liệu thay đổi. Hơn nữa, LiveData là lifecycle aware, nó sẽ tự động xóa tham chiếu nếu trong thời gian dài không sử dụng.

`UserProfileViewModel`

```
class UserProfileViewModel(
   savedStateHandle: SavedStateHandle
) : ViewModel() {
   val userId : String = savedStateHandle["uid"] ?:
          throw IllegalArgumentException("missing user id")
   val user : LiveData<User> = TODO()
}
```

Bây giờ, hãy thay đổi UserProfileFragment để lắng nghe dữ liệu thay đổi:

`UserProfileFragment`

```
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
   super.onViewCreated(view, savedInstanceState)
   viewModel.user.observe(viewLifecycleOwner) {
       // update UI
   }
}
```

Bất cứ khi nào dữ liệu về user thay đổi, callback onChange() sẽ được gọi và UI sẽ được cập nhật.

Nếu bạn sử dụng quen thuộc các thư viện khác cũng có chức năng quan sát dữ liệu, thì bạn nhận ra rằng chúng ta không cần phải tạm dừng lắng nghe dữ liệu 1 cách thủ công trong
callback `onStop()` của Fragment. Điều này là không cần thiết vì LiveData là 1 lifecycle aware, tức là nó sẽ không gọi `onChange()` trừ khi fragment đang ở trạng thái hoạt động; nó sẽ nhận thông báo thay đổi ở `onStart()` nhưng `onStop()` thì không. LiveData cũng tự động xóa các observer khi fragment bị hủy, tức `onDestroy()` được gọi.

Chúng ta không thêm bất kỳ logic xử lý nào khi cấu hình thay đổi, ví dụ như khi quay màn hình. `UserProfileViewModel` tự động khôi phục khi cấu hình thay đổi, vì vậy ngay sau fragment được tạo, nó sẽ nhận được cùng 1 thể hiện của ViewModel, và callback được gọi ngay lập tức sử dụng dữ liệu hiện tại. Vì ViewModel tồn tại lâu hơn các View nên bạn không nên đưa tham chiếu của View vào ViewModel. Để biết thêm thông tin về vòng đời của ViewModel xem thêm [tại đây](https://developer.android.com/topic/libraries/architecture/viewmodel#lifecycle).
