ajax() 方法用于执行 AJAX（异步 HTTP）请求，所有的 jQuery AJAX 方法都使用 ajax() 方法，该方法通常用于其他方法不能完成的请求。

基本的语法是：
$.ajax({name:value, name:value, … })

下面是通常使用的示例：
```javascript
$.ajax({
	type:'POST', // 规定请求的类型（GET 或 POST）
	url:uploadV, // 请求的url地址
	dataType:'json', //预期的服务器响应的数据类型 
	data:{},//规定要发送到服务器的数据
	beforeSend:function(){ //发送请求前运行的函数（发送之前就会进入这个函数）
		// ....
	},
	success: function(result){ // 当请求成功时运行的函数
		//...
	},
	error:function(result){ //失败的函数
		//...
	},
	complete:function(){ //请求完成时运行的函数（在请求成功或失败之后均调用，即在 success 和 error 函数之后，不管成功还是失败 都会进这个函数）
		// ...
	}
});
```

|名称						|值/描述																				|
|async						|布尔值，表示请求是否异步处理。默认是 true。											|
|beforeSend(xhr)			|发送请求前运行的函数。																	|
|cache						|布尔值，表示浏览器是否缓存被请求页面。默认是 true。									|
|complete(xhr,status)		|请求完成时运行的函数（在请求成功或失败之后均调用，即在 success 和 error 函数之后）。	|
|contentType				|发送数据到服务器时所使用的内容类型。默认是：”application/x-www-form-urlencoded”。	|
|context					|为所有 AJAX 相关的回调函数规定 “this” 值。											|
|data						|规定要发送到服务器的数据。																|
|dataFilter(data,type)		|用于处理 XMLHttpRequest 原始响应数据的函数。											|
|dataType					|预期的服务器响应的数据类型。															|
|error(xhr,status,error)	|如果请求失败要运行的函数。																|
|global						|布尔值，规定是否为请求触发全局 AJAX 事件处理程序。默认是 true。						|
|ifModified					|布尔值，规定是否仅在最后一次请求以来响应发生改变时才请求成功。默认是 false。			|
|jsonp						|在一个 jsonp 中重写回调函数的字符串。													|
|jsonpCallback				|在一个 jsonp 中规定回调函数的名称。													|
|password					|规定在 HTTP 访问认证请求中使用的密码。													|
|processData				|布尔值，规定通过请求发送的数据是否转换为查询字符串。默认是 true。						|
|scriptCharset				|规定请求的字符集。																		|
|success(result,status,xhr)	|当请求成功时运行的函数。																|
|timeout					|设置本地的请求超时时间（以毫秒计）。													|
|traditional				|布尔值，规定是否使用参数序列化的传统样式。												|
|type						|规定请求的类型（GET 或 POST）。														|
|url						|规定发送请求的 URL。默认是当前页面。													|
|username					|规定在 HTTP 访问认证请求中使用的用户名。												|
|xhr						|用于创建 XMLHttpRequest 对象的函数。													|

```javascript
	var jsonstr = {"id":issueId,"userId":userId};
	var r=confirm("确认删除该回答？");
	if(r){
	   $.ajax({
		url:"http://localhost:8885/answer/"+answerId,
		type:"DELETE",
		contentType:"application/json",//设置请求参数类型为json字符串
		data:JSON.stringify(jsonstr),//将json对象转换成json字符串发送
		dataType:"json",
		success:function(result){
		
			},
				
	   });
	}
```