Hello mọi người, [bài viết hôm trước](https://viblo.asia/p/huong-dan-tao-1-ung-dung-android-bang-flutter-nodejs-phan-2-oOVlYeOBl8W) mình đã hướng dẫn các bạn crawl dữ liệu để phục vụ cho bài hôm nay. Nội dung hướng dẫn bài hôm nay sẽ là tạo server và cung cấp API chuẩn bị cho app. Dự là bài hôm nay khá dài và đụng chạm nhiều kiến thức, các bạn cần chuẩn bị các kiến thức cơ bản sau:
*  GIT
*  JavaScript, ES6, mongoose
* MongoDb, bạn có thể xem thêm [tutorial](https://www.tutorialspoint.com/mongodb/index.htm) 
* Express NodeJs, bạn có thể xem thêm [tutorial](https://www.tutorialspoint.com/nodejs/nodejs_express_framework.htm)

Yều cầu thiết lập các môi trường cần thiết:
* IDE: intellij (bạn có thể sử dụng Visual Studio Code, Sublime Text....)
* NodeJs [tại đây](https://nodejs.org/en/download/)
* Mongodb [tại đây](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

Mục tiêu của bài này sẽ làm được các API cung cấp cho app, bao gồm:
* get list category:
> BASE_URL/category

> https://gentle-fjord-54250.herokuapp.com/category

* get list recipe by category:
> BASE_URL/category/{category_id}

> https://gentle-fjord-54250.herokuapp.com/category/5d67293df35b984e744868e0

* get recipe detail by id:
> BASE_URL/recipe/{recipe_id}

> https://gentle-fjord-54250.herokuapp.com/recipe/5d6f2d0c89771377812fbad2

* search recipe by name:
> BASE_URL/recipe?q={name}

> https://gentle-fjord-54250.herokuapp.com/recipe?q=m%C3%A1t+ng%E1%BB%8Dt

BASE_URL khi làm ở local thì sẽ là: `localhost:8081` và khi deploy trên host sẽ là `gentle-fjord-54250.herokuapp.com` (tùy vào các bạn đăng ký tên miền)

Trước tiên, bạn hãy nhìn vào mô hình sau để thấy rõ các mối quan hệ giữa app, server, database như thế nào.
![](https://images.viblo.asia/13cd8e65-bc66-47d1-b456-111082d830f5.png)

- Database chính là nơi chứa các thông tin để cho app hiển thị, chính là phần dữ liệu crawl
- Server sẽ là nơi tiếp nhận các request từ app, sau đó xử lý request (phân tích reques, truy vấn database để lấy dữ liệu...), cuối cùng là trả về response dưới dạng API cho app.
- App sẽ get các thông tin từ API để hiển thị trên giao diện.

Cơ bản luồng thực hiện sẽ là như vậy. Buớc đầu tiên mình sẽ làm việc với Database.

### Cấu trúc Database
Ở đây mình sử dụng db dạng NoSQL là Mongodb. Bạn cần cài đặt mongodb trước khi làm các bước tiếp theo nha

Đầu tiên mình sẽ tạo 1 database có tên là **cooking_db**
> use cooking_db

Tiếp theo sẽ là tạo 1 document là **category**
> db.createCollection("category")

Trong db chỉ chứ duy nhất 1 collection với cấu trúc như sau
```json
[
  {
    // category 1
    // name: tên danh mục
    // images: hình ảnh mô tả danh mục
    // recipes: danh sách các món ăn
    name: "",
    image: [],
    recipes: [
        {
          // recipe
          // name: tên món ăn
          // time: thời gian làm
          // img: hình ảnh món ăn
          // des: cách làm chi tiết....
          // và 1 số thông tin khac
        }
      ]
  },
  {
    // category 2
    // name: tên danh mục
    // images: hình ảnh mô tả danh mục
    // recipes: danh sách các món ăn
    name: "",
    image: [],
    recipes: [
        {
          // recipe
          // name: tên món ăn
          // time: thời gian làm
          // img: hình ảnh món ăn
          // des: cách làm chi tiết....
          // và 1 số thông tin khac
        }
      ]
  },
  {
    ...
  }
]
```
Tất cả dữ liệu crawl được mình đã tổng hợp lại thành 1 file, bạn có thể tải [ở đây](https://drive.google.com/file/d/1xi_eDTVhaExnCvT_QVImuWPCjTOF9Hdo/view?usp=sharing). Sau khi tải về bạn sẽ thực hiện import nó vào db với cú pháp như sau:
> mongoimport --db dbName --collection collectionName --file fileName.json --jsonArray

Trong đó: 
- dbName là cooking_db
- collectionName là category
- fileName.json là category.json

Hãy nhớ là mở termial để thực hiện câu lệnh này ở cùng folder chứa file json nha, nếu không thì đường dẫn bị sai là không chạy nhé. 

Sau khi import thành công, bạn có thể kiểm tra trong collection đã có dữ liệu chưa, bằng câu lệnh sau:
> db.category.find()

Nếu kết quả hiện ra đống dữ liệu giống như trong file category.json thì tức là bạn đã import thành công. Nếu không thấy gì thì quá trình import đã gặp vấn đề, bạn kiểm tra cẩn thận lại từng bước rồi import lại, nếu vẫn không được hãy comment bên dưới bài viết, mình sẽ xem giùm. Đến đây bạn đã hoàn thành cấu trúc database.

Và để server có thể connect được đến database thì bạn phải start mongodb trong suốt quá trình server chạy nha. Bạn có thể xem cách start [ở đây](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

### Server p1: khởi tạo project
Đâu tiên, bạn cần tạo 1 project để làm việc, ở đây mình sử dụng intellij và tạo 1 project js. Vì thằng intellij này nó hỗ trợ tạo sẵn cho mình vài file cấu hình cơ bản cho project. Bạn có thể tạo 1 project trống rồi tự thêm mấy file này cũng được, không vấn đề gì.

Cấu trúc project sẽ gồm các package và các file cơ bản như sau:

![](https://images.viblo.asia/07fb87bf-ee85-4a6a-a592-8526f458e03a.PNG)

Trong đó:
- controller: là nơi chính xử lý các request
- model: là nơi định nghĩa các class
- router: là nơi tiếp nhận các request từ app gửi lên, rồi điều hướng đến các controller tuơng ứng để xử lý request
- public: là nơi chứa các rerouce như ảnh, video
- file .gitignore cấu hình cho git, bạn có thể bỏ qua hoặc download [ở đây](https://github.com/nvquangth/cooking-server/blob/master/.gitignore)
- file Procfile cấu hình khi server deploy trên hệ thống, ở đây mình mới chỉ làm ở local, nên phần này bạn tạm thời bỏ qua
- file README.md cấu hình cho git, bạn có thể bỏ qua
- file app.js khởi chạy server, rồi connect database, và các cấu hình cơ bản khác cho sever
- file package.json là file khai báo các dependencies, dùng để import các module/library cần dùng
Cấu trúc file như thế này:
``` json
{
  "name": "cooking-server",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/nvquangth/cooking-server.git"
  },
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/nvquangth/cooking-server/issues"
  },
  "homepage": "https://github.com/nvquangth/cooking-server#readme",
  "dependencies": {
    "express": "^4.17.1",
    "mongoose": "^5.6.12"
  },
  "devDependencies": {},
  "keywords": [],
  "description": ""
}
```
Ở đây bạn chỉ cần quan tâm đến 2 mục chính là:
> "main": "app.js",

và

>  "dependencies": {
    "express": "^4.17.1",
    "mongoose": "^5.6.12"
  },
  
main sẽ chỉ định file được chạy đầu tiên khi run project, dependencíe thì như trên mình nói là sẽ chứa các module/library cần dùng. Ở đây mình sử dụng express và mongoose nên cần cài đặt 2 module này. Việc cài đặt thì bạn chỉ cần vài câu lện npm là xong à, bạn tự kiếm chỗ này nha, dễ lắm à. Mình để link hướng dẫn ở đây: [nmp expres](https://www.npmjs.com/package/express) ,[ npm mongoose](https://www.npmjs.com/package/mongoose). Trong đó:
- express: là 1 framework js giúp bạn tạo ra các API dễ và nhanh chóng, đúng với cái tên của nó
- mongoose: giúp bạn tạo ra các Schema thao tác với database mongodb

OK, phần giải thích và setup 1 số thông tin cơ bản cho project đã xong, tiếp theo mình sẽ đi vào file app.js, đây là file khởi đầu mọi chuyện.
![](https://images.viblo.asia/cb252ed7-21b8-4543-9e02-dcc2247960fa.PNG)

Mình muốn các bạn chỉ cần các quan tâm các vùng mình khoanh đỏ, các phần khác có thể tạm bỏ qua. Trong đó:
- 1, 2 là khai báo import các module/library
- 3 là phần connect tới database mình vừa tạo ấy
- 4 là khởi tạo server

Có thể các bạn chưa hiểu, hay hiểu chưa hết thì cứ làm theo mình để code chạy đưoc đã. Các bạn yên tâm là lúc mới làm mình cũng chả hiểu mấy đâu, cứ chạy được cái cho nó sướng rồi tính tiếp. Đến đây bạn run project bằng IDE hoặc dùng lệnh để chạy cũng đựoc
> node app.js

kết quả mà in ra dòng "Server is running ..." "Connection successful" là khởi tạo server và connect tới database thành công rồi. Nếu có lỗi thì bạn hãy kiểm tra các phần 1, 2 xem import đã đúng chưa, phần 3 thì xem mongodb có đang được start hay không, tên đường dẫn tới db có đúng không, phần 4 thì xem cú pháp có đúng chưa.

### Server p2: tạo model
Theo như cấu trúc collection mà mình đã nêu ở trên thì mình sẽ tạo ra các model tuơng ứng để map với format này. Ở đây mình cần tạo 4 model là: category, recipe, component, cook_step, tương ứng sẽ là 1 file .js như sau:

category.js
```javascript
const CategorySchema = new mongoose.Schema({
    id: String,
    name: String,
    images: [{
        type: String
    }],
    recipes: [{
        type: RecipeSchema
    }],
    total_recipes: Number
}, {
    collection: 'category'
});
```

recipe.js
```js
const RecipeSchema = new mongoose.Schema({
    name: String,
    img: String,
    level: String,
    des: String,
    time: Number,
    serving: Number,
    components: [{
        type: ComponentSchema
    }],
    cook_steps: [{
        type: CookStepSchema
    }]
});
```

component.js
```js
const ComponentSchema = new mongoose.Schema({
    name: String,
    quantity: String,
    unit: String
});
```

cook_step.js
```js
const CookStepSchema = new mongoose.Schema({
    step: Number,
    des: String,
    pictures: [{
        type: String
    }]
});
```

Ok, bước này chỉ đơn giản vậy thôi. Bạn có thể tham khảo code của mình [tại đây](https://github.com/nvquangth/cooking-server/tree/master/model)

Đến đây bạn đã đi được 1 nửa chặng đường của việc tạo server. Vì phần này khá là dài nên mình sẽ chia thành 2 bài cho các bạn đỡ ngại, bài tiếp theo mình sẽ nói tiếp về cách nhận request từ client, truy vấn database và trả về API nữa là xong. À còn 1 vấn đề nữa là deploy lên server chạy được như link mình để trên đầu bài kìa. Còn bây giờ bạn hãy thực hành để làm được 1/2 đã nha.

Hẹn gặp các bạn ở bài sau.
