# Closure

Closure는 함수와 함수가 선언된 어휘적 환경의 조합이다.

이것이 뭔말인지 살펴보자.

Closure는 반환된 내부함수가 자신이 선언되었을 때의 환경인 lexical scope를 기억해서 함수외부에서 호출되었을 때 생성된다.

(lexical scope : lexical environment scope를 나타낸다. 함수를 선언했을 때 생성되는 스코프이다.)

함수를 처음 선언하는 순간, 함수 내부의 변수는 자기 스코프로부터 가장 가까운 곳(상위 범위에서)에 있는 변수를 계속 참조한다.

```javascript
function foo() {
  var color = "blue";

  function bar() {
    console.log(color);
  }
  return bar; // bar가 선언된 환경 바깥으로 반환되었다!
}

var baz = foo();

baz();
```

baz를 할당(초기화)할 때 bar의 outer lexical environment를 foo로 결정한다.

쉽게 말해서 bar는 자신이 생성될 때의 lexical scope를 기억하고 있다. (여기서는 foo)

foo는 함수를 리턴하고, 리턴하는 함수(bar)가 Closure를 형성했다. Closure를 형성했기 때문에 baz를 실행할 경우 bar에서 color를 찾을 때 lexical scope chain을 이용해 찾는다.

Closure는 함수와 함수가 선언된 어휘적 환경의 조합이다. 이 환경은 Closure가 생성된 시점의 유효 범위 내에 있는 모든 지역 변수로 구성된다.

## 사용

Closure는 어떤 데이터(어휘적 환경)와 그 데이터를 조작하는 함수를 연관시켜준다.

(이는 함수가 선언된 lexical scope에서 데이터들을 찾을 수 있고, 이것들이 함수와 엮여있기 때문이다.)

유사한 동작을 하는 함수들 여러개를 만들되, 각 함수들마다 인자의 값을 약간 다르게 생성한다고 하자.

직접 함수를 작성할 수 있겠지만 Closure를 이용할 경우 로직을 재사용할 수 있다.

```javascript
function makeAdder(add) {
  return function (number) {
    return number + add;
  };
}

const add12 = makeAdder(12);
const add14 = makeAdder(14);
const add16 = makeAdder(16);

const a = add12(14); // 26
const b = add14(14); // 28
const c = add16(14); // 30
```

## private 데이터 만들기

Javascript에서 Closure를 이용해 private한 데이터들을 만들 수 있다.

이는 Closure에서 함수의 lexical scope에서 사용하는 데이터들을 외부에서 직접 참조하지 못하기 때문이다.

```javascript
const counter = (function () {
  let _count = 0;

  function changeBy(number) {
    _count += val;
  }
  return {
    increment: function () {
      changeBy(1);
    },
    decrement: function () {
      changeBy(-1);
    },
    value: function () {
      return _count;
    },
  };
})();

counter.increment();
counter.decrement();
counter.value();
// 직접 _count에 접근할 수 없다.
```
