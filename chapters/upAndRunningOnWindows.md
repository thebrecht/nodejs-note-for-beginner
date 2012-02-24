#在Windows安裝Node.js和CoffeeScript超簡單!!

如何在Windows上安裝Node.js和CoffeeScript？之前Google過，有不少(搞工)方法，今天才發現原來Winodws上已經有一鍵安裝的MSI檔了，真是太幸福了。

不囉嗦的安裝下載點：[http://nodejs.org/#download]

想多了解的請繼續往下。

##什麼是Node.js與CoffeeScript

Node.js是什麼，如果要用最簡單的一句話來解釋，Node.js能讓你在伺服器端使用JavaScript來開發應用程式或服務。

那CoffeeScript又是什麼呢，它是一個新的語言，能讓你用類似Ruby+Python的綜合的語法來寫JavaScript，之後再編譯成JavaScript去執行。

這樣做有幾個好處，寫法比較簡潔、補足一些現今JavaScript比較棘手的用法（例如缺少Class）、避掉一些JavaScript的缺點（像是全域變數等等）。

不過這樣做的利弊如何，還是有待時間考驗，雖然理論上用CoffeeScript的生產力會提高，但要學CoffeeScript不代表你可以不懂JavaScript，換言之，用CoffeeScript的代價是，為了一套語言，你得懂兩套語法。

另外一個當然是在co-work上面的問題，你自己學得很high，但你的同事不願意學，那也會有問題。雖然CoffeeScript編譯後產生的是純正的JavaScript程式碼，但是老實說，沒有足夠功力，它產出的程式碼不見得好懂，而且在維護上也不可能一個人修改編譯的程式碼，一個人維護CoffeeScript的程式碼。

不過即便如此，CoffeeScript還是相當優秀的語言，花點時間了解、學習這個新技術，也不是壞事。

##輕輕鬆鬆快速安裝

好啦，現在就讓我們來安裝吧。

[http://nodejs.org/#download]

上面的網址會帶你到Node.js的官網，進去後就可以看到Windows Installer，下載下來執行Node.js就完工了。

另外，Node.js有豐富的套件可以使用，因此也發展出了套件管理程式－*npm*，當你裝完上面的安裝版之後，它也幫你搞定了。

CoffeeScript必須依賴Node.js，所以要嘗試這杯咖啡的話，上面的步驟一定要先完工。

之後，就可以開始安裝CoffeeScript了，語法也很簡單，一行搞定。請先進去Windows的命令列模式，然後輸入：

`npm install -g coffee-script`

npm就會到網路上抓回最新版的CoffeeScript來安裝了。

##簡單測試語法

接下來可以測試一下，我寫了一個「firstCupOfCoffee.coffee」的檔案，內容是：
`getCoffee = (typeOfCoffee) ->  
  "您點的咖啡是#{typeOfCoffee} "  `

然後編譯這個檔案，語法是:
`coffee -c firstCupOfCoffee.coffee`


它就會幫我編譯出一個firstCupOfCoffee.js，編譯出的內容則是：
`(function() {  
  var getCoffee;  
  
  getCoffee = function(typeOfCoffee) {  
    return "您點的咖啡是" + typeOfCoffee + " ";  
  };  
  
}).call(this);`

以上就是我自己的CoffeeScript初體驗。現在能在Winodws上寫CoffeeScript，真是方便多了。

另外，如果無法在命令列執行node、npm的語法，就有可能是環境變數沒有指到它們的執行檔，只要去Windows的環境變數中加上檔案所在即可。
