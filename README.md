# 함수형 자바스크립트를 위한 문법 다시 보기

- [객체와 대괄호 다시보기](#객체와-대괄호-다시보기)
  - [변칙적인 난해한 코드를 짜는 이유](#변칙적인-난해한-코드를-짜는-이유)
  - [객체와 key](#객체와-key)
  - [함수나 배열에 달기](#함수나-배열에-달기)
- [함수 정의 다시 보기](#함수-정의-다시-보기)
  - [호이스팅](#호이스팅)
  - [호이스팅 활용](#호이스팅-활용)
  - [익명함수 즉시 실행](#익명함수-즉시-실행)
  - [new Function 과 eval](#new-Function-과-eval)
  - [유명(named) 함수](#유명(named)-함수)

<br/>

## 객체와 대괄호 다시보기

### 변칙적인 난해한 코드를 짜는 이유

1. 더 짧은 코드를 위해
2. 추상화의 다양한 기법
3. if를 없애기 위해
4. 특별한 로직을 위해
5. 캐시를 위해
6. 은닉을 위해
7. 함수를 선언하고 참조하기 위해
8. 컨텍스트를 이어주기 위해

> 다양한 라이브러리 혹은 프레임워크의 코드를 읽고 테스트 케이스를 살펴보는 것이 도움이 될것.



### 객체와 key

```js
var obj = { a: 1, "b": 2 };	// 1번 "b"
obj.c = 3;
obj['d'] = 4;	// 2번 대괄호 표기법

var e = 'e';
obj[e] = 5;

function f() {
    return 'f';
}

obj[f()] = 6;
console.log(obj);
// { a: 1, b: 2, c: 3, d: 4, e: 5, f: 6 } 
```

- 객체의 key와 value는 **{}**, **.**, **[]** 등을 통해 설정할 수 있음.
- 객체의 key에 직접 선언하는 1번 방식과, 대괄호 표기법을 사용하는 2번 방식을 사용하면 어떤 문자열이든 key로 사용 가능.



#### 띄어쓰기가 들어간 key 만들기

```js
// 띄어쓰기를 써도 key로 만들 수 있음.
var obj2 = { " a a a ": 1 };
obj2[' b b b '] = 2;
console.log(obj2);
// { " a a a ": 1, " b b b ": 2 }
```



#### 특수 문자가 들어간 key 만들기

```js
// 특수 문자를 써도 key로 만들 수 있음.
var obj3 = { "margin-top": 5 };
obj3["padding-bottom"] = 20;
console.log(log3);
// { margin-top: 5, padding-bottom: 20 }
```



#### 숫자가 들어간 key 만들기

```js
// 숫자를 써도 key로 만들 수 있음.
var obj4 = { 1: 10 };
obj4[2] = 20;
console.log(log4);
// { 1: 10, 2: 20}
```



#### {} 과 [] 선언의 차이

- 코드 실행 가능 유무

```js
var obj5 = { (true ? "a" : "b"): 1 }; 
// SyntaxError
```

```js
var obj6 = {};
obj6[true ? "a" : "b"] = 1;
console.log(obj6);
// { a: 1 }
```

```js
var obj7 = { [true ? "a" : "b"]: 1 };
```
> 위의 코드는 ES6 이후 부터 적용 가능...


<br/>


### 함수나 배열에 달기

- 자바스크립트는 함수도 객체이므로 key/value 쌍으로 구성 가능.
- 자바스크립트는 사용자 정의 객체든 기본객체(Object, Array, Function 등…)든 key 의 참조, 수정에 제약이 없고 유연함.



#### 배열에 숫자가 아닌 key 사용

```js
var obj10 = [];
obj10.a = 1;
console.log(obj10);
// [a: 1]
console.log(obj10.length);
// 0
```



#### 배열에 숫자로 key 사용

- push 와 동일하게 작용.
- 자동으로 length 증가.

```js
var obj11 = [];
obj11.[0] = 1;
obj11.[1] = 2;
console.log(obj11);
// [1, 2]
console.log(obj11.length);
// 2
```



#### 한번에 배열 length 올리기

```js
var obj12 = [];
obj12.length = 5;
console.log(obj12);
// Array[5]

var obj13 = [1, 2];
// 5번째 인덱스에 숫자 5 추가.
obj13[5] = 5;
console.log(obj13);
// [1, 2, 5: 5]
console.log(obj13.length);
// 6

// 다음 인덱스에 숫자 6 추가.
obj13.push(6);
console.log(obj13);
// [1, 2, 5: 5, 6: 6]
console.log(obj13.length);
// 7
```

- 일반적으로는 length를 한번에 올리거나 Array(length), arr[i] = 1 등을 권장하지 않음.
- 의도적으로 동시성(비동기)을 만든 경우에는 순서를 보장하지 않으므로 좋은 해법일 수도 있음. ex) bluebird.js 의 all, map 등이 있음.



#### 배열 사용법에 따른 성능 차이

- arr.push(1) 보다 arr[i] = 1 이 성능이 좋음.
- IE 에서는 5배 이상 나기도 함.

```js
var l = 10000;
var list = [];
for(var i = 0; i < l; i++){ list.push(i); }

var l = 10000;
var list = [];
for(var i = 0; i < l; i++){ list[list.length] = i; }

var l = 10000;
var list = [];
list.length = l;
for(var i = 0; i < l; i++){ list[i] = i; }

var l = 10000;
var list = Array(l);
for(var i = 0; i < l; i++){ list[i] = i; }
```

> 2016-2017 크롬 기준 아래의 예제로 사용할수록 빨라짐.



#### undefined 으로 채워진 배열 만들기

```js
Array.apply(null, {length: 3});
// [undefined, undefined, undefined]
```



#### delete

- 자바스크립트에서는 기본 객체의 메서드나 프로퍼티도 지울 수 있음.

```js
var obj = { a: 1, b: 2, c: 3 };
delete obj.a;
delete obj['b'];
delete obj['C'.toLowerCase()];
console.log(obj);
// {};

delete Array.prototype.push;
var arr1 = [1, 2, 3];
arr1.push(4);
// Uncaugth TypeError: arr1.push is not a function...
```


<br/>


## 함수 정의 다시 보기

#### 함수 선언식

```js
function add1(a, b){	
    return a + b;
}
```

#### 함수 표현식

```js
var add2 = function(a, b){
    return a + b;
}
```

#### 메서드

```js
var m = {
    add3: function(a, b){
        return a + b;
    }
}
```

<br/>


### 호이스팅

- 변수나 함수가 어디서 선언되든지 해당 스코프 최상단에 위치하게 되어 동일 스코프 어디서든 참조할 수 있는 것.

```js
add1(10, 5);
// 15

add2(10, 5);
// Uncaugth TypeError: add2 is not a function...

// 함수 선언식.
// 함수 선언 자체가 호이스팅 됨
function add1(a, b){	
    return a + b;
}

// 함수 표현식.
// add2 변수는 호이스팅 되고, 값(function) 할당은 현재위치에서 이루어짐.
// add2 를 호출하는 시점에서 add2는 undefined - 주소 공간은 할당되었지만 공간에 할당된 값이 없음.
var add2 = function(a, b){
    return a + b;
}
```



#### 선언한적 없는 함수 실행

- 함수 표현식을 선언 이전에 사용한 것과는 다른 에러 발생.

```js
hi();
// Uncaugth ReferenceError: hi is not defined
```



#### 선언한적 없는 변수 실행

- 선언한적 없는 함수 실행과 동일한 에러 발생.

```js
var a = hi;
// Uncaugth ReferenceError: hi is not defined
```

<br/>


### 호이스팅 활용

#### 코드 가독성 높이기

```js
function add(a, b){
    return valid() ? a + b : new Error();
    
    function valid(){
        return Number.isInteger(a) && Number.isInteger(b);
    }
}

console.log(add(10, 5));
// 15

console.log(add(10, "a"));
// Error(...)
```

<br/>


### 익명함수 즉시 실행

- 익명함수 이용시 괄호로 함수를 감싸주지 않으면 선언 실패로 에러 발생.

```js
(function(a){
    console.log(a);
    // 100
})(100);
```

- 익명함수는 선언만 해도 에러 발생.

```js
function(){}
// Uncaugth SyntaxError: Unexcepted token (
```

- 익명함수를 값으로 사용하는 경우 에러 발생하지 않음.

```js
function f1(){
    return function(){}
}
f1();
```

- 익명함수를 값으로 사용하는 경우 괄호 없이 즉시실행도 가능.

```js
function f1(){
    return function(a){
        console.log(a);
        // 1
    }(1);
}
f1();
```



#### 괄호 없이 정의 가능한 다양한 상황

- 연산자와 함께 정의.
- 함수를 값으로 다룸.

```js
!function(a) {
    console.log(a);
    // 1
}(1);

true && function(a) {
    console.log(a);
    // 1
}(1);

1 ? function(a) {
    console.log(a);
    // 1
}(1) : 5;

0, function(a) {
    console.log(a);
    // 1
}(1);

var b = function(a) {
    console.log(a);
    // 1
}(1);

function f2() {}
f2(function(a) {
    console.log(a);
    // 1
}(1));

var f3 = function c(a){
    console.log(a);
    // 1
}(1);

new function(){
    console.log(1);
    // 1
}
```



#### 즉시실행으로 객체 생성

```js
var pj = new function(){
    this.name = 'PJ';
    this.age = 28;
    this.constructor.prototype.hi = function(){
        console.log('hi');
    }
}
console.log(pj);
// { name: "PJ", age: 28 }
pj.hi();
// hi
```



#### 즉시 실행하며 this 바인딩

```js
var a = function(a){
    console.log(this, a);
}.call([1], 1);
```

<br/>


### new Function 과 eval

- 문자열을 자바스크립트 코드로 변환하기 때문에 일반 코드에 비해 느리지만 성능저하의 직접적 원인이 되지 않도록 사용 가능함.
- 자바스크립트로 HTML 템플릿 엔진을 생성하는 경우 new Function이 꼭 필요하기도 함.

```js
var a = eval('10 + 5');
console.log(a);
// 15

var add = new Function('a, b', 'return a + b;');
add(10, 5);
// 15
```



#### 성능에 영향주지 않는...

```js
function L(str){
    var splitted = str.split('=>');
    return new Function(splitted[0], 'return (' + splitted[1] +');');
}

console.time('1');
var arr = Array(10000);
_.map(arr, function(v, i){	// 1번
    return i * 2;
})
console.timeEnd('1');

console.time('2');
var arr = Array(10000);
_.map(arr, L('v, i => i * 2'))	// 2번
console.timeEnd('2');
```

- **2번**은 이미 만들어진 Function 인스턴스를 iteratee로 _.map에게 넘긴다. 
- new Function 을 한번만 수행하기 때문에 속도에 큰 차이가 없다.



#### 성능에 영향을 주는...

```js
function L(str){
    var splitted = str.split('=>');
    return new Function(splitted[0], 'return (' + splitted[1] +');');
}

console.time('3');
var arr = Array(10000);
_.map(arr, function(v, i){	
    return function(v, i){	// 1번
        return i * 2;
    }(v, i);
})
console.timeEnd('3');

console.time('4');
var arr = Array(10000);
_.map(arr, function(v, i){	
    return L('v, i => i * 2')(v, i);	// 2번
})	// 2번
console.timeEnd('4');
```

- **2번**에서 루프를 돌 때마다 new Function 을 실행하게 된다.



#### 그래서, 메모이제이션(memoization) 기법

원래의,

```js
function L(str){
    var splitted = str.split('=>');
    return new Function(splitted[0], 'return (' + splitted[1] + ')');
}
```

메모이제이션 기법을 사용한,

```js
function L2(str){
    if(L2[str]) return L2[str];
    var splitted = str.split('=>');
    return L2[str] = new Function(splitted[0], 'return (' + splitted[1] + ')');
    // 함수를 만든 후 L2[str]에 캐시하면서 리턴.
}
```

<br/>


### 유명(named) 함수

```js
var fl = function f(){
    console.log(f);
};
```



#### 익명함수에서 자신을 참조...

```js
var f1 = function(){
    console.log(f1);
}

f1();
// function(){
//	console.log(f1);
//}

// 위험 상황
var f2 = f1;
f1 = 'hi~~';

f2();
// hi~~
```

```js
var f1 = function(){
    console.log(arguments.callee);	// 1번
}

f1();
// function(){
//	console.log(arguments.callee);
//}

var f2 = f1;
f1 = null;

f2();
// function(){
//	console.log(arguments.callee);
//}
```

- **1번**은 ES5의 strict mode 에서 사용 불가 -> 유명함수 사용시 대체 가능.



#### 유명함수에서 자신을 참조...

- 함수가 값으로 사용되는 상황에서 자신을 참조하기 매우 편함.
- 함수의 이름이 바뀌든 메서드 안에서 생성한 함수를 다시 참조하고 싶은 상황이든 어떤 경우에도 정확히 참조함.

```js
var f1 = function f(){
    console.log(f);
}

f1();
// function f(){
//	console.log(f);
// }

var f2 = f1;
f1 = null;

f2();
// function f(){
//	console.log(f);
// }
```



#### 아주 안전하고 편안한 자기참조

- 함수 이름은 내부 스코프에서만 참조가 가능하고, 외부에서는 그 이름을 참조할 수 없고 없애지도 못해서 매우 안전.

```js
var h1 = 1;
var hello = function hi() {
  console.log(h1);
};

hello();
// function hi() {
//   console.log(h1);
// };

console.log(h1);
// 1

console.log(++h1);
// 2

hello();
// function hi() {
//   console.log(h1);
// };

console.log(hello.name == "h1");
// true

var z1 = function z() {
  console.log(z, 1);
};

var z2 = function z() {
  console.log(z, 2);
};

z1();
// function z() {
//   console.log(z, 1);
// };

z2();
// function z() {
//   console.log(z, 2);
// };

console.log(z1.name == z2.name);
// true

z;
// Uncaught ReferenceError: z is not defined (z는 외부 스코프에서 참조 불가.)
```



#### '즉시 실행 + 유명 함수' 를 이용한 재귀

```js
function flatten(arr) {
  return (function f(arr, new_arr) {
    arr.forEach(function(v) {
      Array.isArray(v) ? f(v, new_arr) : new_arr.push(v);
    });
    return new_arr;
  })(arr, []);
}

console.log(flatten([1, [2], [3, 4]]));
// [1, 2, 3, 4]

console.log(flatten([1, [2], [[3], 4]]));
// [1, 2, 3, 4]

console.log(flatten([1, [[2], [[3], [[4], 5]]]]));
// [1, 2, 3, 4, 5]
```



#### '즉시 실행 + 유명 함수' 가 아닌 재귀

```js
function flatten2(arr, new_arr) {
  arr.forEach(function(v) {
    Array.isArray(v) ? flatten2(v, new_arr) : new_arr.push(v);
  });
  return new_arr;
}

console.log(flatten2([1, [2], [3, 4]], []);	// 항상 빈 Array를 추가로 넘겨야 하는 복잡도 증가.

function flatten3(arr, new_arr) {
  if (!new_arr) return flatten3(arr, []);	// if 문 생김.
  arr.forEach(function(v) {
    Array.isArray(v) ? flatten3(v, new_arr) : new_arr.push(v);
  });
  return new_arr;
}

console.log(flatten3([1, [2], [3, 4]]);
```

- flatten 은 if 가 없으면서 사용하기 간단하지만 함수를 한 번 생성함.
- flatten2 는 if 가 없고 가장 빠르지만 함수를 사용할 때 직접 배열을 넘거줘야함.
- flatten3 은 사용하기 간단하지만 if 가 있음.



#### 재귀의 아쉬움...

- 자바스크립트에서 환경에 따라 다르지만 대략 15,000번 이상 재귀가 일어나면 'Maximum call stack size exceeded'라는 에러가 발생하고 소프트웨어가 죽지만 사실상 거의 발생하지 않음.
- ~~자바스크립트는 꼬리 재귀 최적화가 되지 않았지만~~(**ES6에서는 명시**) 성능때문에 재귀를 사용할 일이 없는것은 잘못된 정보.
- 자바스크립트에서는 비동기 프로그래밍이 많이 쓰이고 비동기가 일어나면 스택이 초기화 되기 때문에 애초에 비동기 상황이었다면 스택이 초기화 될 것이기 때문에 재귀 사용을 피할 이유가 없음!
