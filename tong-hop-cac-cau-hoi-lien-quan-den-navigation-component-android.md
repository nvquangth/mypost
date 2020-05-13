Như các bạn đã biết, [Navigation](https://developer.android.com/guide/navigation) là 1 trong những thành phần khá hay trong Android Jetpack được Google giới thiệu trong năm 2018. Việc cấu hình và sử dụng Navigation chắc hẳn không còn quá xa lạ gì với mọi người nữa. Do vậy việc code và chạy được sẽ không phải là vấn đề mình quan tâm ở đây nữa. Mà ở bài viết này, mình sẽ chia sẻ những câu hỏi hay những vấn đề gặp phải trong quá trình sử dụng Navigation.

**1. Những lợi ích mà Navigation đem lại là gì?**
* Giúp xử lý dễ dàng fragment transactions
* Giúp xử lý sự kiện nhấn Back và Up 1 cách chính xác
* Hỗ trợ animation và transaction cơ bản
* Hỗ trợ thực hiện [deep link](https://developer.android.com/guide/navigation/navigation-deep-link)
* Hỗ trợ việc navigate kết hợp với các UI pattern có sắn như: Toolbar, Drawer Layout, Bottom Navigation
* Hỗ trợ [Safe Args](https://developer.android.com/guide/navigation/navigation-pass-data#Safe-args)

**2. Navigation cấu thành từ những thành phần nào?**
* Graph xml
* NavHostFragment
* NavController

**3. NavHostFragment sử dụng FragmentManager hay ChildFragmentManager?**
ChildFragmentManager

**4. Có thể sử dụng Navigation với multiple Activity hay không? Nếu có, thì ở Activity này có thể  sử dụng chung NavHostFragment ở Activity kia không?**

Có. Nhưng khi tạo activity mới thì cũng phải tạo thêm NavHostFragment nếu muốn thực hiện các transaction trong activity này.

**5. Có pass 1 đối tượng không phải Seriable hoặc Parcelable thông qua argument của Navigation không?**

Không pass được. Lỗi run-time Object is not Serializable or Parcelable.

**6. Có bao nhiêu cách để pass data giữa các fragment?**

2 cách:
* Thông thường
* Sử dụng [Safe Args](https://developer.android.com/guide/navigation/navigation-pass-data#Safe-args)

**7. Nếu sử dụng phương thức setupActionBarWithNavController() thì title của toolbar sẽ là giá trị của thuộc tính label trong thẻ <fragment> của graph. Vậy nếu trong phương thức onCreateView() của Fragment ta set lại title cho Actionbar thì lúc này title của Actionbar sẽ nhận giá trị nào?**
    
title bị ghi đè khi set trong code
    
**8. Trong phương thức navigate(id), id ở đây nên sử dụng id của action hay id của destination?**
    
Nên sử dụng id của action. Vì sử dụng action để cung cấp thông tin 1 cách trực quan về destination và có thể kết hợp với safeArgs để thay thế cho id của destination. Hơn nữa cũng có thể sử dụng với animation.
    
**9. Có thể thực hiện Unit Test với Navigation được không?**
    
[Có](https://developer.android.com/guide/navigation/navigation-testing)
    
**10. Khi kêt hợp Navigation Componet với Bottom Navigation thì có nên quan tâm đến việc giữ lại back stack của mỗi tab hay không?**
    
Còn tùy
- Bản chất design của Bottom Nav không cho giữ lại state ở mỗi tab
- Thực tế nhiều trường hợp để cải thiện user exp thì cần giữ lại state
- Với [sample](https://github.com/googlesamples/android-architecture-components/blob/296c08826e35450080328ac2c3870d198627c4c2/NavigationAdvancedSample/app/src/main/java/com/example/android/navigationadvancedsample/NavigationExtensions.kt#L33) thì google có nói rằng sẽ support việc này
- Tuy nhiên, nếu giữ lại state, thì việc điều hướng này có độ phức tạp cao giữa các tab với nhau.
    
**11. Khi kết hợp Navigation Component với Bottom Navigation thì nếu muốn giữ lại back stack ở mỗi tab thì làm như thế nào?**
    
Sử dụng mutilple NavHostFragment, tức là mỗi tab sẽ là 1 NavHostFragment tương ứng
    
**12. Khi thực hiện deeplink thì back stack có được giữ lại không?**
    
[Không](https://developer.android.com/guide/navigation/navigation-deep-link#explicit)
    
**13. Khi nhiều màn hình cần navigate() đến 1 màn hình xác định thì cần phải tạo ra 1 số lượng action tương ứng với số màn hình đó. Trong trường hợp này, có cách nào để tránh việc tạo ra nhiều action không?**
    
Có, sử dụng [global action](https://developer.android.com/guide/navigation/navigation-global-action)
    
**14. popUpTo="@id/fragmentX" : pop trong back stack cho đến khi gặp fragmentX
popUpToInclusive="true"   : pop trong back stack cho đên khi gặp fragmentX và pop luôn fragmentX ra khỏi back stack, nhưng nếu fragmentX ở root của back stack và không có thuộc tính destination thì thuộc tính này không có tác dụng**\
    
**15. Khi sử dụng <BottomNavigation>.setupWithNavController(<NavController>) thì cần chú ý điều gì để BottomNavigation có thể match với Navigation Graph**
    
id của của các item trong menu Bottom Navigation phải trùng với id của các fragment tương ứng trong Graph
    
**16. Có mấy cách để set animation với Navigation?**
    
2 cách: 
- set trong file navigation xml ở thẻ <action>
- set trong code ở phương thức findNavController().navigate(amin)
    
**17. Khi nhấn nút Back của thiết bị, thì ở Fragment có handle được sự kiện onBackPress() như ở activity không?**
    
Có, ở Fragment cung cấp callback <Activity>.addOnBackPressCallback() {}
    
**18. 1 trong các principles của Navigation là "The Up button never exits your app", bạn hiểu như thế nào về nó**
    
- Ở màn hình fixed start không hiện nút Up
- Khi thực hiện deeplink, ứng dụng được navigate đến 1 màn hình nào đó có xuất hiện nút Up, thì khi click vào nút Up này sẽ không thoát khỏi ứng dụng, mà trở về màn hình fixed start
    
**19. Có bắt được sự kiện khi destination thay đổi không?**
    
Có, ở MainActivity sử dụng: navController.addOnDestinationChangedListener {}
    
**20. Navigation có hỗ trợ shared element không?**
    
Có, sử dụng FragmentNavigatorExtras
    
**21. Có pass được 1 array object parcelable hoặc seriliable thông qua argument không?**
    
Có, định dạng type argument là .model[]
    
**22. Navigation có support dialog fragment không?**
    
Có
 
Mình có làm 1 số demo sử dụng Navigation, các bạn có thể tham khảo [tại đây](https://github.com/nvquangth/moviedb-kotlin-architecture) hoặc [tại đây](https://github.com/nvquangth/cong-thuc-nau-an-android)
    
Các câu hỏi và trả lời trên được rút ra trong quá trình tìm hiểu và thực tế làm việc của mình cùng một số thành viên trong team về [Navigation](https://developer.android.com/guide/navigation). Có thể có những ý kiến và câu trả lời khác nhau, có đúng có sai. Vì vậy rất mong được nhận được sự đóng góp của mọi người về bài chia sẻ này. Thanks all.
