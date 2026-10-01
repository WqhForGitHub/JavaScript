# 1. 基本用法

`fetch()` 的功能与 XMLHttpRequest 基本相同，都是向服务器发出 HTTP 请求，但有三个主要的差异。
（1）`fetch()` 使用 Promise，不使用回调函数，因此大大简化了写法，写起来更简洁。
（2）`fetch()` 采用模块化设计，API 分散在多个对象上（Response 对象、Request 对象、Headers 对象），更合理一些。相比之下，XMLHttpRequest 的 API 设计并不是很好，输入、输出、状态都在同一个接口管理，容易写出非常混乱的代码。
（3）`fetch()` 通过数据流（Stream 对象）处理数据，可以分块读取，有利于提高网站性能表现，减少内存占用，对于请求大文件或者网速慢的场景相当有用。XMLHttpRequest 对象不支持数据流，所有的数据全部放在缓存里，不支持分块读取，必须等待全部获取后，再一次性读取。

用法上，`fetch()` 接受一个 URL 字符串作为参数，默认向该网址发出 GET 请求，返回一个 Promise 对象。它的基本用法如下。
```javascript
fetch(url)
	.then(...)
	.then(...)
```
下面是一个例子，从服务器获取 JSON 数据。
```javascript
fetch('https://api.github.com/users/ruanyf')
	.then(response => response.json())
	.then(json => console.log(json))
	.catch(err => console.log('Request Failed', err));
```
上面示例中，`fetch()` 接收到的 `response` 是一个 Stream 对象，里面的数据本例是 JSON 数据，所以使用 `response.json()` 方法，将其转为 JSON 对象。它是一个异步操作，返回一个 Promise 对象。

Promise 可以使用 await 语法改写，使得语义更清晰。
```javascript
async function getJSON() {
	let url = 'https://api.github.com/users/ruanyf';
	try {
		let response = await fetch(url);
		return await response.json();
	} catch (error) {
		console.log('Request Failed', error);
	}
}
```
上面示例中，`await` 语句必须放在 `try...catch` 里面，这样才能捕捉异步操作中可能发生的错误。

后文都采用 `await` 的写法，不再使用 `.then()` 的写法。
# 2. Response 对象：处理 HTTP 回应
## 2.1 Response 对象的同步属性

`fetch()` 请求成功以后，得到的是一个 Response 对象。它对应服务器的 HTTP 回应。
```javascript
const response = await fetch(url);
```
前面说过，Response 包含的数据通过 Stream 接口异步读取，但是它还包含一些同步属性，对应 HTTP 回应的标头信息（Headers），可以立即读取。
```javascript
async function fetchText() {
	let response = await fetch('/readme.txt');
	console.log(response.status);
	console.log(response.statusText);
}
```
上面示例中，`response.status` 和 `response.statusText` 就是 Response 的同步属性，可以立即读取。

标头信息属性有下面这些。

**Response.ok**

`Response.ok` 属性返回一个布尔值，表示请求是否成功，`true` 对应 HTTP 请求的状态码 200 到 299，`false` 对应其他的状态码。

**Response.status**

`Response.status` 属性返回一个数字，表示 HTTP 回应的状态码（例如 200，表示成功请求）。

**Response.statusText**

`Response.statusText` 属性返回一个字符串，表示 HTTP 回应的状态信息（例如请求成功以后，服务器返回 “OK”）。

**Response.url**

`Response.url` 属性返回请求的 URL。如果 URL 存在跳转，该属性返回的是最终 URL。

**Response.type**

`Response.type` 属性返回请求的类型。可能的值如下：
- `basic`：普通请求，即同源请求
- `cors`：跨源请求
- `error`：网络错误，主要用于 Service Worker
- `opaque`：如果 `fetch()` 请求的 `type` 属性设为 `no-cors`，就会返回这个值，详见请求部分。表示发出的是简单的跨源请求，类似 `<form>` 表单的那种跨源请求。
- `opaqueredirect`：如果 `fetch()` 请求的 `redirect` 属性设为 `manual`，就会返回这个值，详见请求部分。

**Response.redirected**

`Response.redirected` 属性返回一个布尔值，表示请求是否发生过跳转。
## 2.2 判断请求是否成功

`fetch()` 发出请求以后，有一个很重要的注意点：只有网络错误，或者无法连接时，`fetch()` 才会报错，其他情况都不会报错，而是认为请求成功。

这就是说，即使服务器返回的状态码是 4xx 或 5xx，`fetch()` 也不会报错（即 Promise 不会变为 `rejected` 状态）。

只有通过 `Response.status` 属性，得到 HTTP 回应的真实状态码，才能判断请求是否成功。请看下面的例子。
```javascript
async function fetchText() {
	let response = await fetch('/readme.txt');
	if (response.status >= 200 && response.status < 300) {
		return await response.text();
	} else {
		throw new Error(response.statusText);
	}
```
上面示例中，`response.status` 属性只有等于 2xx（200~299），才能认定请求成功。这里不用考虑网址跳转（状态码为 3xx），因为 `fetch()` 会将跳转的状态码自动转为 200。

另一种方法是判断 `response.ok` 是否为 `true`。
```javascript
if (response.ok) {
	// 请求成功
} else {
	// 请求失败
}
```

















