#Node.jS初學者筆記(2)-用POST傳送資料

我們將延續上一篇用GET方式的作法，改成POST之後，傳送資料到Web Server。

##Step 1－將表單改用POST傳送

讓我們資源回收一下，把之前signupform.html的表格，由GET改成POST即可。

*signupform.html*

	<!DOCTYPE html>
	<html>
  		<head>
    			<title>Node.js菜鳥筆記(2)</title>	
			<link rel="stylesheet" href="css/style.css" type="text/css" media="all" />
  		</head>
  		<body> 
    			<h1>Node.js菜鳥筆記-註冊</h1>
			<form id="signup" method="POST" action="http://localhost:3000/Signup">
	  			<label>使用者名稱：</label><input type="text" id="username" name="username" /><br>
	  			<label>電子郵件：</label><input type="text" id="email" name="email" /><br>
	  			<input type="submit" value="註冊我的帳號" /><br>
			</form>
  		</body>
	</html>

看到第9行，我們已經改成POST了，第一步就這樣收工。當然title改成第二回比較不會錯亂。


##Step 2－監聽data與end事件來處理POST
讓我們先寫出改成POST之後，需要的程式碼。

*app.coffee*

	http = require 'http'
	url = require 'url'
	qs = require 'querystring'

	server = http.createServer (req,res) ->
   		urlData = url.parse req.url,true
   		action = urlData.pathname
   		res.writeHead 200,"Content-Type":"text/html"
   		if action is "/Signup"
		   formData = ''
		   req.on "data",(data)->
				   formData += data

		   req.on "end", ->
				   user = qs.parse formData
				   res.writeHead 200,"Content-Type":"text/html"
				   res.end "<h1>#{user.username}歡迎您的加入</h1><p>我們已經將會員啟用信寄至#{user.email}</p>"
  		 else
		  res.end "<h1>歡迎光臨Node.js菜鳥筆記</h1>"

   
	server.listen 3000

	console.log 'Server跑起來了，現在時間是:' + new Date()

第3行我們加入了'querystring'這個moduel，方便我們等一下解析由表單POST回來的資料。

第10行我們加入一個formData的變數，用來搜集待等一下表單回傳的資料。

第11行開始，就是本文的重點了。對JavaScript有點認識的朋友，應該都知道JavaScript是一種事件驅動的語言，也就是說，它一直在監聽著各種事件來決定它要如何工作；有事件，它才有動作。這種特性一樣在node.js上體現。

在上一篇文章中，我們只從req拿出url的資料，這次我們要在它身上加上事件。JavaScript在訂閱事件時使用addEventListener，而node.js使用的則是on。

第11行我們加上了監聽'data'的事件，它會在瀏覽器傳送資料到Web Server時被執行，參數是它所接收到的資料，型態是字串。第14行，我們加上'end'的事件，當瀏覽器的請求事件結束時，它就會動作。

由於瀏覽器使用POST在上傳資料時，會將資料一塊塊地上傳，因為我們在監聽data事件時，透過formData變數將它累加起來。由於我們上傳的資料很少，一次就結束，不過如果日後需要傳的是資料比較大的檔案，這個累加動作就很重要。

當資料傳完，就進到end事件中，我們用到qs.parse來解析formData。formData的內容是字串，內容是：

> username=wordsmith&email=wordsmith%40some.where

而qs.parse可以幫我們把這個querystring轉成物件的格式，也就是：

> {username=wordsmith&email=wordsmith%40some.where}

一旦轉成物件並指定給user之後，其他的事情就和GET方法時操作的一樣，寫response的表頭，將內容回傳，並將user.username和user.email代入到內容中。

##Step 3－app.js up and run

接下來的部分就簡單了。

1)將app.coffee編譯成app.js
	coffee -c app.coffee

2)執行app.js
	node app.js

3.用Signup表單發送username和email的資料

4.收到完事的回應。

收工。


好了，用POST傳送資料就介紹到這裡了，下一篇我們將用Ajax的方式來傳送資料。

##延伸閱讀
1. node.js的http module說明：http://nodejs.org/docs/latest/api/http.html
2. node.js的url module說明：http://nodejs.org/docs/latest/api/url.html
3. node.js的querystring module說明:http://nodejs.org/docs/latest/api/querystring.html
4. The Little Book on CoffeeScript: http://arcturo.github.com/library/coffeescript/
5. Node入門(中文):http://www.nodebeginner.org/index-zh-tw.html

