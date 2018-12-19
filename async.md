> 주의! 얕은 정리...



#### 비동기 통신 

- 동적인 요청! 역사 이야기!
- XMLHttpRequest, ajax, axios, fetch



#### 비동기 함수

- callback, Promise, async await, setTimeout, interval



> 요약
>
> XMLHttpRequest, ajax, axios, fetch 는 비동기 통신을 하는 방법.
>
> callback, Promise, async await 은 비동기를 동기적으로 처리하기 위한 방법.

<br/>



# [XMLHttpRequest (XHR)](#https://developer.mozilla.org/ko/docs/XMLHttpRequest)

1999년 ie5 에서 소개되었으며 클라이언트 측 스크립트를 http 또는 https 로 서버에세 요청하고, 텍스트 형식으로 (이름과 다르게 다양한 데이터 다룸 xml, html, json 과 같은) 데이터를 받는 것을 가능하게 함.

 (MDN 발췌) **페이지 전체의 데이터를 새로 받아오지 않고**도 특정 URL로부터 데이터를 받아올 수 있습니다. 이를 이용해 웹페이지는 사용자를 방해하지 않고도 페이지의 **일부분만을 업데이트**할 수 있습니다. `XMLHttpRequest` 는 **Ajax 프로그래밍에 주로 사용**됩니다.

> XMLHttpRequset 자체는 통신 규약, 통신 인터페이스

```js
var req = new XMLHttpRequest();
req.open('GET', 'http://www.mozilla.org/', true);
req.onreadystatechange = function (aEvt) {
  if (req.readyState === 4) {
     if(req.status === 200)
      dump(req.responseText);
     else
      dump("Error loading page\n");
  }
};
req.send(null);
```



<br/>



# Ajax

2005년 2월 구글 Maps 가 소개 되면서 핫해졌는데, 구글 Map이 출시된 후 Jesse James Garrett은 구글 맵이 다른 대화형 웹사이트들과 어떤 특징들을 공유한다는 것을 알게 되었다.

그는 이러한 특성들을 비동기 자바스크립트와 XML의 약자(**Asynchronous Javascript And XML**)로 **Ajax**라 불렀다. 두둥..

> 비동기란? 특정 코드의 실행이 완료될 때까지 기다리지 않고 다음 코드를 먼저 수행하는 자바스크립트의 특성



#### Ajax의 두 가지 초석

- 첫째, 컨텐츠 로딩을 백그라운드에서 비동기적으로 진행한다. (XMLHttpRequest)
- 둘째, 그 결과물을 가지고 현재 페이지에서 동적으로 업데이트를 한다.(dynamic HTML) - 부분로딩

따라서, 매번 전체 페이지를 다시 로드하는 것보다 많은 사용성 개선이 되었다.

Ajax가 나온 이후, 이전과 다른 데이터 형식이 인기를 얻게 되었고(XML 대신 JSON),

다른 프로토콜들이 사용되게 되었고(HTTP뿐만 아닌 웹 소캣..),

양방향 통신 또한 가능하게 되었다.



### Ajax의 장점

Ajax를 이용하면 다음과 같은 장점이 있습니다.

1. 웹 페이지 전체를 다시 로딩하지 않고도, 웹 페이지의 일부분만을 갱신할 수 있습니다.
2. 웹 페이지가 로드된 후에 서버로 데이터 요청을 보낼 수 있습니다.
3. 웹 페이지가 로드된 후에 서버로부터 데이터를 받을 수 있습니다.
4. 백그라운드 영역에서 서버로 데이터를 보낼 수 있습니다.



### Ajax의 한계

Ajax를 이용하면 여러 장점을 가지지만, Ajax로도 다음과 같은 일들은 처리할 수 없습니다.

