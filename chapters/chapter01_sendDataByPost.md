#Node.jS初學者筆記(1)-用GET傳送資料

Node.js可以拿來建構網站/網站應用程式，比較特別的是，它本身就是一個Web Server平臺，所以你要用Node.js來開發應用程式的時候，同時也得處理Web Server的事情。

當然Node.js也可以和其他的Web Server搭配(例如Apache)，或是Node.js也有許多middleware，可以處理掉Web Server這個非常底層的事情。不過就學習的角度來說，先讓我們來感覺一下Node.js的這一個特性，我自己覺得也相當不錯。

這一次我們會先學如何從Client端，用GET方式傳資料給Node.js，下一次我們再換成POST，第三次我們再用Ajax的方式。學會這三種，大概和Server溝通就沒有什麼問題了。

如果我學會websocket怎麼用的話，也許就會有第四個筆記了。

##Step 1－啟動Server

首先讓我們來看一下程式碼：
```
http = require 'http'
server = http.createServer (req,res) ->
    res.writeHead 200,'Content-Type':'text/html'
        res.end "<h1>歡迎光臨Node.js菜鳥筆記</h1>"

    server.listen 3000

	console.log 'Server跑起來了，現在時間是:' + new Date()

```
第1行先用require方式，把node.js預設的http module引用進來。要啟用Web Server，就是靠它。

第2行我們用http的createServer方法來建立一個Web Server的實體物件。

createServer後面接了一個奇怪的「(req,res)->」，這是CoffeeScript的function表示法。

> ->

	相當於
	
> function(){}

如果有參數的話，則寫成:

> (parameters)->

相當於:

> function(parameters){}

所以上面的例子，就等於是 function(req,res){}，至於fucntion的內容，則是下面2行縮進的程式。必須要提醒的是，在CoffeeScript中，縮進和空白行都是有意義的，有時會因為這樣引發錯誤，這點是剛開始學用CoffeeScript時一定要注意的地方。

回過頭來說，http.createServer需要一個參數，這個參數必須是一個函式，而這個函式會被加到request事件中，也就是說，當Web Server監聽到request發生時，就會執行這個函式。

而這個requst事件則有兩個參數，一個是http.ServerRequest，一個是http.ServerResponse，現階段我們可以不用太深究，只要大概記住，function(req,res)中的req，會記錄client發送請求時的一些資訊，像是url或是表單內容等。而res，則是我們用來送回資訊給瀏覽器的物件。

好了，我們第1步的用意只在把Server給run起來，所以先不用管browser怎麼要資料，我們就是先送歡迎資訊回去。

在http溝通過程中，我們要先送表頭資訊過去，res.writeHead就是這個功能，200這個狀態碼表示成功，然後我們告訴瀏覽器，我們回傳的文件類型是一個html文件，所以用'text/html'。

接下來res.end則是告訴瀏覽器，我們這次的溝通結束囉，他的參數可以放訊息進去，所以我們就利用它來寫內容。在每一次的溝通中, res.end()是一定要執行的，不然瀏覽器就不知道什麼時候該結束和Server對話。

我們送給瀏覽器的訊息就是一個簡單的html tag。

然後，我們server.listen是指定我們用哪個port來監聽要送給node.js的請求。

基本上到這裡就大功告成了，不過最後我們再附上一個console.log的功能，在Server啟用時，輸出到console，告訴我們已經執行了。

程式寫完了，記得把程式存起來，我們將檔名存成app.coffee吧。剩下最後兩動，一個是把CoffeeScript編譯成JavaScript，另一個是執行node.js，讓Server跑起來。

##第1動
執行cmd到命令列模式底下，找到app.coffee所在的目錄，執行:
```
coffee -c app.coffee
```

執行完後，就會發現目錄底下多了一個app.js的檔案了。

##第2動
接下來就是要啟用Server這個神聖的moment了。在app.js的所在目錄下，執行:
```
node app.js
```

順利的話，就會看到console.log那一行訊息跑出來了。
<img src='http://ithelp.ithome.com.tw/upload/images/20120208/201202081600554f322bb7aaada_resize.png' />

接著打開瀏覽器，輸入http://localhost:3000，應該就可以看到歡迎訊息了。

<img src='http://ithelp.ithome.com.tw/upload/images/20120208/201202081600594f322bbbc4a5d_resize.png' />


##Step 2－撰寫使用GET方法的表單

