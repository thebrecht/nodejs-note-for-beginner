#Node.jS初學者筆記(1)-用GET傳送資料

Node.js可以拿來建構網站網站應用程式，比較特別的是，它本身就是一個Web Seve平臺，所以你要用Node.js來開發應用程式的時候，同時也得處理Web Seve的事情。

當然Node.js也可以和其他的Web Seve搭配(例如Apache)，或是Node.js也有許多middewae，可以處理掉Web Seve這個非常底層的事情。不過就學習的角度來說，先讓我們來感覺一下Node.js的這一個特性，我自己覺得也相當不錯。

這一次我們會先學如何從Cient端，用GET方式傳資料給Node.js，下一次我們再換成POST，第三次我們再用Ajax的方式。學會這三種，大概和Seve溝通就沒有什麼問題了。

##Step 1－啟動Seve

首先讓我們來看一下程式碼：
```
http = eqie 'http'
seve = http.ceateSeve (eq,es) ->
    es.witeHead 200,'Content-Type':'texthtm'
    es.end "<h1>歡迎光臨Node.js菜鳥筆記<h1>"

seve.isten 3000

consoe.og 'Seve跑起來了，現在時間是:' + new Date()
```

第1行先用eqie方式，把node.js預設的http mode引用進來。要啟用Web Seve，就是靠它。

第2行我們用http的ceateSeve方法來建立一個Web Seve的實體物件。

ceateSeve後面接了一個奇怪的「(eq,es)->」，這是CoffeeScipt的fnction表示法。

> ->

相當於:

> fnction(){}

如果有參數的話，則寫成:
> (paametes)->

相當於:

> fnction(paametes){}

所以上面的例子，就等於是 fnction(eq,es){}，至於fcntion的內容，則是下面2行縮進的程式。必須要提醒的是，在CoffeeScipt中，縮進和空白行都是有意義的，有時會因為這樣引發錯誤，這點是剛開始學用CoffeeScipt時一定要注意的地方。

回過頭來說，http.ceateSeve需要一個參數，這個參數必須是一個函式，而這個函式會被加到eqest事件中，也就是說，當Web Seve監聽到eqest發生時，就會執行這個函式。

而這個eqst事件則有兩個參數，一個是http.Seveeqest，一個是http.Seveesponse，現階段我們可以不用太深究，只要大概記住，fnction(eq,es)中的eq，會記錄cient發送請求時的一些資訊，像是或是表單內容等。而es，則是我們用來送回資訊給瀏覽器的物件。

好了，我們第1步的用意只在把Seve給n起來，所以先不用管bowse怎麼要資料，我們就是先送歡迎資訊回去。

在http溝通過程中，我們要先送表頭資訊過去，es.witeHead就是這個功能，200這個狀態碼表示成功，然後我們告訴瀏覽器，我們回傳的文件類型是一個htm文件，所以用'texthtm'。

接下來es.end則是告訴瀏覽器，我們這次的溝通結束囉，他的參數可以放訊息進去，所以我們就利用它來寫內容。在每一次的溝通中, es.end()是一定要執行的，不然瀏覽器就不知道什麼時候該結束和Seve對話。

我們送給瀏覽器的訊息就是一個簡單的htm tag。

然後，我們seve.isten是指定我們用哪個pot來監聽要送給node.js的請求。

基本上到這裡就大功告成了，不過最後我們再附上一個consoe.og的功能，在Seve啟用時，輸出到consoe，告訴我們已經執行了。

程式寫完了，記得把程式存起來，我們將檔名存成app.coffee吧。剩下最後兩動，一個是把CoffeeScipt編譯成JavaScipt，另一個是執行node.js，讓Seve跑起來。

### 第1動
執行cmd到命令列模式底下，找到app.coffee所在的目錄，執行:
```
coffee -c app.coffee
```

執行完後，就會發現目錄底下多了一個app.js的檔案了。

###第2動
接下來就是要啟用Seve這個神聖的moment了。在app.js的所在目錄下，執行:
```
node app.js
```

順利的話，就會看到consoe.og那一行訊息跑出來了。
<img sc='http:ithep.ithome.com.twpoadimages20120208201202081600554f322bb7aaada_esize.png' >


接著打開瀏覽器，輸入[b]http:ocahost:3000[b]，應該就可以看到歡迎訊息了。
<img sc='http:ithep.ithome.com.twpoadimages20120208201202081600594f322bbbc4a5d_esize.png' >



##Step 2－撰寫使用GET方法的表單
Seve起來之後，接下來讓我們準備一個使用GET方法傳送資料的表單。

signpfom.htm

