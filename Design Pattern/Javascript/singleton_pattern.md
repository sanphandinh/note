# The Singleton Pattern

Singleton Pattern là pattern mà nó hạn chế việc khởi tạo của một lớp bằng một **"một"** instance duy nhất. Nó là hữu ích khi cần chính xác một đối tượng để điều phối các hành động trên toàn hệ thống.

Singleton pattern có thể được thực hiện bằng việc tạo một lớp với một phương thức mà tạo ra một **instance** của class nếu nó chưa tồn tại. Trong trường hợp nó **instance** tồn tại rồi, thì hàm đó đơn giản là trả về **instance** đó.

Đây là một ví dụ cho việc khởi tạo một Singleton trong Javascript

```js
var mySingleton = (function () {

  // Instance stores a reference to the Singleton
  var instance;

  function init() {

    // Singleton

    // Private methods and variables
    function privateMethod(){
        console.log( "I am private" );
    }

    var privateVariable = "Im also private";

    var privateRandomNumber = Math.random();

    return {

      // Public methods and variables
      publicMethod: function () {
        console.log( "The public can see me!" );
      },

      publicProperty: "I am also public"

      getRandomNumber: function() {
        return privateRandomNumber;
      }

    };

  };

  return {

    // Get the Singleton instance if one exists
    // or create one if it doesn't
    getInstance: function () {

      if ( !instance ) {
        instance = init();
      }

      return instance;
    }

  };

})();
```

_Khả năng ứng dụng_ của Singleton pattern được mô tả:

- Có chính xác 1 instance của một class và nó phải có thể truy cập bởi client từ điểm truy cập mà ai cũng biết
- Instance duy nhất phải có thể mở rộng bằng cách phân lớp và các client có thể sử dụng phiên bản instance mở rộng mà không phải sửa đổi code của chúng.

Ý thứ 2 thường được mô tả như sau:

```js
mySingleton.getInstance = function () {
  if (this._instance == null) {
    if (isFoo()) {
      this._instance = new FooSingleton();
    } else {
      this._instance = new BasicSingleton();
    }
  }
  return this._instance;
};
```

Nhìn đoạn code trên, chúng ta không cần cập nhật mỗi điểm của code khi truy cập vào instance. __FooSingleton__ nên là một subclass của __BasicSingleton__ và được thực hiện như nhau. 

Một điều quan trọng về sự khác nhau giữa static instance của class và một singleton là: trong khi một singleton có thể được thực hiện như một static instance, nó cũng có thể __constructed lazily__, mà không sử dụng tài nguyên hoặc memory, cho đến khi static instance cần.

Tuy nhiên việc sử dụng Singleton Pattern trong hệ thống là dấu hiệu cho thấy __hệ thống được kết hợp chặt chẽ với nhau__ hoặc __logic được trải rộng quá mức trên nhiều phần mã__. Singleton có thể gây ra __khó khăn test__.