1. Ajax는 클라이언트가 서버에 데이터를 요청하는 클라이언트 풀링 방식을 사용하므로, 서버 푸시 방식의 실시간 서비스는 만들 수 없습니다.
2. Ajax로는 바이너리 데이터를 보내거나 받을 수 없습니다.
3. Ajax 스크립트가 포함된 서버가 아닌 다른 서버로 Ajax 요청을 보낼 수는 없습니다.
4. 클라이언트의 PC로 Ajax 요청을 보낼 수는 없습니다.

> 클라이언트 풀링(client pooling) 방식이란 사용자가 직접 원하는 정보를 서버에게 요청하여 얻는 방식을 의미합니다.
> 이에 반해 서버 푸시(server push) 방식이란 사용자가 요청하지 않아도 서버가 알아서 자동으로 특정 정보를 제공하는 것을 의미합니다.



## Ajax 구현해보기

```js
function getXMLHttpRequest() {
    if (window.ActivXObject) {
        try {
            return new ActiveXObject("Msxml2.XMLHTTP");
        } catch(e) {
            try {
                return ActiveXObject("Microsoft.XMLHTTP");
            } catch(e1) {
                return null;
            }
        }
    } else if (window.XMLHttpRequest) {
        return new XMLHttpRequest();
    } else {
        return null;
    }
}
  
function sendRequest(url, params, callback, method) {
    var httpRequest = getXMLHttpRequest();
    var httpMethod = method ? method : 'GET';
 
    if (httpMethod != 'GET' && httpMethod != 'POST') {
        httpMethod = 'GET';
    }
 
    var httpParams = (params == null || params == '') ? null : params;
    var httpUrl = url;
 
    if (httpMethod == 'GET' && httpParams != null) {
        httpUrl = httpUrl + "?" + httpParams;
    }
 
    httpRequest.open(httpMethod, httpUrl, true);
    httpRequest.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
    httpRequest.onreadystatechange = callback;
    httpRequest.send(httpMethod == 'POST' ? httpParams : null);
}
```



## jQuery 사용하기

> jQuery 는 Ajax 프레임워크 중 하나 Ajax 에 대한 많은 API 를 제공

```js
function getData(){
    var tableData;
    $.get('https://domain.com/products/1', function(response){
        tableData = response;
    })
    return tableData;
}
console.lof(getData());
// undefined
```



## callBack 패턴 사용

```js
function getData(callbackFunc){
    $.get('url 주소/products/1', function(response){
        callbackFunc(response);
    })
}

getData(function(tableData){
    console.log(tableData);
})
```



## But, Callback Hell ...

```js
$.get('url', function (response) {
    // 1) 받아온 데이터 인코딩
	parseValue(response, function (id) {
        // 2) 사용자 인증
		auth(id, function (result) {
            // 3) 화면에 데이터 표시
			display(result, function (text) {
				console.log(text);
			});
		});
	});
});
```

> 가독성이 떨어지고 로직 변경이 어려워짐... 콜백 지옥 ㅠ^ㅠ
>
> Promise 와 같은 직렬화(Serialize) 방식을 사용하면 된다..!



<br/>



# Promise API

**“A promise is an object that may produce a single value some time in the future”**