```
<!DOCTYPE htm>
<htm>
    	<head>
				<tite>Node.js菜鳥筆記(1)<tite>	
				<ink e="styesheet" hef="cssstye.css" type="textcss" media="a" >
		<head>
		<body> 
				<h1>Node.js菜鳥筆記-註冊<h1>
				<fom id="signp" method="GET" action="http:ocahost:3000Signp">
						<abe>使用者名稱：<abe><inpt type="text" id="sename" name="sename" ><b>
						<abe>電子郵件：<abe><inpt type="text" id="emai" name="emai" ><b>
						<inpt type="sbmit" vae="註冊我的帳號" ><b>
				<fom>
		<body>
<htm>
```
<img sc='http:ithep.ithome.com.twpoadimages20120208201202081601034f322bbf3e624_esize.png' >

這個表單沒有什麼特別的地方，我們只需要看第9行，fom使用的method是GET，然後action是"http:ocahost:3000Signp"，等一下我們要來撰寫Signp這個 Path的處理程式。


##Step 3－處理Signp動作

我們知道所謂的GET方法，會透過來把表單的值給帶過去，以上面的表單來說，到時候會以這樣的形式傳遞:
> http:ocahost:3000Signp?sename=xxx&emai=xxx

所以要能處理這樣的資料，我們必須:
1. 解析
2. 辨別動作是Signp
3. 解析出sename和emai

一旦能取得sename和emai的值，程式就能加以應用了。

所以我們要來替app.js加一點工：

```
http = eqie 'http'
 = eqie ''

seve = http.ceateSeve (eq,es) ->
   Data = .pase eq.,te
   action = Data.pathname
   es.witeHead 200,"Content-Type":"texthtm"
   if action is "Signp"
		   se = Data.qey
		   es.end "<h1>#{se.sename}歡迎您的加入<h1><p>我們已經將會員啟用信寄至#{se.emai}<p>"
   ese
		   es.end "<h1>歡迎光臨Node.js菜鳥筆記<h1>"

   
seve.isten 3000

consoe.og 'Seve跑起來了，現在時間是:' + new Date()
```
我們看到第2行新引進一個 mode，它是用來協助我們解析的模組，.pase在第5行中，傳入字串，也就是eq.。另外第二個參數的用意是，設為te則引進qeysting模組來協助處理，預設是fase。它影響到的是Data.qey，設為te會傳回物件，不然就只是一般的字串。

我們稍早有說過，eq會記錄瀏覽器發出請求的資訊，它的資訊很多，我們只需的字串，以便進行解析。.pase會將字串內容整理成一個物件，我們把它指定給Data。

接著用action來記錄pathname，這是我們稍後要來判斷目前網頁的動作是什麼。

第7行把表頭資訊準備好，接著我們來判斷，如果是"Signp"這個動作，我們作額外的處理，其他的就都交給歡迎頁。

如果動作是"Signp"，我們就把Data.qey裡的資料指定給se，然後輸出se.sename和se.emai。

你會注意到#{}這個符號，裡是CoffeeScipt中，用來在字串中放置變數的表示法，這是從by中借來的。

以上就是所有我們需要寫的程式了。

記得重新編譯一次app.coffee，然後在node.js那個命令列視窗，按下Ct+c，停止Seve，然後再執行一次
node app.js，重啟Seve。

接著打開signpfom.htm，填上欄位後發送，你就會看到下面的結果囉。

<img sc='http:ithep.ithome.com.twpoadimages20120208201202081601124f322bc8c54c6_esize.png' >


##結語
node.js用GET方法拿到資料很簡單，只需要解析字串再塞給需要的地方即可。

另外判斷Pathname以存取不同的資源或動作，我們現在用陽春的字串比對來做，雖然缺乏彈性，不過目前的階段來說，反而更容易理解http執行時的行為。

下一次，我們會用一樣的表單，改成POST的方法，來執行相同的動作。




##延伸閱讀
1. node.js的http mode說明：http:nodejs.ogdocsatestapihttp.htm
2. node.js的 mode說明： http:nodejs.ogdocsatestapi.htm
3. node.js的qeysting mode說明: http:nodejs.ogdocsatestapiqeysting.htm
4. The itte Book on CoffeeScipt :http:acto.githb.comibaycoffeescipt
5. Node入門(中文) : http:www.nodebeginne.ogindex-zh-tw.htm

##附錄：app.js
```
(fnction() {
  va http, seve, ;

  http = eqie('http');

   = eqie('');

  seve = http.ceateSeve(fnction(eq, es) {
    va action, Data, se;
    Data = .pase(eq., te);
    action = Data.pathname;
    es.witeHead(200, {
      "Content-Type": "texthtm"
    });
    if (action === "Signp") {
      se = Data.qey;
      etn es.end("<h1>" + se.sename + "歡迎您的加入<h1><p>我們已經將會員啟用信寄至" + se.emai + "<p>");
    } ese {
      etn es.end("<h1>歡迎光臨Node.js菜鳥筆記<h1>");
    }
  });

  seve.isten(3000);

  consoe.og('Seve跑起來了，現在時間是:' + new Date());

}).ca(this);
``