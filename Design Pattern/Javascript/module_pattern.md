# The Module Pattern

Module là một mảnh ghép của bất kỳ kiến trúc ứng dụng mạnh mẽ nào và thường giúp cho việc phân tách và tổ chức code trong ứng dụng.

Trong Javascript, có một vài phương thức để triển khai module:

- Object literal notation
- Module pattern
- AMD modules
- CommonJS modules
- ECMAScript Harmony modules

Module pattern một phần được dựa trên Object Literals.

## Object Literal

**Object literal** là một object mà được mô tả như một tập các cặp giá trị **name/value** phân cách nhau bởi dấu **","** và được phân phối trong cặp dấu ngoặc nhọn **({})**. Một điều cần chú ý là **Object Literals** không yêu cầu sử dụng **new operator**.

Ví dụ:

```js
var myModule = {
  myProperty: 'someValue',

  // object literals can contain properties and methods.
  // e.g we can define a further object for module configuration:
  myConfig: {
    useCaching: true,
    language: 'en',
  },

  // a very basic method
  myMethod: function () {
    console.log('Where in the world is Paul Irish today?');
  },

  // output a value based on the current configuration
  myMethod2: function () {
    console.log(
      'Caching is:' + this.myConfig.useCaching ? 'enabled' : 'disabled'
    );
  },

  // override the current configuration
  myMethod3: function (newConfig) {
    if (typeof newConfig === 'object') {
      this.myConfig = newConfig;
      console.log(this.myConfig.language);
    }
  },
};

// Outputs: Where in the world is Paul Irish today?
myModule.myMethod();

// Outputs: enabled
myModule.myMethod2();

// Outputs: fr
myModule.myMethod3({
  language: 'fr',
  useCaching: false,
});
```

Sử dụng Object literals có thể hỗ trợ cho việc đóng gói và phân phối code.

## Module Pattern

Module pattern được định nghĩa như một cách để cung cấp cơ chế đóng gói mà hỗ trợ **private** và **public**.

Trong JS, module pattern được sử dụng để mô phỏng một vài khái niệm của **class** như cung cấp các **phương thức public/private** và các biến bên trong một đối tượng, do đó bảo vệ các phần trong module với **global scope**. Điều này giảm được vấn đề về xung đột tên

### Privacy (Tính riêng tư)

Module pattern đóng gói trạng thái riêng tư và phân phối sử dụng **closures**. Với cách này, chỉ những **public API** được trả về, và giữ mọi thứ còn lại trong closure private.

Nó đưa cho chúng ta một giải pháp trong sáng để bảo vệ các logic thực hiện trong khi vẫn cung cấp giao diện sử dụng cho các thành phần khác.

Không giống các ngôn ngữ truyền thống khác, JS không có modifier, các biến về kỹ thuật không thể được khai báo public hay private, và vì vậy chúng ta sử dụng các khái niệm **function scope** để giải lập nó.

### Ví dụ

Một ví dụ về thiết lập module

```js
var testModule = (function () {
  var counter = 0;

  return {
    incrementCounter: function () {
      return ++counter;
    },

    resetCounter: function () {
      console.log('counter value prior to reset: ' + counter);
      counter = 0;
    },
  };
})();

// Usage:

// Increment our counter
testModule.incrementCounter();

// Check the counter value and reset
// Outputs: 1
testModule.resetCounter();
```

## Biến thể module pattern

### Import Mixins

Biến thể này mô tả làm cách nào các biến global (ví dụ: Jquery, Underscore) có thể được truyền vào như một tham số trong module function.

Cách làm là truyền các biến global như tham số tới hàm ẩn danh (tạo ra module).

Ví dụ cho ta thấy:

```js
// Global module
var myModule = (function (jQ, _) {
  function privateMethod1() {
    jQ('.container').html('test');
  }

  function privateMethod2() {
    console.log(_.min([10, 5, 100, 2, 1000]));
  }

  return {
    publicMethod: function () {
      privateMethod1();
    },
  };

  // Pull in jQuery and Underscore
})(jQuery, _);

myModule.publicMethod();
```

### Export (khá khó hiểu để tạm bản tiếng anh)

This next variation allows us to declare globals without consuming them and could similarly support the concept of global imports seen in the last example.

```js
// Global module
var myModule = (function () {
  // Module object
  var module = {},
    privateVariable = 'Hello World';

  function privateMethod() {
    // ...
  }

  module.publicProperty = 'Foobar';
  module.publicMethod = function () {
    console.log(privateVariable);
  };

  return module;
})();
```

### Một số thiết lập module pattern của công cụ và framework

- Dojo
- ExtJS
- YUI
- Jquery

## Ưu nhược điểm

### Ưu điểm

- Nó rõ ràng hơn nhiều cho các lập trình viên có nền tảng từ Object-Oriented hơn là ý tưởng đóng gói thực sự, ít nhất là từ phía Javascript

- Nó hỗ trợ private data. Trong module pattern, thành phần public có thể chạm tới được các phần private, tuy nhiên các thành phần bên ngoài không thể chạm tới.

### Nhược điểm

Chúng ta truy cập đồng thời cả thành phần public và private khác nhau, khi chúng ta muốn thay đổi **visibility**, chúng ta thực sự phải tạo những thay đổi trên mỗi nơi thành phần đó được sử dụng.

Chúng ta cũng không thể truy cập các **thành phần private** trong phương thức mà được thêm sau này.

Khó khăn trong unit test các thành phần private
