# The Observer Pattern

Observer là một design pattern trong đó một Object (subject) duy trì một danh sách các object phụ thuộc nó (observer), nó sẽ tự động thông báo tới các observer khi có bất kỳ thay đổi nào về trạng thái.

Khi một subject cần thông báo tới các observer về điều gì đó, nó sẽ **broadcast** các thông tin tới các observers.

![Mô tả observer pattern](https://learning.oreilly.com/library/view/learning-javascript-design/9781449334840/httpatomoreillycomsourceoreillyimages1547801.png)

Khi một observer không còn cần các thông tin của Subject nó có thể **detach** khỏi Subject của nó.

Các component trong Observer pattern

_Subject_: quản lý danh sách các observers, có thể thêm hoặc remove observer

_Observer_: cung cấp 1 interface cho object mà sẽ cần khi được thông báo từ Subject khi trạng thái thay đổi

_ConcreteSubject_: Broadcast thông tin trạng thái thay đổi tới các Observer, lưu trữ trạng thái của ConcreteObserver.

_ConcreteObserver_: lưu trữ một tham chiếu tới ConcreteSubject, thực hiện một interface cập nhật cho Observer để đảm bảo rằng trạng thái được đồng nhất với Subject

## So sánh với Publish/Subcribe Pattern

Trong Observer Pattern, Observer muốn nhấn thông tin về sự thay đổi phải subcribe tới subject

Trong publish/subcribe pattern lại khác sử dụng một topic/event channel, nó sẽ nằm giữa object cần thông tin (Subscribler) và object bắn thông tin (sự kiện) (publisher). Với cách thiết kế này giảm bớt sự phụ thuộc giữa subscriber và publisher.

![So sánh Observer pattern vs Publish/Subscribe Pattern](https://learning.oreilly.com/library/view/learning-javascript-design/9781449334840/httpatomoreillycomsourceoreillyimages1547803.png)

## Ưu điểm

Observer và Publish/Subscribe pattern khuyến khích chúng ta suy nghĩ về mối quan hệ giữa các thành phần trong ứng dụng. Chia ứng dụng thành các phần nhỏ hơn, nhiều block kết nối lỏng lẻo với nhau để tăng khả năng quản lý code và tái sử dụng.

Việc tăng tính lỏng lẻo giữa các phần là một đặc điểm tốt cho việc quản lý và tái sử dụng code. Ví dụ khi một object cần thông báo tới các object khác mà sẽ không cần phải giả định liên quan đến đối tượng đó (nếu sử dụng observer hoặc publish/subscribe pattern)

Đây là một công cụ tốt cho thiết kế hệ thống tách rời và nên được cân nhắc như một công cụ quan trọng trong.

## Nhược điểm

Trong Publish/Subscribe pattern, bởi các publisher phân tách với subscribe, nó có thể thỉnh thoảng trở lên khó đạt sự đảm bảo rằng các phần cụ thể của ứng dụng hoạt động như mong đợi.

Các subscribe không biết về sự tồn tại của nhau và mù mờ về chi phí cho việc chuyển đổi giữa publisher.

## Cách thức thực hiện Publish/Subscribe

Bản chất là mô hình publish/subscribe phù hợp với Javascript ecosystem, vì ECMAScript được thực hiện theo hướng event driven.

```js
var pubsub = {};

(function (q) {
  var topics = {},
    subUid = -1;

  // Publish or broadcast events of interest
  // with a specific topic name and arguments
  // such as the data to pass along
  q.publish = function (topic, args) {
    if (!topics[topic]) {
      return false;
    }

    var subscribers = topics[topic],
      len = subscribers ? subscribers.length : 0;

    while (len--) {
      subscribers[len].func(topic, args);
    }

    return this;
  };

  // Subscribe to events of interest
  // with a specific topic name and a
  // callback function, to be executed
  // when the topic/event is observed
  q.subscribe = function (topic, func) {
    if (!topics[topic]) {
      topics[topic] = [];
    }

    var token = (++subUid).toString();
    topics[topic].push({
      token: token,
      func: func,
    });
    return token;
  };

  // Unsubscribe from a specific
  // topic, based on a tokenized reference
  // to the subscription
  q.unsubscribe = function (token) {
    for (var m in topics) {
      if (topics[m]) {
        for (var i = 0, j = topics[m].length; i < j; i++) {
          if (topics[m][i].token === token) {
            topics[m].splice(i, 1);
            return token;
          }
        }
      }
    }
    return this;
  };
})(pubsub);
```

Còn đây là cách sử dụng:

```js
// Another simple message handler

// A simple message logger that logs any topics and data received through our
// subscriber
var messageLogger = function (topics, data) {
  console.log('Logging: ' + topics + ': ' + data);
};

// Subscribers listen for topics they have subscribed to and
// invoke a callback function (e.g messageLogger) once a new
// notification is broadcast on that topic
var subscription = pubsub.subscribe('inbox/newMessage', messageLogger);

// Publishers are in charge of publishing topics or notifications of
// interest to the application. e.g:

pubsub.publish('inbox/newMessage', 'hello world!');

// or
pubsub.publish('inbox/newMessage', ['test', 'a', 'b', 'c']);

// or
pubsub.publish('inbox/newMessage', {
  sender: 'hello@google.com',
  body: 'Hey again!',
});

// We can also unsubscribe if we no longer wish for our subscribers
// to be notified
// pubsub.unsubscribe( subscription );

// Once unsubscribed, this for example won't result in our
// messageLogger being executed as the subscriber is
// no longer listening
pubsub.publish('inbox/newMessage', 'Hello! are you still there?');
```

