# The mediator Pattern

Mediator là một design pattern mà cho phép chúng ta **expose** một giao diện chung mà thông qua đó các thành phần khác nhau có thể giao tiếp.

Nếu một hệ thống có quá nhiều các mối quan hệ trực tiếp giữa các component, lúc này cần 1 điểm tập trung mà điều khiển việc giao tiếp giữa các component. Meditor pattern thúc đẩy tạo ra sự **kết nối lỏng lẻo** bằng cách đảm bảo rằng thay vì các thành phần tham chiếu một cách rõ ràng, nó sẽ tương tác thông qua 1 điểm tập trung.

![Mediator pattern mô tả](https://learning.oreilly.com/library/view/learning-javascript-design/9781449334840/httpatomoreillycomsourceoreillyimages1547805.png)

## Cài đặt Mediator Pattern

```js
var mediator = (function () {
  // Storage for topics that can be broadcast or listened to
  var topics = {};

  // Subscribe to a topic, supply a callback to be executed
  // when that topic is broadcast to
  var subscribe = function (topic, fn) {
    if (!topics[topic]) {
      topics[topic] = [];
    }

    topics[topic].push({ context: this, callback: fn });

    return this;
  };

  // Publish/broadcast an event to the rest of the application
  var publish = function (topic) {
    var args;

    if (!topics[topic]) {
      return false;
    }

    args = Array.prototype.slice.call(arguments, 1);
    for (var i = 0, l = topics[topic].length; i < l; i++) {
      var subscription = topics[topic][i];
      subscription.callback.apply(subscription.context, args);
    }
    return this;
  };

  return {
    publish: publish,
    subscribe: subscribe,
    installTo: function (obj) {
      obj.subscribe = subscribe;
      obj.publish = publish;
    },
  };
})();
```

