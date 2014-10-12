Day 14: 在 Azure 上使用內容傳遞網路 (CDN)
================================

# 前言

本文將簡單介紹如何在 Azure Management Portal 中建立一個 CDN 服務，以讓世界各地的訪客瀏覽您的網站或服務時能有更快的載入速度。

# 什麼是 CDN?

![CDN](https://raw.githubusercontent.com/hungys/azure-blog/master/media/14-using-azure-cdn/cdn-concept.png)

CDN 是 Content delivery network 的縮寫，中文可翻內容傳遞網路。這項技術主要是用在大型商業性網站，可以將網站中的靜態內容（例如 jpg 圖檔）或是動態內容（例如資料庫查詢）緩存在世界各地的 CDN 節點中。假設您的服務佈建在 Azure 的香港資料中心，如果訪客的所在地是在美國，則每次 request 都需要連線到香港的伺服器來取得資料，造成 response time 較高的結果。如果使用了 CDN 服務，而且在美國佈建有節點的話，則可以直接從美國的節點取得和香港伺服器一樣的內容，不僅讓使用者端有更快的讀取時間，也減輕了主要伺服器的負擔。

其中最重要的是，這一系列過程都是自動實現的，在設定好之後您只需要依照平常的方式來部署網站，隨後主伺服器上的檔案便會將需要緩存的內容提交的世界各地的節點儲存，當訪客瀏覽您的網站時，DNS 伺服器會自動導引到最近或最快的緩存節點上。

目前 Azure CDN 在全球各地都佈建有節點，而台灣部分也跟上 Amazon CloudFront 的腳步增加了節點伺服器，根據 [MSDN 文件](http://msdn.microsoft.com/zh-tw/library/azure/gg680302.aspx)指出，目前東亞地區已經有香港、新加坡、雅加達及**高雄**等節點。

# 建立儲存體帳戶

在示範 CDN 設定之前，首先我們先建立一個 Storage 帳戶來存放一些靜態內容，建立的方式相當簡單，只需要在 Management Portal 中點選**「New」**並選取**「Data Service」**>**「Storage」**>**「Quick Create」**即可。

![Create storage account](https://raw.githubusercontent.com/hungys/azure-blog/master/media/14-using-azure-cdn/create-storage-account.png)

接下來讀者就可以開始建立 container 並使用相關工具來上傳檔案，若是 Windows 用戶可以參考 [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)，提供了簡便的圖形化界面來管理儲存體帳戶。

![Storage account](https://raw.githubusercontent.com/hungys/azure-blog/master/media/14-using-azure-cdn/storage-account-dashboard.png)

本文在此直接使用 xplat-CLI 來示範如何上傳檔案，對於 `azure storage` 底下的相關指令皆需要搭配 Key 來使用，只需要在指令後加上下面的 options 即可：

```
$ azure storage [command] -a <YOUR ACCOUNT NAME> -k <YOUR PRIMARY KEY HERE>
```

若要上傳檔案到指定的 container，可以使用下列指令：

```
$ azure storage blob upload ~/Desktop/test.png <CONTAINER NAME> test.png -a <ACCOUNT NAME> -k <PRIMARY KEY>
info:    Executing command storage blob upload
+ Checking test.png in container photo                             
\ Uploading /Users/hungys/Desktop/test.png to blob test.png in con+ainer photo
Percentage: 100.0% (90.94KB/90.94KB) Average Speed: 90.94KB/S Elapsed Time: 00:00:00
```

# 建立 CDN 服務

接下來我們要開始設定 CDN 的服務，同樣在 Management Portal 底下點選**「New」**並選取**「App Service」**>**「CDN」**>**「Quick Create」**。在此會列出一個您帳戶底下所有 domain 的清單，請選擇剛剛所建立的儲存體服務所位在的域名。

![Create CDN](https://raw.githubusercontent.com/hungys/azure-blog/master/media/14-using-azure-cdn/create-cdn.png)

這個動作只需要數秒就可以完成，到此 CDN 服務就已經設定完成了，其中「[http://az676418.vo.msecnd.net/](http://az676418.vo.msecnd.net/)」就是我們用來存取 CDN 的端點。不過根據官方網站的記載，端點建立的組態無法立即使用，最多需要 60 分鐘進行註冊，以透過 CDN 網路傳播。這時嘗試立即使用 CDN 網域名稱的使用者可能會收到狀態碼 400 (不正確的要求)，直到可透過 CDN 使用內容為止。

![CDN](https://raw.githubusercontent.com/hungys/azure-blog/master/media/14-using-azure-cdn/cdn-dashboard.png)

經過了漫長的等待，這時候我們已經可以使用剛剛所建立的 CDN 端點「[http://az676418.vo.msecnd.net/](http://az676418.vo.msecnd.net/)」來存取放在 container 中的 blob 了！預設情況下 CDN 並不支援 HTTPS 及 Query String，若您有這樣的需求可以視情況在 Management Portal 中開啟。

![from CDN](https://raw.githubusercontent.com/hungys/azure-blog/master/media/14-using-azure-cdn/from-cdn.png)

# 參考資料

- 使用 Azure 的 CDN, [http://azure.microsoft.com/zh-tw/documentation/articles/cdn-how-to-use/](http://azure.microsoft.com/zh-tw/documentation/articles/cdn-how-to-use/)
- 內容傳遞網路, [http://zh.wikipedia.org/wiki/%E5%85%A7%E5%AE%B9%E5%82%B3%E9%81%9E%E7%B6%B2%E8%B7%AF](http://zh.wikipedia.org/wiki/%E5%85%A7%E5%AE%B9%E5%82%B3%E9%81%9E%E7%B6%B2%E8%B7%AF)