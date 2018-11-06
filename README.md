# Chapter01 함수형 자바스크립트 소개

<br/>

## 함수형 프로그래밍

성공적인 프로그래밍을 위해 부수효과(Side Effect)를 최대한 멀리하고, 조합성을 강조하는 프로그래밍 패러다임.

- 부수효과를 줄인다. == 순수함수를 만든다.
- 함수 조합성을 높인다. == 모듈화 수준을 높인다.


<br/>


## 함수형 자바스크립트의 예 (값으로써의 함수와 클로저)

```js
// 함수는 값을 리턴할 수 있고 함수는 값이 될 수 있다.
function addMaker(a) {
    // 클로저.
    return function(b) {
        return a + b;
    }   
}
```

```js
addMaker(10)(5);	// 15
```

```js
var add5 = addMaker(5);
add5(3);	// 8
add5(4);	// 9
```

```js
var add3 = addMaker(3);
add3(3);	// 6
add3(4);	// 7
```


<br/>



## 함수형 자바스크립트의 실용성1

#### 리팩토링 전

```js
var users = [
  { id: 1, name: "ID", age: 32 },
  { id: 2, name: "HA", age: 25 },
  { id: 3, name: "BJ", age: 32 },
  { id: 4, name: "PJ", age: 28 },
  { id: 5, name: "JE", age: 27 },
  { id: 6, name: "JM", age: 32 },
  { id: 7, name: "HI", age: 24 }
];

var temp_users = [];
for (var i = 0, len = users.length; i < len; i++) {
  if (users[i].age < 30) temp_users.push(users[i]);
}
console.log(temp_users.length);	// 4

var ages = [];
for (var i = 0, len = temp_users.length; i < len; i++) {
  ages.push(temp_users[i].age);
}
console.log(ages);	// [ 25, 28, 27, 24 ]

var temp_users = [];
for (var i = 0, len = users.length; i < len; i++) {
  if (users[i].age >= 30) temp_users.push(users[i]);
}
console.log(temp_users.length);	// 3

var names = [];
for (var i = 0, len = temp_users.length; i < len; i++) {
  names.push(temp_users[i].name);
}
console.log(names);	// [ 'ID', 'BJ', 'JM' ]
```



#### 리팩토링 후

```js
var users = [
  { id: 1, name: "ID", age: 32 },
  { id: 2, name: "HA", age: 25 },
  { id: 3, name: "BJ", age: 32 },
  { id: 4, name: "PJ", age: 28 },
  { id: 5, name: "JE", age: 27 },
  { id: 6, name: "JM", age: 32 },
  { id: 7, name: "HI", age: 24 }
];

// 1. filter 함수 생성
const filter = (list, predicate) => {
  let new_list = [];
  for (let i = 0, len = list.length; i < len; i++) {
    if (predicate(list[i])) new_list.push(list[i]);
  }
  return new_list;
};

// 2. map 함수 생성
const map = (list, iteratee) => {
  let new_list = [];
  for (let i = 0, len = list.length; i < len; i++) {
    new_list.push(iteratee(list[i]));
  }
  return new_list;
};

// 3. bvalue 함수 생성
const bvalue = key => obj => obj[key];

// 4. 출력 함수 생성.
function log_length_value(value) {
  console.log(value.length);
  console.log(value);
}

// 5. 함수 중첩.
log_length_value(map(filter(users, user => user.age < 30), bvalue("age")));
log_length_value(map(filter(users, user => user.age >= 30), bvalue("name")));
```



#### 1. filter 함수 생성

for 을 filter 로 if 문을 predicate 로...

```js
/* 기존 코드.
var temp_users = [];
for (var i = 0, len = users.length; i < len; i++) {
  if (users[i].age < 30) temp_users.push(users[i]);
}
console.log(temp_users.length);	// 4
*/

// 바꾼 코드.
const filter = (list, predicate) => {
  let new_list = [];
  for (let i = 0, len = list.length; i < len; i++) {
    if (predicate(list[i])) new_list.push(list[i]);
  }
  return new_list;
};

let users_under_30 = filter(users, function(user){
    return user.age < 30
});
```

- 재사용성이 높아짐.
- filter 함수는 내부에서 관리하고 있는 상태를 따로 두지 않고 넘겨진 **인자(list, predicate)에만 의존함**. 
- filter 함수는 동일한 인자가 들어오면 **항상 동일한 값을 리턴**하도록 함. 
- filter 함수는 **매개변수로 전달받은 값(list)을 변경하지 않고 새로운 값을 만듦** => 함수형 프로그래밍의 컨셉중 하나.
- predicate는 값을 변경하지 않고, filter의 if에 **값을 전달하는 역할만 수행**.



#### 2. map 함수 생성

for 을 filter 로 if 문을 predicate 로...

