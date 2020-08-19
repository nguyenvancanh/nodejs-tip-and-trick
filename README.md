Mặc dù mới được ra đời vào năm 2009, nhưng hiện nay, NodeJs đã trở thành một nền tảng được đông đảo những lập trình viên trên thế giới lựa chọn cho ứng dụng của mình. Theo thống kê của Stack Overflow, năm 2019 thì NodeJs đã dành vị trí số 1 với hơn 50% người dùng (trên tổng số người tham gia khảo sát ). Không quá khi nói NodeJs đang là một xu hướng mới của các nhà lập trình viên trên thế giới. Nếu bạn là một nhà phát triển phần mềm, đặc biệt là phát triển ứng dụng web thì không có lý do gì mà chúng ta không cập nhật công nghệ mới, để bắt kịp với xu hướng phát triển của cả thế giớiđúng không nào ? 

Lý do nên học NodeJS:

- NodeJS được viết bằng Javascript, mà nếu đã là một lập trình viên web, thì chắc hẳn các bạn không còn quá xa lạ gì với Javascript đúng không nào => không quá khó hiểu khi học.
- NodeJs có cộng đồng người xử dụng lớn mạnh => nếu bạn cần hỗ trợ, sẽ nhanh chóng tìm được câu trả lời
- Nhờ cơ chế xử lý bất đồng bộ, nên NodeJs có tốc độ xử lý nhanh => giải quyết được cho bạn nhưng bài toán về performance
- Dễ dàng mở rộng, với việc dùng nodeJs thì vấn đề mở rộng website của bạn không còn là vấn đề quá lớn nữa. 

Với tất cả nhưng ưu điểm trên, có xứng đáng để bạn bỏ thời gian ra học và nghiên cứu nodeJs không nào ? Theo cá nhân mình thì hoàn toàn xứng đáng đó. 

Trong quá trình học một ngôn ngữ hay một framework mới chắc chắn là chúng ta sẽ mắc phải nhưng sai lầm nhất định, NodeJS cũng vậy. Trong bài viết này mình xin phép tổng hợp lại các "tip" mà những người đi trước đã gặp phải và rút ra. Hy vọng sẽ giúp ích được cho mọi người trong quá trình học Node


**1. Nodemon cho develop - pm2 cho product**

Có một điều tương đối phiền hà cho những người mới học node, đó là khi bạn thay đổi source code, bạn phải chạy lại server node để cập nhật code mới, điều này đôi khi sẽ làm bạn phát điên vì phải thao tác chuyển qua lại giữa các tab nhiều lần đúng không nào. Nếu bạn tìm hiểu thêm thì sẽ thấy có tool cho phép bạn không cần chạy lại server mỗi lần thay đổi code - đó là Nodemon. Cài đặt nodemon vào project của bạn bằng lệnh

