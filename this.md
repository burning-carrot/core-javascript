# this

> Javascript에서 this는 무엇인가?

this가 무엇인지 대답하라고 할 때 보통 상황마다 다른 this를 대답하곤 한다.

"그래서 this가 뭔데요?"

**this는 현재 실행 문맥이다. 그런데 이놈은 상황마다 다르다.**

실행문맥이란 말은 호출자가 누구인지와 같다.

콘텍스트(context) 객체는 this가 바라보고 있는 어떤 객체이다.

그렇다면 역으로 this는 context를 가리키는 객체이다.

this가 변하는 상황을 먼저 나열해보자

- global scope
- Object's method (암시적 바인딩)
- call(), apply(), bind() (명시적 바인딩)
- arrow function
- new
- callback

상황에 따른 this를 정리해보자

---

## global scope

전역공간에서의 this는 전역객체를 가리킨다.

- 브라우저는 window
- Node.js는 global

전역변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당한다.

```javascript
var a = 1;

this.a; // 1
```

## Object's method

함수는 자체로 독립적인 기능을 수행

메서드는 자신을 호출한 대상 객체에 관한 동작 수행 (부모 객체라고 하자)

- 함수에서의 this : 전역객체
- 메서드에서의 this : 호출한 객체

```javascript
var func = function () {
  console.log(this);
};

(function () {
  func();
})(); // 즉시 실행함수로 실행해도, 다른 함수에서 a를 호출해도 a의 this는 Window, global이다.

var obj = {
  method: func,
};

obj.method(); // obj를 가리킨다.
```

아래의 경우를 살펴보자

```javascript
const testObj = {
  outerFunc: function () {
    console.log(this); // outerFunc

    function innerFunc() {
      console.log(this); // Window
    }
    innerFunc();
  },
};

testObj.outerFunc();
```

outerFunc 내부의 innerFunc에서 this는 전역 객체가 된다. 이는 callback에서 더 자세히 다룬다.

## call, apply, bind

함수에 명시적으로 this를 바인딩 할 수 있다.

call과 apply의 차이는 바인딩할 함수의 인자가 되며, 인자를 넣어주는 방법이 다르다.

```javascript
func.call(this, `param1`, `param2`); // argument
func.apply(this, [`param1`, `param2`]); // array
```

call과 apply의 return값은 undefined이다.

또한 call, apply는 바인딩 하는 순간 실행된다.

bind의 경우는 this 바인딩만 해주고 실행은 직접 해줘야 한다.

```javascript
func.bind(this, `param1`, `param2`);
```

## arrow function

화살표 함수는 함수를 선언할 때 this에 바인딩할 객체가 정적으로 결정되며, 화살표 함수를 둘러싸는 렉시컬 스코프(lexical scope)의 this가 사용된다.

또한 call, apply, bind를 사용해 명시적으로 this를 bind하더라도 무시한다.

```javascript
var obj = {
  outer: function () {
    console.log(this); // this1

    var innerFunc = () => {
      console.log(this); // this2
    };
    innerFunc();
  },
};

obj.outer(); // this1과 this2는 동일하다.
```

```javascript
var name = "outer";
const arrowObj = {
  name: "object",
  func: () => console.log(`${this.name} function`),
};

const funcObj = {
  name: "object",
  func: function () {
    console.log(`${this.name} function`);
  },
};

arrowObj.func(); // outer function
funcObj.func(); // object function
```

## new

생성자 함수에서는 인스턴스 자신이된다. (class와 비슷함)

```javascript
var Obj = function (name, age) {
  this.name = name;
  this.age = age;
};

var obj = new Obj("나비", 1); // {name:"나비", age:1}
```

## callback (서브루틴)

콜백함수의 제어권을 가지는 함수(메서드)마다 다르다

제어권을 가지는 함수가 콜백 함수에서 this를 무엇으로 할지 결정하기 때문이다.