```js
/* 기존 코드.
var ages = [];
for (var i = 0, len = temp_users.length; i < len; i++) {
  ages.push(temp_users[i].age);
}
console.log(ages);	// [ 25, 28, 27, 24 ]
*/

// 바꾼 코드.
const map = (list, iteratee) => {
  let new_list = [];
  for (let i = 0, len = list.length; i < len; i++) {
    new_list.push(iteratee(list[i]));
  }
  return new_list;
};

let ages = map(users_under_30, function(user){
    return user.age;
});	
console.log(ages);	// [ 25, 28, 27, 24 ]
```



#### 3. bvalue 함수 생성

bvalue 함수에서 iteratee 생성.

```js
const bvalue = key => obj => obj[key];
```



#### 4. 출력함수 생성

```js
function log_length_value(value) {
  console.log(value.length);
  console.log(value);
}
```



#### 5. 함수 중첩

```js
log_length_value(map(filter(users, user => user.age < 30), bvalue("age")));
log_length_value(map(filter(users, user => user.age >= 30), bvalue("name")));
```



#### 결론

- 함수형 프로그래밍에서는 **'항상 동일하게 동작는 함수'를 만들고 보조 함수를 조합**하는 식으로 로직을 완성함. 
- 보조 함수 역시 인자이며, 보조 함수에서도 상태를 변경하지 않으면 보조 함수를 받은 함수는 항상 동일한 결과를 만드는 함수가 된다. => **부수효과를 줄일 수 있음**.
- 들어온 데이터의 특성은 보조 함수가 대응해 주기 때문에 함수가 데이터의 특성에서 완전히 분리될 수 있다. => **다형성을 높이며 안전성도 높일 수 있음**.
- 객체지향에서 자신의 상태를 메서드를 통해 변경하는 것은 단점이 아니라 객체지향의 방법론 그 자체.
- 반면에 함수형 프로그래밍은 부수효과를 최소화 하는 것이 목표에 가까움. 지향점의 차이.


<br/>


## 함수형 자바스크립트의 실용성2

특정 조건의 객체를 찾는 로직에 filter 함수를 사용하게 되면, 기존의 filter 함수는 무조건 list.length 만큼 predicate가 실행되기 때문에 효율적이지 못하고, 동일 조건에 값이 두 개 이상이라면 두 개 이상을 찾음.

#### 리팩토링 후

```js
let users = [
  { id: 1, name: "ID", age: 32 },
  { id: 2, name: "HA", age: 25 },
  { id: 3, name: "BJ", age: 32 },
  { id: 4, name: "PJ", age: 28 },
  { id: 5, name: "JE", age: 27 },
  { id: 6, name: "JM", age: 32 },
  { id: 7, name: "HI", age: 24 }
];

// 2. 
function object(key, val) {
  let obj = {};
  obj[key] = val;
  return obj;
}

function match(obj, obj2) {
  for (const key in obj) {
    if (obj[key] !== obj2[key]) {
      return false;
    }
  }
  return true;
}

function bmatch(obj2, val) {
  if (arguments.length == 2) obj2 = object(obj2, val);
  return function(obj) {
    return match(obj, obj2);
  };
}

const findIndex = (list, predicate) => {
  for (let i = 0, len = list.length; i < len; i++) {
    if (predicate(list[i])) return i;
  }
  return -1;
};

console.log(findIndex(users, bmatch({ id: 6, name: "JM", age: 32 })));
```



#### 1. filter => find 함수 확장.

인자로 키와 값 대신 함수를 사용해 확장하기. 

```js
/* filter 함수 
const filter = (list, predicate) => {
  let new_list = [];
  for (let i = 0, len = list.length; i < len; i++) {
    if (predicate(list[i])) new_list.push(list[i]);
  }
  return new_list;
};
*/

// filter 함수를 대체할 새로운 find 함수
const find = (list, predicate) => {
  for (let i = 0, len = list.length; i < len; i++) {
    if (predicate(list[i])) return list[i];
  }
};

// 나이가 25인 사람 찾기.
find(users, function(user) { return user.getAge() === 25;});

// 이름에 'P'가 들어간 사람 찾기.
find(users, function(user) { return user.name.indexOf('P') !== -1;});

// 나이가 32이고, 이름이 'JM'인 사람 찾기.
find(users, function(user) { return user.age == 32 && user.name === 'JM';});

// 나이가 30 미만인 사람 찾기.
find(users, function(user) { return user.getAge() < 30;});
```

- find 함수는 list 내부에 무엇이 들어 있는지에 대해서 관심이 없음.
- 객체지향 프로그래밍이 약속된 이름의 메서드를 대신 실행해주는 식으로 외부 객체에게 위임을 한다면, 함수형 프로그래밍은 보조 함수를 통해 완전히 위임하는 방식을 취함.