```
$ npm install -g nodemon
```
Bạn có thể tìm hiểu thêm về nodemon ở trang [https://github.com/remy/nodemon](https://github.com/remy/nodemon)

Nodemon là một công cụ tuyệt vời, khi bạn đã cài đặt nó rồi, bạn có thể chạy file node script thông qua câu lệnh

```
$ nodemon [tènfile].js
```

Vậy trên production thì sao? Nếu bạn sử dụng Herojku, Nodejetsu, .. hay bất kỳ một nhà cung cấp server tương tự khác (trên đây đã config sẵn cho ứng dụng node của bạn luôn chạy ) thì không cần quá quan tâm đến vấn đề này. Nhưng nếu như bạn đang dùng EC2, hay bất kỳ một nhà cung cấp server cloud khác, làm cách nào để đảm bảo rằng ứng dụng node của bạn luôn chạy.? Câu trả lời cho vấn đề này là bạn sẽ dùng công cụ **PM2**. Bạn có thể tìm hiểu thêm ở đây [https://github.com/Unitech/pm2](https://github.com/Unitech/pm2). PM2 là một công cụ tương tư như nodemon, nhưng được dùng cho production. Nó sẽ giám sát các file của bạn, mỗi khi có thay đổi hay một bản deploy mới, thì code trên server của bạn sẽ được tự động cập nhật. Khác với nodemon, khi **pm2** bị crash, thì nó sẽ ngay lập tức khởi động lại node app của bạn.

PM2 cũng vượt trội khi bạn cần mở rộng ứng dụng của mình thành multiple core. PM2 đi kèm vợi một load balance (bộ cân bằng tair) cho phép bạn dễ dàng chỉ định số lượng instance mà ứng dụng của bạn sẽ chạy. Câu lệnh để chạy như sau:

```
$ pm2 start app.js -i max
```
Tham số -i là số lượng instance mà bạn muốn chạy, trong trường hợp này PM2 đi kèm với một hằng số là max, sẽ tự động điều chỉnh theo số core bạn có. 

**2 Async or Q **

Bạn càng viết nhiều ứng dụng nodejs, bạn càng thấy việc sử dụng callback giống như một mê cung vậy, nếu bạn chưa hình dung được nó là gì thì xem ví dụ sau ddaya:

```
function register(name, password, cb){
  checkIfNameExists(name, function(err, result){
   if(err){
    return cb(“error”);
   }
   checkIfPasswordGood(password, function(err, result){
    if(err){
     return cb(“error”);
    }
    
    createAccount(name,password, function(err,result){
     if(err){
      return cb(“error”);
     }
     createBlog(name, function(err, result){
      sendEmail(name, function(err, result){
       callback(result);
      });
     });
    });
   });
  });
 }
```

Mặc dù nó có thể không phải là một đoạn code hữu ích, hoặc hoàn hảo nhất. Nhưng nó là là ví dụ tuyệt vời cho bạn hình dùng được callback trong node js nó phức tạp thế nào. Làm sao để chúng ta có thể tránh gặp phải những đoạn code tương tuwj? 

Cách đơn giản nhất là sử dụng event, nhưng như vậy đôi khi chúng ta sẽ gặp phải tình trạng đó là dùng event gọi tới một hàm private, những hàm chỉ có duy nhất một chức năng. 

Vậy làm cách nào để bạn có thể giải quyết được vấn đề trên, có 2 thư viện để giúp bạn giải được bài toán trên đó là async.js và Q. Cả 2 đều có nhưng giải pháp riêng biệt để ngăn chặn việc sử dụng "callback hell"

Async.js hay gọi ngắn gọn lại là async cho phép bạn dễ dàng thực thi các hàm theo cách tuần tự, hoặc song song mà không cần lồng cúng vào nhau. Bạn có thể tham khảo thêm về async trên trang (https://github.com/caolan/async)[https://github.com/caolan/async]. Dưới đây là một số template mà async hỗ trợ dựa vào tài liệu readme mà họ cung cấp

```
async.map([‘file1',’file2',’file3'], fs.stat, function(err, results){
  // results is now an array of stats for each file
 });
 
 async.filter([‘file1',’file2',’file3'], fs.exists, function(results){
 // results now equals an array of the existing files
});
 
 async.parallel([
  function(){ … },
  function(){ … }
  ], callback);
 
 async.series([
  function(){ … },
  function(){ … }
  ]);
 
 async.waterfall([
  function(callback){
   callback(null, ‘one’, ‘two’);
  },
  function(arg1, arg2, callback){
   callback(null, ‘three’);
  },
  function(arg1, callback){
 // arg1 now equals ‘three’
 callback(null, ‘done’);
 }
 ], function (err, result) {
 // result now equals ‘done’ 
});
```

Nếu chúng ta áp dụng async vào đoạn code register ở đầu bài toán, chúng ta sẽ được một đoạn code vô cũng dễ đọc.

Một thư viện khác cũng hữu ích không kém đó là Q, các bạn có thể tham khảo thêm tại link (https://github.com/kriskowal/q)[https://github.com/kriskowal/q]. Thư viện này làm việc chủ yếu dựa vào promise. Một promise cơ bản là một object được trả về từ một phương thức với promise là cuối cùng sẽ trả về một object. Mối quan hệ này hoàn toàn thích hợp với bản chất bất đồng bộ của javascript và nodejs

Một số ví dụ trên trang chủ của Q:

```
 promiseMeSomething()
 .then(function (value) {
 }, function (reason) {
 });

```

Hàm promise me sẽ trả về một đối tượng ngay lập tức. Gọi "then" để trả về giá trị bạn mong muốn. Đây là một cách vô cùng ngắn gọn để tránh sự phức tạp của việc callback

```
Q.fcall(checkIfNameExists)
 .then(checkIfPasswordIsGood)
 .then(createAccount)
 .then(createBlog)
 .then(function (result) {
 // Do something with the result
})
 .catch(function (error) {
 // Handle any error from all above steps
})
 .done();
 
```

**3. Debug trong NodeJs** 

Debug trong NodeJs sẽ hơi khó hiểu nếu bạn đang học Java hay C# mà chuyển sang học NodeJS. Phần lớn những người mới lập trình nodejs sẽ sử dụng console.log để tiến hành debug. Tuy nhiên, vẫn có những lựa chọn khác mang tính quy ước hơn để tiến hành debug. Nodejs được xây dựng với việc tích hợp sẵn debug với việc gọi node debug. Tuy nhiên có một công cụ được nhiều developer lựa chọn hơn nữa đó là node-inspector. 

Node inspector là một debuger interface sử dụng  Blink Developer Tools. Tóm lại, Node - inspector cho phép bạn gỡ lỗi ứng dụng của bạn bằng bất kỳ editor nào bạn chọn hay đơn giản nhất là trực tiếp trên trình duyệt chorome. Cho phép bạn thực hiện một số điều thực sự thú vị, như là thay đổi mã code trực tiếp, gỡ lỗi từng step. Để cài đặt vào project của mình bạn làm theo link sau: (https://github.com/node-inspector/node-inspector)[https://github.com/node-inspector/node-inspector]

**4. Nodefly**

Sau khi hoàn thành và chạy ứng dụng của mình, bạn có khi nào tự hỏi là làm cách nào để theo dõi và tạo báo cáo để đảm bảo ứng dụng của bạn đang chạy một cách tối ưu nhất hay không ? Có một thư viện vô cùng đơn giản cho phép bạn thực hiện điều này mà không cần phải tốn quá nhiều công sức đó là **Nodefly**. Nodefly chỉ là những dòng code đơn thuần, với mục đích là theo dõi ứng dụng của bạn có bị tràn bộ nhớ hay không, đo lường các truy vấn tới redis, mongodb và một số ứng dụng thú vị khác.

Xem thêm hướng dẫn và cài đặt tại link

(http://www.nodefly.com)[http://www.nodefly.com]


**6. Sử dung NPM để quản lý các module**

Một trong những việc phổ biến và cơ bản nhất trong lập trình nodejs đó là bạn sẽ cần phải cài đặt các package cũng như các thư viện cần thiết vào project của mình thông qua npm. Node có một file để quản lý các package trong project đó là file package.json. Tuy nhiên, một vấn đề mà tất cả nhứng beginer gặp phải đó là giữ cho file package.json này luôn được cập nhật với các package bạn đang làm việc trong project. Bạn sẽ luôn phải mở file package.json và cập nhật thêm những module mới mà bạn mới cài đặt. Thao tác tưởng chừng vô cùng đơn giản nhưng nhiều beginer lại không biết, đó là khi cài đặt thì npm sẽ làm điều này cho bạn. Chỉ với thao tác vô cùng đơn giản 

```
npm install — save module_name 
```

Chỉ cần như vậy là npm sẽ tự động update file package.json với tên module và vesion mà bạn vừa cài đặt.

**6. Không check trong node_mudule folder **

Mặc dù chủ đề của chúng ta là về node và modules, nhưng chắc hẳn nhiều bạn không biết rằng chúng ta không nên check trong thư mục node_modules của mình. Lý do đằng sau việc này, là bất kỳ khi nào có ai đó muốn kiểm tra source code của bạn, họ chỉ cần chạy npm install và tải xuống tất cả các module cần thiết. Bạn có thê nói rằng, việc check node_modules không phải là vấn đề quá to tát và nghiêm trọng. Nhưng điều gì xảy ra nếu người kiểm tra source code của bạn không dùng hệ điều hành tương đồng với bạn ? Và một trong số các module mà bạn xử dụng được biên dịch thông qua quá trình chạy npm ? Khi người kiểm tra source code của bạn chắc chắn
sẽ gặp lỗi, mà không biết nguyên nhân tại sao. Ví dụ như các module bcrypt và sentimental  được biên dịch trên máy chủ khi bạn biên dịch chúng vì chúng có thành phần gốc được viết bằng C.

Cách tốt nhất là thêm node_modules của bạn vào file .ignore

```
// .gitignore node_modules/*
```

**7. Đừng quên return**




