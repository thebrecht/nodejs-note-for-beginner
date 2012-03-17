#Node.jS初學者筆記(3)-用Ajax傳送資料

在Node.js要使用Ajax傳送資料，並且與之互動，在接受資料的部份沒有太大的差別，client端不是用GET就是用POST來傳資料，重點在處理完後，用jSON格式回傳。

當然Ajax不見得只傳JSON格式，有時是回傳一段HTML碼，不過後者對伺服器來說，基本上就和前兩篇沒有差別了。所以我們還是以回傳JSON做為這一回的主題。


##Step.0 這次我們要做的工作
這一回其實大多數的工作都會落在前端Ajax上面，前端要負責發送與接收資料，並在接收資料後，撤掉原先發送資料的表單，並將取得的資料，改成HTML格式之後，放上頁面。

我們用圖來說明一下：

(1)傳送資料的表單，就外觀上和前面沒有什麼差別

<img src='http://ithelp.ithome.com.tw/upload/images/20120213/201202132236124f391fdc70ed6_resize.png'/>

(2)送出表單，接收資料後結果

<img src='http://ithelp.ithome.com.tw/upload/images/20120213/201202132236164f391fe092de1_resize.png' />


這裡特別加上一個會員id的資料，是在Server端加入了，當作是Server額外處理過資料再送回Client端的動作。

至於Server端，基本上有點涼，就像前言說的，把收到的資料轉成JSON就收工了。


##Step.1 Ajax表單大改造
我們把之前的signupform.html改名為ajaxSignupForm.html，然後開始內部大改造。

	<!DOCTYPE html>
	<html>
	<head>
		<title>Node.js菜鳥筆記(3)</title>	
			<link rel="stylesheet" href="/css/style.css" type="text/css" media="all" />
			<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script>
	</head>
	<body> 
		<h1>Node.js菜鳥筆記-註冊(用Ajax)</h1>
			<div id="signup">
					<label>使用者名稱：</label><input type="text" id="username" name="username" /><br>
					<label>電子郵件：</label><input type="text" id="email" name="email" /><br>
					<input type="submit" value="註冊我的帳號" /><br>
			</div>
			<script type="text/javascript">
				$(function(){
				   $('#signup input[type="submit"]').click(function(e){						   	 
					   	e.preventDefault();
				       var user = {};
				       user.username = $("#username").val();
				       user.email = $("#email").val();
				       $.post("/Signup",user,function(data){
							    greet(data)								
							 });
					  });
						   
				    var greet = function(msg){
						   $('#signup').html("");
							 var greeting = $('<h2>Hi,'+msg.username+',你的會員id是:'+ msg.id +'我們會將會員啟動信寄至'+msg.email+'</h2>');
						   $('#signup').append(greeting);
					  };	   
				});		 
			</script>			
	</body>
	</html>
在第6行，我們用了Google的CDN來使用jQuery，用來幫我們處理Ajax的工作。

第10行原本是form的html tag，為了強調這是一個Ajax的表單，所以我們把form易為div。其他的就和之前的無異。

第15行開始，這次要傳送和接收的工作，都在接下來的JavaScript當中。我們要做的事有：

1. 用jQUery取得submit按鈕，綁定它的click動作(第17行)
2. 取得表單username和email的值，存放在user這個物件中(第19~21行)
3. 用jQuery的$.post方法，將user的資料傳到Server(第22行)
4. 一旦成功取得資料後，透過greet這個function，組成回報給user的訊息(第23行)
　　1. 清空原本給使用者填資料的表單(第28行)
　　2. 將Server回傳的username、email和id這3個資料，組成回應的訊息(第29行)
　　3. 將訊息放到原本表單的位置(第30行)

經過以上的處理後，一個Ajax的表單的基本功能已經完成。


##Step.2 Node.js處理JSON
先讓我們來看一下Server端的程式碼：

	http = require 'http'
	url = require 'url'
	qs = require 'querystring'

	server = http.createServer (req,res)->
   		urlData = url.parse req.url,true
   		action = urlData.pathname

   		if action is "/Signup"
       			formData = ''
       			msg = ''
       			req.on 'data',(data)->
         			formData += data
         
         		req.on 'end', ->
           			user = qs.parse formData
           			user.id = "123456"
           			msg = JSON.stringify user
           			res.writeHead 200,{"Content-Type":"application/json","Content-Length":msg.length}
           			res.end msg

   		else
      			res.writeHead 200,"Content-Type":"text/html"
      			res.end "<h1>歡迎光臨Node.js菜鳥筆記</h1>"
      
			server.listen 3000

			console.log "Server跑起來了，現在時間是" + new Date()

這裡的程式和上一篇POST方法，基本上大同小異，差別在：

1. 我們幫user的資料加上id，隨意存放一些文字進去，讓Server回傳的資料多於Client端傳上來的，不然會覺得Server都沒做事 :p
2. 我們增加了msg這個變數，存放將user物件JSON文字化的結果。JSON.stringify這個轉換函式是V8引擎所提供的，如果你好奇的話。
3. 大重點來了，我們要告訴Client端，這次回傳的資料格式是JSON，所在Content-type和Content-Length要提供給Client。