#### 2. predicate 를 생성하는 함수 생성.

```js
function bmatch1(key, val){
    return function(obj){
        return obj[key] === val;
    }
}
```

- bmatch1 함수는 하나의 key, value 비교만 가능.
- 클로저를 반환하고, 클로저를 실행하는 방식으로 동작.



#### 3. bmath1 함수의 확장.

여러개의 key에 해당하는 value들을 비교할 수 있는 함수로 bmatch1 함수를 수정하여 여러개의 key, value 를 비교 가능한 함수로 확장.

```js
function object(key, val) {
  let obj = {};
  obj[key] = val;
  return obj;
}

function match(obj, obj2) {
  for (const key in obj) {
    if (obj[key] !== obj2[key]) {
      return false;
    }
  }
  return true;
}

// 확장된 bmatch1 함수.
function bmatch(obj2, val) {
  if (arguments.length == 2) obj2 = object(obj2, val);
  return function(obj) {
    return match(obj, obj2);
  };
}
```



#### 4. find 함수를 findIndex 함수로 변경.

findIndex 함수는 값 비교만 하는 Array.prototype.indexOf 보다 활용도가 높은 findIndex를 만들 수 있음.

```js
/*
const find = (list, predicate) => {
  for (let i = 0, len = list.length; i < len; i++) {
    if (predicate(list[i])) return list[i];
  }
};
*/

const findIndex = (list, predicate) => {
  for (let i = 0, len = list.length; i < len; i++) {
    if (predicate(list[i])) return i;
  }
  return -1;
};
```



#### 5. 함수 조합.

```js
console.log(findIndex(users, bmatch({ id: 6, name: "JM", age: 32 })));
```


<br/>


### function identity(v) {return v;}

```js
_.filter = function(list, predicate) {
  let newArray = [];
  for (let i = 0, len = list.length; i < len; i++) {
    if (predicate(list[i], i, list)) newArray.push(list[i]);
  }
  return newArray;
};

// 받은 인자를 그대로 뱉는 함수.
_.identity = function(v) {
    return v;
}

let 10;
console.log(_.identity(a));	// 10

// _.filter 함수와 조합하여, Truthy Values만 남도록 사용.
_.filter([true, 0, 10, 'a', false, null], _.identity);
```


<br/>
<br/>


## 함수형 자바스크립트를 위한 기초

### 일급함수

- 자바스크립트에서 함수는 일급 객체이자 일급 함수.
- 자바스크립트에서 객체는 일급 객체.
- 자바스크립트에서 모든 값은 일급.
- '일급' == '값을 다룰 수 있다.'


#### 조건

1. 변수에 담을 수 있다.
2. 함수나 메서드의 인자로 넘길 수 있다.
3. 함수나 메서드에서 리턴할 수 있다.
4. 아무 때나(런타임에서도) 선언이 가능하다.
5. 익명으로 선언할 수 있다.
6. 익명으로 선언한 함수도 함수나 메서드의 인자로 넘길 수 있다.

```js
// 1
function f1(){}
let a = typeof f1 == 'function' ? f1 : function() {};

// 3
function f2(){
    return function() {};
}

// 4, 5
(function(a, b)){ return a + b; })(10, 5);

// 2, 6
function callAndAdd(a, b){
    return a() + b();
}
callAndAdd(function(){ return 10; }, function(){ return 5; });
```


<br/>


### 클로저

- 클로저는 자신이 생성될 때의 환경을 기억하는 함수.

  (= 클로저는 자신이 생성될 때의 스코프에서 알 수 있었던 변수를 기억하는 함수다.)

  (= 클로저는 자신이 생성될 때의 스코프에서 알 수 있었던 변수 중 언젠가 **자신이 실행될 때 사용할 변수들만 기억**하여 **유지**시키는 함수다.)

- 클로저라는 용어에 담긴 속성이나 특징들을 모두 가지고 있는 특별 함수만을 클로저라 칭한다. (주관적)

- 브라우저엔진별로 클로저에 대한 동작이 다르다.



#### 조건

1. 클로저로 만들 함수(=myfn) 내부에서 선언되지 않은 변수(=a)가 있어야한다.
2. 그 변수(=a)는 클로저를 생성하는 스코프에서 선언되었거나 알 수 있어야 한다.

```js
function parent(){
    var a = 5;
    function myfn(){
        console.log(a);
    }
}

function parent2(){
    var a = 5;
    function parent1(){
        function myfn(){
            console.log(a);
        }
    }
}
```



#### 클로저 관련 예제

```js
var a = 10;
var b = 20;

// 글로벌 스코프에서 선언된 변수 a, b 가 f1 함수의 존재 유무와 관계없이 유지되기 때문에 클로저가 아님. 
// But, Node.js 에서의 js 파일인 경우 클로저.
function fl(){	
    return a + b;
}
f1();
```