- 자바스크립트 비동기 처리에 사용되는 객체 (Promise 는 callback 함수를 통한, 병렬코드들을 직렬화 시켜준닷
- Promise 패턴은 ES6 스펙에 정식으로 포함되었고,
- Chrome 브라우저에서도 32 버전부터 본격적으로 native promise 가 지원되기 시작했다.



## Promise를 사용하는 이유

아래는 일반적으로 서버에 Ajax 비동기 요청을 보내는 상황이고, 여기서 데이터를 받아오기도 전에 데이터를 사용하려는 상황에 오류 또는 빈 화면이 발생하게 됨.

```js
$.get('url 주소/products/1', function(response){
    // ...
})
```

Promise를 사용하면 서버에서 데이터를 완벽히 받아온 상태에서 다음 작업을 수행 가능함.



#### ajax 코드

```js
function getData(callbackFunc){
    $.get('url 주소/products/1', function(response){
        callbackFunc(response);
    })
}

getData(function(tableData){
    console.log(tableData);
})
```



#### promise 코드

```js
function getData(callback){
    return new Promise(function(resolve, reject){
        $.get('url 주소/products/1', function(response){
            resolve(response);
        })
    })
}

// Promise 를 사용하면 then 사용 가능.
getData().then(function(tableData){
    console.log(tableData);
})
```

> jQuery 에서도 Promise 를 사용할 수 있는 $.Deferred 를 제공.



## Promise states

- Pending(대기) :  비동기 처리 로직이 아직 완료되지 않은 상태
- Fulfilled(이행) : 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태 (= 완료)
- Rejected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태



### Pending (대기)

```js
new Promise();	// Pending 상태
```

```js
new Promise(function(resolve, reject){
    // ...
});
```



### Fulfilled (완료)

```js
new Promise(function(resolve, reject){
    resolve();	// Fulfilled 상태
});
```

> 완료 상태가 되면 프로미스를 호출한 부분에서 then 을 이용해 결과 값을 받을 수 있음.

```js
function getData(callBackFunc){
    return new Promise(function(resolve, reject){
        resolve(100);
    });
}

getData().then(function(resolveData){
    console.log(resolveData);	// 100
})
```



### Rejected (실패)

```js
new Promise(function(resolce, reject){
	reject();
})
```

> 실패 상태가 되면 실패한 이유(실패 처리의 결과 값)를 catch()로 받을 수 있습니다.

```js
function getData(){
    return new Promise(function(resolve, reject){
        reject(new Error("Request is failed."));
    });
}

getData().then().catch(function(err){
    console.log(err);	// Error: Request is failed.
})
```



### Promise Sample

```js
function getData() {
  return new Promise(function (resolve, reject) {
    $.get('url 주소/products/1', function (response) {
      if (response) {
        resolve(response);
      }
      reject(new Error("Request is failed"));
    });
  });
}

// Fulfilled 또는 Rejected의 결과 값 출력
getData().then(function (data) {
  console.log(data); // response 값 출력
}).catch(function (err) {
  console.error(err); // Error 출력
});
```



## Promise Chaining

여러개의 프로미스를 연결하여 사용할 수 있는데, then() 메서드를 호출하고 나면 새로운 프로미스 객체가 반환된다. 

```js
getData(userInfo)
  .then(parseValue)
  .then(auth)
  .then(diaplay);

var userInfo = {
  id: 'test@abc.com',
  pw: '****'
};

function parseValue() {
  return new Promise({
    // ...
  });
}

function auth() {
  return new Promise({
    // ...
  });
}

function display() {
  return new Promise({
    // ...
  });
}
```

> 프로미스는 계속 연결할 수 있으며, 현재로는 프로미스를 중단할 수 있는 방법이 존재하지 않음.



## Promise Error Handling

앞의 예제는 코드가 항상 정상적으로 동작한다고 가정한 예제이므로 에러 핸들링이 필요하다.

에러 처리에 2가지 방법이 존재하는데, 모두 프로미스의 reject() 메서드가 호출되어 실패 상태가 된 경우에 실행된다. 즉, 프로미스의 로직이 정상적으로 돌아가지 않는 경우 호출되는 것!



### then() 의 두번째 인자로 에러를 처리하는 방법

```js
getData().then(handleSuccess, handleError);
```



### catch() 를 이용하는 방법

```js
getData().then().catch();
```



### example

```js
function getData() {
  return new Promise(function (resolve, reject) {
    reject('failed');
  });
}

// 1. then()으로 에러를 처리하는 코드
getData().then(function () {
  // ...
}, function (err) {
  console.log(err);
});

// 2. catch()로 에러를 처리하는 코드
getData().then().catch(function (err) {
  console.log(err);
});
```



### 가급적 Catch 를 사용해라!

```js
function getData(){
    return new Promise(function (resolve, reject) {
		resolve('hi');
	});
}

getData().then(function(result){
    console.log(result);
    throw.new Error("Error in then()");	// Uncaugth Error....
}, function(err){
    console.log('then error: ', err);
})
```

> then 의 두번째 인자로 에러를 처리하는 경우 then() 의 첫 번째 콜백 함수 내부에서 오류가 발생하는 경우를 제대로 잡아내지 못하기 때문에 위 코드를 실행하면 오류가 발생한다. (에러를 잡지 못했습니다... 하지만 catch 로 사용하면? 

```js
function getData(){
    return new Promise(function (resolve, reject) {
		resolve('hi');
	});
}

getData().then(function(result){
    console.log(result);
    throw.new Error("Error in then()");
}).catch(function(err){
    console.log('then error: ', err);
})
```

> catch 를 사용하면 이처럼 더 많은 예외 처리가능.



### Promise.all

프로미스 all 을 사용하면 모든 Promise 가 완료된 후 다음 then으로 이동.

```js
Promise.all([promise1, promise2]).then(function(values){
    console.log('모두 완료됨', values);
})
```

> Promise 패턴을 사용하면 비동기 작업들을 순차적으로 진행하거나, 병렬로 진행하는 등의 컨트럴이 보다 수월해지고 코드의 가독성이 좋아진다. 또 내부적으로 예외처리에 대한 구조가 탄탄하기 때문에 오류가 발생했을 때 오류 처리 등에 대해 보다 가시적으로 관리해줄 수 있는 장점이 있다.
>
> Promise 를 polyfill 해보자!
>
> 핵심 아이디어는 then에 전달된 함수를 큐에 담아주고 덜어내는 방식.



<br/>



# [Fetch](#http://hacks.mozilla.or.kr/2015/05/this-api-is-so-fetching/)

Fetch는 Promise 객체를 반환한다. 그렇기 때문에 Promise 객체와 같이 `then` 을 이용해 결과에 접근할 수 있다.

XHR 의 이벤트 기반 모델(event based model) 은 요즘의 Promise 기반 (그리고 generator 기반) 비동기 프로그래밍 방식과 그다지 잘 어울리지 않습니다. 

> 의문) 왜 잘어울리지 않는지..? 

이런 문제들을 해결하기 위해 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 가 정의되었습니다. Fetch API 는 HTTP 프로토콜의 모든 개념을 JS 에 똑같이 도입함으로써 문제를 해결합니다. Fetch API 는 유틸리티 함수 `fetch()` 를 도입합니다. `fetch()` 함수는 네트워크로부터 리소스를 가져오는 동작을 간결하게 표현합니다.

```js
fetch('주소', 설정객체).then(콜백).catch(콜백);
```

> XMLHttpRequest와 Fetch는 ECMAScript가 아니다?
>
> 브라우저에서만 쓰이는 API이기 때문에 babel에서도 지원해주지 않기 때문에 크로스 브라우징을 위해선 [window.fetch polyfill](https://github.com/github/fetch)을 쓰자.



#### XMLHttpRequest 객체

```js
const jsonURL = "https://blog.perfectacle.com/mock/test.json";
const getDataAjax = url => {
  const xhr = new XMLHttpRequest();
  xhr.open("get", url, true);
  xhr.responseType = "json";
  xhr.onreadystatechange = () => {
    if(xhr.readyState === 4) { // 4 means request is done.
      if(xhr.status === 200) { // 200 means status is successful
        for(let key in xhr.response) { // 받아온 json 데이터의 키와 값의 쌍을 모두 출력.
          if(xhr.response.hasOwnProperty(key))
            console.log(`${key}: ${xhr.response[key]}`);
        }
      } else { // 통신 상에 오류가 있었다면 오류 코드를 출력.
        console.error(`http status code: ${xhr.status}`);
      }
    }
  };
  xhr.send();
};
getDataAjax(jsonURL);
```



#### Fetch 사용

```js
const jsonURL = "https://blog.perfectacle.com/mock/test.json";
const getDataAjaxFetch = url => (
  fetch(url).then(res => res.json())
);
getDataAjaxFetch(jsonURL).then(data => {
  for(let key in data) { // 받아온 json 데이터의 키와 값의 쌍을 모두 출력.
    if(data.hasOwnProperty(key))
      console.log(`${key}: ${data[key]}`);
  }
}).catch(err => console.error(err));
```



### Fetch 의 응답

위의 코드에서 Fetch 의 응답인 `res` 는 문자열이 아니다. `res.json()` 로 사용하면 json 형태로 바꿀 수 있고, 바꾼 결과를 return 하면 그것 또한 Promise !!

Promise 를 직접 사용할 때보다 then을 한 번 더 타야한다는 단점이 존재하지만 XMLHttpRequest 보다는 편리하다.



<br/>



# Axios

- 많은 API 요청이 있는 SPA에선 페이지 전환 시 요청을 취소해주자
  - 모든 라이브러리가 AJAX 요청 취소를 지원하지는 않는다
  - fetch API 는 요청을 취소할 수 있는 기능을 제공하지 않는다
  - API 요청의 취소는 간단하다. [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 에서 [abort](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/abort) 메서드를 제공하고 있기 때문에 이를 호출하는 것으로 취소시킬 수가 있다. 혹은 라이브러리에서 제공하는 메서드를 사용하면 될 것이다.
- [axios](https://github.com/mzabriskie/axios) 는 Cancelable Promise 형태로 이를 [구현해두었다](https://github.com/mzabriskie/axios#cancellation).

#### GET

```js
// GET
axios.get('/api/todos')
  .then(res => {
    prettyPrint(res.data)
  })
```

#### POST

```js
// POST
axios.post('/api/todos', {title: "ajax 공부"})
  .then(res => {
    prettyPrint(res.data)
  })
```

#### PATCH

```js
// PATCH
axios.patch('/api/todos/3', {title: "axios 공부"})
  .then(res => {
    prettyPrint(res.data)
  })
```

#### DELETE

```js
// DELETE
axios.delete('/api/todos/3')
  .then(res => {
    prettyPrint(res.data)
  })
```



#### 인자 사용

```js
// config 객체
axios.get('/api/todos', {
  params: { // query string
    title: 'react 공부'
  },
  headers: { // 요청 헤더
    'X-Api-Key': 'my-api-key'
  },
  timeout: 1000 // 1초 이내에 응답이 오지 않으면 에러로 간주
}).then(res => {
    prettyPrint(res.data)
  })
```

> axios 요청 메소드의 두 번째 인자로 config 객체를 넘길 수 있습니다. config 객체를 통해 요청의 쿼리 스트링, 요청 헤더, 쿠키 포함 여부 등 많은 것들을 설정할 수 있습니다.



### example

```js
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/user/12345', {
  cancelToken: source.token
}).catch(function (thrown) {
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
    // handle error
  }
});

axios.post('/user/12345', {
  name: 'new name'
}, {
  cancelToken: source.token
})

// cancel the request (the message parameter is optional)
source.cancel('Operation canceled by the user.');
```



<br/>



# Async Await (ES2017)

Promise를 사용하는 비동기 프로그래밍 방식은 이전의 방식과 비교하면 여러 가지 장점을 갖지만, **여전히 콜백을 사용한다**는 점 때문에 '불편하다', '가독성이 좋지 않다'는 비판을 받아왔습니다.

```js
// 비동기 함수
async function func1() {
  // ...
}

// 비동기 화살표 함수
const func2 = async () => {
  // ...
}

// 비동기 메소드
class MyClass {
  async myMethod() {
    // ...
  }
}
```

비동기 함수 내에서 **await 키워드**를 쓸 수 있다는 것입니다. `await`는 Promise의 `then` 메소드와 유사한 기능을 하는데, **await 키워드 뒤에 오는 Promise가 결과값을 가질 때까지 비동기 함수의 실행을 중단시킵니다.** 여기서의 '중단'은 비동기식이며, 브라우저는 Promise가 완료될 때까지 다른 작업을 처리할 수 있습니다.

`await`는 연산자이기도 하며, **await 연산의 결과값은 뒤에 오는 Promise 객체의 결과값**이 됩니다.

비동기 함수의 가장 큰 장점은 **동기식 코드를 짜듯이 비동기식 코드를 짤 수 있다**는 것입니다. 아래 예제는 Github 데이터를 불러오는 예제를 비동기 함수를 사용해 다시 작성한 것입니다.

await` 키워드는 `for`, `if`와 같은 제어 구문 안에서도 쓰일 수 있기 때문에, `then` 메소드를 사용할 때보다 **복잡한 비동기 데이터 흐름을 아주 쉽게 표현할 수 있다**는 장점이 있습니다. 다만, 비동기 함수 역시 Promise를 사용하기 때문에, 비동기 함수를 잘 쓰기 위해서는 여전히 Promise에 대해 잘 알고 있어야 합니다.

- async / await는 비동기 방식을 동기 코드와 유사하고 읽기 쉽게 만들어 준다. 이게 가장 큰 장점이다.
- ES7 에서 지원하고 Node 7 LTS  이상버전에서  asnyc / await 문법을 지원한다.



```js
function getProcessedData(url) {
  return downloadData(url) // returns a promise
    .catch(e => {
      return downloadFallbackData(url) // returns a promise
    })
    .then(v => {
      return processDataInWorker(v); // returns a promise
    });
}
```

```js
async function getProcessedData(url) {
  let v;
  try {
    v = await downloadData(url); 
  } catch (e) {
    v = await downloadFallbackData(url);
  }
  return processDataInWorker(v);
}
```

> return 구문에 await 구문이 없다는 것에 주목하자. 이는 async function의 반환값이 암묵적으로 [`Promise.resolve`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve)로 감싸지기 때문이다.



promise와 async await 문법을 비교해보면 어떤게 가독성이 좋은지 알 수 있다.  **async 함수는 Promise가 없으면 의미가 없으며, await를 사용하려면 함수가 async로 선언되어야 한다**. async함수는 화살표함수 () = { } 로도 가능하고, 함수표현식으로도 가능하다. 앞에 async만 붙여주면된다. **주의해야 할점은 await는 asysnc 함수 바로 안에서만 쓰여야한다. async 함수안에 일반 다른 함수에서사용하게 되면 에러는 나지 않지만 제대로 동작하지 않게 된다**. 



## 왜 async / await 가 promise 보다 더 좋은?가?

### 1. 간결함과 깔끔함

위에서 줄인 코드량을 보면 알 수 있다.  then을 사용할 필요도 없고 코드의 가독성도 굉장히 뛰어나다.



### 2. 에러 핸들링

aysync / await는 동기와 비동기 에러 모두를 try / catch로 처리 한다.  promise를 사용한다면 then의 2번째 파라미터에 에러함수를 추가하거나 .catch를 호출해야 되고, 여러 프로미스가 있다면 에러를 처리하는 코드는 중복될거고 에러를 띄우는 console.log는 굉장히 복잡해 질거다.

callback hell을 해결하기 위해 promise가 나왔고 promise를 해결하기 위해 async / await가 나온거시다. 

하지만 promise1.json의 데이터를 받아와야 promise2.json을 실행하고 데이터를 받아오면 promise3.json을 실행하는 조건이 아니라면 async / await를 쓰지 말자. 

예를 들어 promise1.json의  ajax 요청이 10초 promise2.json  20초  promise3.json 30초가 걸린다면 async / await를 쓴다면 저 코드는 무조건 60초가 걸린다. 

만약 저런 조건이 아닌 따로따로 호출해도 상관없는 데이터라면 비동기로 구현해야 더 빠르기 때문이다. **상황에 따라 적절하게 잘 사용하도록 하자.** 