Server很輕鬆就完成任務了。


##Step.3 踢到鐵板了，開始找bug

既然前、後端都完成了，就讓我們快快樂樂把ajaxSignupForm.html開啟，品嚐一下勝利的果實吧...

填完資料了，送出...

沒反應...

打開firebug查一下，嗯，資料的的確確有送出，但是回應那邊卻是沒有接收到資料...

於是接下來就是展開各種debug手法，試圖找出錯誤，在上窮碧落下黃泉之後，才突然拍額大叫：「我是豬頭!!」

原來擋在我們前頭的五指山是...*JavaScript的同源法則*。

簡單地說，JavaScript為了安全性起見，限制存取動作必須是在同一domain name底下。

前兩篇為了強調說明GET和POST這兩個行為，所以我們的signupform.html不是放在Server上，而是放在client端(好啦，也有偷懶的成份)，對於一般form表單的行為行得通，但是一遇到Ajax，client的程式和Server端當然不在同一個domain name底下，所以悲劇就發生了。

所以呢，我們要做的事，就是把ajaxSignupForm也放在Server上，這樣就沒問題了。


##Step.4 將Ajax Form放上Server
要在node.js中載入靜態檔案，我們必須在多一個fs的module來協助我們處理，fs是Node.js用來處理檔案讀取、寫入的模組。

另外，我們將建一個public的目錄，當作是ajaxSignupForm.html的存放處。

另外，由於我們同樣用"/Signup"來處理表單的傳送與結果的呈現，所以我們必須判斷當Client是用"GET"來請求"/Signup"時，我們就載入ajaxSignupForm.html，而當Client是用"POST"發出請求時，就代表Client送出了資料，這時我們就要處理送來的資料並且回傳。

改寫後的程式碼如下：

	http = require 'http'
	url = require 'url'
	qs = require 'querystring'
	fs = require 'fs'

	server = http.createServer (req,res)->
   		urlData = url.parse req.url,true
   		action = urlData.pathname
   		publicPath = __dirname + "\\public\\"

   	if action is "/Signup"
      		if req.method is "POST"
       		formData = ''
       		msg = ''
       		req.on 'data',(data)->
         		formData += data
         
         		req.on 'end', ->
           			user = qs.parse formData           
           			user.id = "123456"
           			msg = JSON.stringify user
           			res.writeHead 200,{"Content-Type":"application/json","Content-Length":msg.length}
           			res.end msg
         
      		else
       		form = publicPath + "ajaxSignupForm.html"
       		fs.readFile form,(err,contents) ->
         	#檔案如果沒發生錯誤，就把讀取進來的內容送出去，否則送出500這個錯誤狀態碼
         	if err isnt true
           		res.writeHead 200,"Content-Type":"text/html"
           		res.end(contents)
         	else
           		res.writeHead 500
           		res.end

   	else
      		res.writeHead 200,"Content-Type":"text/html"
      		res.end "<h1>歡迎光臨Node.js菜鳥筆記</h1><p><a href=\"/Signup\">註冊</a></p>"
      
	server.listen 3000

	console.log "Server跑起來了，現在時間是" + new Date()

首先讓我們注意一下第9行。我們設定一個變數來存放public這個目錄在檔案系統中的位置。由於我是在Windows系統中寫這個程式碼，加上要避開特殊字元，所以public要寫成*"\\public\\"*，如果是在linux系統中，只要寫成*"/public/"*即可。

另外*__dirname*是Node.js的全域物件，用來表示執行程式所在的路徑。由於我們是在app.js所在目錄底下建立plubc目錄，所以用第9行這樣的方式來表達。

第12行就是用來判斷client是用什麼方法來和Server溝通，如果來的是POST，就和之前一樣，完全不用動。

不過來的如果是GET，我們就要用第26行開始的方法來處理。

第26行我們先組成表單的存取路徑和檔名，然後用fs.readFile來讀取檔案。

fs.readFile是一個*非同步*的處理函式，所以第1個參數指定檔案路徑，第2個參數則是callback函式，負責檔案讀取後的處理行為。它有兩個傳入的參數，第1個是錯誤發生時的錯誤物件，第2個則是讀入的檔案內容。

所以當錯誤沒有發生的時候，我們就把讀入的content送回去給client端，萬一有錯誤，則送回500狀態。

以上，就是修改後的版本。

記得*重新編譯app.coffee*，然後執行Server，這次就能成功的傳回資料了（淚～）


##延伸閱讀
1. node.js的http module說明: http://nodejs.org/docs/latest/api/http.html
2. node.js的url module說明: http://nodejs.org/docs/latest/api/url.html
3. node.js的querystring module說明: http://nodejs.org/docs/latest/api/querystring.html
4. node.js的File System module說明: http://nodejs.org/docs/latest/api/fs.html
5. The Little Book on CoffeeScript: http://arcturo.github.com/library/coffeescript/
6. Node入門(中文): http://www.nodebeginner.org/index-zh-tw.html