```js
function f2(){
    var a = 10;
    var b = 20;
    
	// 함수가 생성될 때의 스코프가 알고 있는 변수 a, b 를 사용하지 않기 때문에 클로저가 아님.
	function f3(c, d){
        return c + d;
    }
    return f3;
}
var f4 = f2();
f4(5, 7);
```

```js
function f4(){
    var a = 10;
    var b = 20;
    // f4 함수가 실행되는 사이에 클로저가 생겼다가, f5를 실행하면서 사라짐.
    function f5(){
        return a + b;
    }
    return f5();
}
f4();
```

```js
function f6(){
    var a = 10;
    // 클로저.
    // f6 의 실행이 끝나도 f7 이 생성되는 시점의 a 값을 기억함.
    function f7(b){
        return a + b;
    }
    return f7;
}
// f6 의 실행 결과를 f8 에 담지 않는다면 클로저가 아님.
var f8 = f6();
f8(20);
f8(10);
```



#### 메모리 누수

개발자가 '의도하지 않았는데' 메모리가 해제되지 않고 계속 남는 것을 의미하며, 메모리 누수가 지속적으로 반복될 때는 치명적인 문제가 발생한다. 계속해서 모르는 사이에 새어 나가야 누수라고 볼 수 있다.

위의 함수 f8 은 아무리 실행되더라도 이미 할당된 a가 그대로 유지되기 때문에 메모리 누수는 발생하지 않음.



#### 클로저의 조금 긴 '때'

- '때가 조금 길다.'
- '스코프에서 알 수 있었던.'

```js
function f9(){
    var a = 10;
    var f10 = function(c){
        return a + b + c;
    }
    var b = 20;	
    return f10;
}
var f11 = f9();
f11(30);	// 60
```

> b 변수가 호이스팅 되면서 'undefined' 이기때문에 오류가 발생하지는 않고, f9 함수가 실행 완료된다. f10 함수는  f9 함수의 실행컨텍스트 안에 들어가기 때문에 '20'이 할당된 b 변수를 사용할 수 있게 된다.


<br/>


### 클로저의 실용 사례

- 이전 상황을 나중에 일어날 상황과 이어 나갈 때.
  - 이벤트 리스너로 함수를 넘기기 이전에 알 수 있던 상황들을 변수에 담아 클로저로 기억.
  - 콜백 패턴에서 콜백으로 함수를 넘기기 이전 상황을 클로저로 만들어 기억.
- 함수로 함수를 만들거나 부분 적용을 할 때.

```html
<div></div>

<script>
    var users = [
        { id: 1, name: "HA", age: 25 },
        { id: 2, name: "PJ", age: 26 },
        { id: 3, name: "JE", age: 27 },
    ];

    $('.user-list').append(
        _.map(users, function (user) {	// 클로저가 아님.
            var button = $('<button>').text(user.name);
            button.click(function () {	// 계속 유지되는 클로저 (내부에서 user 사용).
                if (confirm(user.name + "님을 팔로잉 하시겠습니까?")) follow(user);
            })
            return button;
        })
    );

    function follow(user) {
									        // 클로저가 되었다가 없어지는 클로저.
        $.post('/fllow', { user_id: user.id }, function () {	
            alert('이제 ' + user.name + "님의 소식을 볼 수 있습니다.");
        })
    }
</script>
```

> map 과 같은 함수는 부수효과로 부터 자유롭고 편하게 프로그래밍 할 수 있도록 도와준다.


<br/>



### 고차함수

- 고차함수에는 위에서 만든 map, filter, find, findIndex, bvalue, bmatch 같은 함수들이 포함된다.
- Underscore.js 라는 라이브러리에서 위와같은 고차함수들을 제공.

#### 조건

1. 함수를 인자로 받아 대신 실행하는 함수.

   ```js
   function callWith10(val, func){
       return func(10, val);
   }
   function add(a, b){
       return a + b;
   }
   function sub(a, b){
       return a - b;
   }
   callWith10(20, add);
   callWith10(5, sub);
   ```

2. 함수를 리턴하는 함수.

   ```js
   function constant(val){
       return function(){
           return val;
       }
   }
   
   var always10 = constant(10);
   ```

3. 함수를 인자로 받아서 또 다른 함수를 리턴하는 함수.

   ```js
   function callWith(val1){
       return function(val2, func){
           return func(val1, val2);
       }
   }
   
   var callWith10 = callWith(10);
   callWith10(20, add);	// 30
   
   var callWith5 = callWith(5);
   callWith5(5, sub);		// 0
   ```

<br/>


### 함수의 부분적용...
....
