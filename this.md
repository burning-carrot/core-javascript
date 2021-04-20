# this

> Javascript에서 this는 무엇인가?

this가 무엇인지 대답하라고 할 때 보통 상황마다 다른 this를 대답하곤 한다.

"그래서 this가 뭔데요?"

**this는 그것이 속한 특정 context 객체를 가리킨다. 그런데 이놈은 상황마다 다르다.**

콘텍스트(context) 객체는 this가 바라보고 있는 어떤 객체이다.

그렇다면 역으로 this는 context를 가리키는 객체이다.

this가 변하는 상황을 먼저 나열해보자

- global scope
- Object's method (암시적 바인딩)
- call(), apply(), bind() (명시적 바인딩)
- arrow function
- new
- callback

이제부터 정리해보자

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

화살표 함수는 자신의 this가 없다.

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

## callback

콜백함수의 제어권을 가지는 함수(메서드)마다 다르다

제어권을 가지는 함수가 콜백 함수에서 this를 무엇으로 할지 결정하기 때문이다.

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