Server起來之後，接下來讓我們準備一個使用GET方法傳送資料的表單。

*signupform.html*
```
<!DOCTYPE html>
<html>
	<head>
		<title>Node.js菜鳥筆記(1)</title>
		<link rel="stylesheet" href="css/style.css" type="text/css" media="all" />
	</head>
	<body>
		<h1>Node.js菜鳥筆記-註冊</h1>
		<form id="signup" method="GET" action="http://localhost:3000/Signup">
			<label>使用者名稱：</label><input type="text" id="username" name="username" /><br>
			<label>電子郵件：</label><input type="text" id="email" name="email" /><br>
			<input type="submit" value="註冊我的帳號" /><br>
		</form>
	</body>
</html>
```

<img src='http://ithelp.ithome.com.tw/upload/images/20120208/201202081601034f322bbf3e624_resize.png' /> 

這個表單沒有什麼特別的地方，我們只需要看第9行，form使用的method是GET，然後action是"http://localhost:3000/Signup"，等一下我們要來撰寫/Signup這個URL Path的處理程式。 


##Step 3－處理Signup動作

我們知道所謂的GET方法，會透過URL來把表單的值給帶過去，以上面的表單來說，到時候URL會以這樣的形式傳遞:

> http://localhost:3000/Signup?username=xxx&email=xxx

所以要能處理這樣的資料，我們必須:

1. 解析URL
2. 辨別動作是Signup
3. 解析出username和email

一旦能取得username和email的值，程式就能加以應用了。

所以我們要來替app.js加一點工：
```
http = require 'http'
url = require 'url' 

server = http.createServer (req,res) -> 
  urlData = url.parse req.url,true
  action = urlData.pathname
  
  res.writeHead 200,"Content-Type":"text/html"
  if action is "/Signup"
    user = urlData.query
    res.end "<h1>#{user.username}歡迎您的加入</h1><p>我們已經將會員啟用信寄至#{user.email}</p>"
  else
    res.end "<h1>歡迎光臨Node.js菜鳥筆記</h1>"
  
server.listen 3000
  
console.log 'Server跑起來了，現在時間是:' + new Date()
```

我們看到第2行新引進一個url module，它是用來協助我們解析URL的模組，url.parse在第5行中，傳入url字串，也就是req.url。另外第二個參數的用意是，設為ture則引進querystring模組來協助處理，預設是false。它影響到的是urlData.query，設為true會傳回物件，不然就只是一般的字串。

我們稍早有說過，req會記錄瀏覽器發出請求的資訊，它的資訊很多，我們只需url的字串，以便進行解析。url.parse會將字串內容整理成一個物件，我們把它指定給urlData。

接著用action來記錄pathname，這是我們稍後要來判斷目前網頁的動作是什麼。

第7行把表頭資訊準備好，接著我們來判斷，如果是"/Signup"這個動作，我們作額外的處理，其他的就都交給歡迎頁。

如果動作是"/Signup"，我們就把urlData.query裡的資料指定給user，然後輸出user.username和user.email。

你會注意到#{}這個符號，裡是CoffeeScript中，用來在字串中放置變數的表示法，這是從Ruby中借來的。

以上就是所有我們需要寫的程式了。

記得重新編譯一次app.coffee，然後在node.js那個命令列視窗，按下Crtl+c，停止Server，然後再執行一次node app.js，重啟Server。

接著打開signupform.html，填上欄位後發送，你就會看到下面的結果囉。 

<img src='http://ithelp.ithome.com.tw/upload/images/20120208/201202081601124f322bc8c54c6_resize.png' />

##結語

node.js用GET方法拿到資料很簡單，只需要解析url字串再塞給需要的地方即可。 

另外判斷Pathname以存取不同的資源或動作，我們現在用陽春的字串比對來做，雖然缺乏彈性，不過目前的階段來說，反而更容易理解http執行時的行為。

下一次，我們會用一樣的表單，改成POST的方法，來執行相同的動作。

##延伸閱讀
1. node.js的http module說明：http://nodejs.org/docs/latest/api/http.html 
2. node.js的url module說明： http://nodejs.org/docs/latest/api/url.html 
3. node.js的querystring module說明: http://nodejs.org/docs/latest/api/querystring.html 
4. The Little Book on CoffeeScript: http://arcturo.github.com/library/coffeescript/ 
5.Node入門(中文): http://www.nodebeginner.org/index-zh-tw.html 