일반적으로는 호출한 객체의 this를 bind하지 않으므로, bind 되지 않았기 때문에 실행문맥이 전역 객체가 된다.

```javascript
// setTimeout의 this는 Window, Global이다.
setTimeout(function () {
  console.log(this); // Window
}, 300);

// Array.prototype.forEach에서 this는 Window, Global이다.
[1, 2, 3, 4, 5].forEach(function (x) {
  console.log(this, x); // Window
});

// eventListener에서 this는 event.target이다.
// <button id="a">클릭</button>
document.body.querySelector("#a").addEventListener("click", function (e) {
  console.log(this); // button
});
```

앞서 object의 method에서 예시로 들었던 코드를 다시 살펴보자

```javascript
const testObj = {
  outerFunc: function () {
    console.log(this); // outerFunc

    function innerFunc() {
      console.log(this); // Window
    }
    innerFunc();
  },
};

testObj.outerFunc();
```

여기서 innerFunc는 callback과 마찬가지로 outerFunc 내부에서 innerFunc가 호출할때는 그 어떤 문맥도 지정하지 않았다.

따라서 서브루틴 내에서 바깥의 this 를 사용하려고 할때는 arrow function 을 이용해 원하는 this를 사용할 수 있다.

---

## 실전 문제들

[7 Interview Questions on "this" keyword in JavaScript. Can You Answer Them?](https://dmitripavlutin.com/javascript-this-interview-questions/)

### Variable vs property

```javascript
const object = {
  message: "Hello, World!",

  getMessage() {
    const message = "Hello, Earth!";
    return this.message;
  },
};

console.log(object.getMessage()); // What is logged?
```

getMessage는 object의 메소드이다. 이 경우 this는 호출한 객체를 나타내므로 `Hello, World!`가 출력된다.

### Cat name

```javascript
function Pet(name) {
  this.name = name;

  this.getName = () => this.name;
}

const cat = new Pet("Fluffy");

console.log(cat.getName()); // What is logged?

const { getName } = cat;
console.log(getName()); // What is logged?
```

첫번째의 경우 object의 메소드로 호출했다. 따라서 `Fluffy`가 출력된다.

두번째의 경우 object의 메소드를 함수로 추출해서 실행했다.

이 때 getName은 arrow function이므로 렉시컬 스코프의 this를 기억한다.

따라서 위와 같이 this는 Pet이다. 따라서 `Fluffy`가 출력된다.

만약 getName이 arrow function이 아닌 일반 함수일 경우에 getName을 추출해서 사용할 경우 this는 전역객체이다.

전역객체에서 name은 선언되지 않았으므로 undefined이다. 따라서 `undefined`가 출력된다.

### Delayed greeting

```javascript
const object = {
  message: "Hello, World!",

  logMessage() {
    console.log(this.message); // What is logged?
  },
};

setTimeout(object.logMessage, 1000);
```

object의 메소드에서 this는 자기 자신이다.

그러나 우리는 setTimeout의 callback으로 object의 메소드를 인자로 넣었다.

따라서 이 함수는 메소드가 아닌 일반 함수로 동작한다.

이 일반 함수는 arrow function이 아니기 때문에 렉시컬 스코프를 기억하지 못하고 따라서 this는 전역객체가 된다.

따라서 `undefined`가 출력된다.

만약 arrow function인 경우에는 어떻게될까?

이는 객체에서 프로퍼티에 arrow function 함수를 할당하는 형태가 된다.

```javascript
var message = "new world";

const object = {
  message: "Hello, World!",

  logMessage() {
    console.log(this.message); // What is logged?
  },

  logMessageArrow: () => {
    console.log(this.message); // What is logged?
  },
};

object.logMessage(); // 'Hello, World!'
object.logMessageArrow(); // 'new world'

setTimeout(object.logMessage, 1000); // 'new world'
setTimeout(object.logMessageArrow, 1000); // 'new world'
```

이 경우 arrow function는 자신의 this를 가지고 있지 않다. 따라서 이 this는 전역 객체가 된다.

따라서 위 경우는 다음과 같이 출력된다. 객체의 프로퍼티로 arrow function을 할당하는 경우에는 무조건 전역 객체가 this이기 때문이다. 이는 callback으로 사용될 때도 어차피 전역이므로 동일하다.

함수 단위 스코프를 이용하는 경우는 다음과 같다.

```javascript
var foo = "foo";

function hello() {
  const object = {
    message: "Hello, World!",
    foo: "bar",

    logMessage() {
      console.log("method", this.foo, this.message);
    },

    logMessageArrow: () => {
      console.log("arrow", this.foo, this.message);
    },
  };

  object.logMessage(); // 'method' 'bar' 'Hello, World!'
  object.logMessageArrow(); // 'arrow' 'foo' undefined

  setTimeout(object.logMessage, 1000); // 'method' 'foo' undefined
  setTimeout(object.logMessageArrow, 1000); // 'arrow' 'foo' undefined
}

hello();
```

class와 new 키워드를 사용하는 경우는 callback으로 메소드를 사용할 때 다음과 같다.

여기서 arrow function의 경우 렉시컬 스코프를 기억하기 때문이다.

```javascript
class Object {
  constructor(message) {
    this.message = message;
    this.logMessage = function () {
      console.log("method", this.message);
    };
    this.logMessageArrow = () => {
      console.log("arrow", this.message);
    };
  }
}

const object = new Object("Hello, World!");

setTimeout(object.logMessage, 1000); // 'method' undefined
setTimeout(object.logMessageArrow, 1000); // 'arrow' 'Hello, World!'
```

### Artificial method

```javascript
const object = {
  message: "Hello, World!",
};

function logMessage() {
  console.log(this.message); // "Hello, World!"
}

// Write your code here...
```

this를 강제로 바인딩 하는 문제이다.

bind, apply, call 등을 이용해 강제로 바인딩을 할 수 있다.

혹은 object의 메소드로 지정한 후 호출해 사용할 수 있다.

```javascript
object.logMessage = logMessage;
object.logMessage();

// Using func.call() method
logMessage.call(object);

// Using func.apply() method
logMessage.apply(object);

// Creating a bound function
const boundLogMessage = logMessage.bind(object);
boundLogMessage();
```

### Greeting and farewell

```javascript
const object = {
  who: "World",

  greet() {
    return `Hello, ${this.who}!`;
  },

  farewell: () => {
    return `Goodbye, ${this.who}!`;
  },
};

console.log(object.greet()); // What is logged?
console.log(object.farewell()); // What is logged?
```

이 경우에 arrow function의 this는 전역객체이다.

따라서 greet의 경우에는 `Hello, World!`가, farewell의 경우에는 `Goodbye, undefined!`가 출력된다.

### Tricky length

```javascript
var length = 4;
function callback() {
  console.log(this.length); // What is logged?
}

const object = {
  length: 5,
  method(callback) {
    callback();
  },
};

object.method(callback, 1, 2);
```

object.method에 인자로 넣은 callback을 실행했다.

callback이 인자로 실행되므로 여기서 this는 전역객체이다. 따라서 `4`가 출력된다.

### Calling arguments

```javascript
var length = 4;
function callback() {
  console.log(this.length); // What is logged?
}

const object = {
  length: 5,
  method() {
    arguments[0]();
  },
};

object.method(callback, 1, 2);
```

이 경우 method에서 argument는 특수한 변수이며 다음과 같다.

```javascript
{
  0: callback,
  1: 1,
  2: 2,
  length: 3
}
```

여기서 arguments[0]의 callback을 호출할 경우 이 callback은 arguments의 메소드처럼 동작한다.

따라서 이 경우 this는 arguments를 나타내므로 arguments의 길이인 `3`이 출력된다.

(object.method에 인자로 값을 3개 넣었으므로)

---

출처

- [[javascript] this는 어렵지 않습니다.](https://blueshw.github.io/2018/03/12/this/)
