Day 15: Azure API Management 服務簡介
===============================

# 前言

API Management 是 Azure 上還滿新的一個服務，筆者一開始看到覺得還滿有趣的，但因為經驗還比較不足，所以花了一些時間才理解他所提供的服務內容以及想解決的問題。如果今天一家公司或一個 App 只有自己家 App 一項產品、API 只有自己會使用，那可能還沒辦法直接感受到 API Management 的方便，但如果今天提供的是一個服務，API 面向許多通路或是客戶，那麼 API Management 便會是一個很好的管理架構。

# API Management 簡介

若要用最簡單的方式來介紹 API Management，基本上他就是一個 API Proxy，每一個 API Management 服務都提供了一個「[https://*.portal.azure-api.net/](https://*.portal.azure-api.net/)」端點讓您呼叫自己的 API，主要的應用情境筆者理解如下：

- **匯集各方的 API 到同一個 endpoint**：假設產品所需要用到的 API 是來自各個第三方的 endpoint，透過 API Management 可以整到同一個端點底下來方便管理，若有端點的更新也可以直接從 Portal 抽換更新。
- **提供自己服務的 API 讓其他客戶使用**：假如公司的服務規模大到提供 API、SDK 來讓第三方使用，這時可能會有不同等級的區分，例如可能依照價格來控制每秒或每分鐘可以請求的流量，或是依照不同的服務等級有不同的 API 可以使用，這時便可以透過 API Management 來統一管理，減少原始 API 所需要做的變動。

此外，API Management 當然也提供了豐富的遙測功能，可以透過 Dashboard 來監控 API 的使用量，關於其他更多功能可以參考官方網站的說明：[http://azure.microsoft.com/zh-tw/services/api-management/](http://azure.microsoft.com/zh-tw/services/api-management/)。

# 建立 API Management 服務

若要申請一個 API Management 服務，同樣是透過 Management Portal 來建立。首先點選**「New」**並選取**「App Service」**>**「API Management」**>**「Create」**。

![create-service-01](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/create-service-01.png)

接著自己取一個網域名稱並選擇要部署的地區，並點選下一步輸入管理者資料即可完成建立，建立 API Management 到真正啟用需要一點時間，請耐心等候。

![create-service-02](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/create-service-02.png)

建立完成後便可以從 Management Portal 開啓，以筆者註冊的服務為例，API 的端點為「[https://cpbl.azure-api.net](https://cpbl.azure-api.net)」，而管理者界面的位置是「[https://cpbl.portal.azure-api.net](https://cpbl.portal.azure-api.net)」，您也可以透過下方的「Manage」按鈕進入管理界面。

![create-service-03](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/create-service-03.png)

進到 Dashboard 後便可以開始管理您的服務，首頁上也會有 API 使用的記錄。

![create-service-04](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/create-service-04.png)

# API Management 後台總覽

進到管理後台後，後台的功能主要可以分成以下幾個項目：

1. API 管理 (APIs)

	![dashboard-overview-01](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/dashboard-overview-01.png)
	
	在**「APIs」**分頁底下，您可以將您的 Web Service 加入，以透過 Azure 的 endpoint 來提供服務，而 API 的所有 endpoint 以及 operation 都可以在裡面做管理。
	
2. 產品管理 (Products)

	![dashboard-overview-02](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/dashboard-overview-02.png)
	
	在**「Products」**分頁中所謂產品的定義，以一家提供單一 API 服務的公司為例，便可以在裡面新增免費試用的產品「Free Trial」、低價位的「Starter」以及高價位的「Premium」三種產品，除了可以賦予三種產品不同的 API 權限，也可以針對它們的用量限制做管理。
	
3. 原則管理 (Polocies)

	![dashboard-overview-03](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/dashboard-overview-03.png)
	
	接續產品管理，在**「Policies」**中的原則便是對應到剛剛提到的「用量限制」，除了可以對產品賦予 usage quota 之外，甚至可以設定相關功能的權限，目前所提供的原則清單如下：
	
	* Allow cross domain calls
	* Authenticate with Basic
	* Authenticate with client certificate
	* Convert JSON to XML
	* Convert XML to JSON
	* CORS
	* Find and replace string in body
	* Get from cache
	* JSONP
	* Limit call rate
	* Mask URLs in content
	* Restrict caller IPs
	* Rewrite URI
	* Set HTTP header
	* Set query string parameter
	* Set usage quota
	* Store to cache
	
	假如沒有 API Management，要達到這些需求勢必得花上好一番功夫，但在 Azure 中已經為您寫好了絕大部分會使用到的原則管理。
	
4. 使用者管理 (Users/Groups)

	![dashboard-overview-04](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/dashboard-overview-04.png)
	
	在**「Users」**、**「Groups」**分頁中主要是用來管理 API 的訂閱用戶，也就是您所提供服務的對象，此外也可以使用群組來做管理。除了透過 Administrator 的界面來管理用戶，您也可以自己建立網站並使用 [REST API](http://msdn.microsoft.com/en-us/library/azure/dn776326.aspx) 來串接 API Management 的後台。
	
5. 數據分析 (Analytics)

	![dashboard-overview-05](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/dashboard-overview-05.png)
	
	在**「Analytics」**分頁中主要可以觀察各個用戶、API 的使用情形以及健康狀態，也可以用來監測訂閱者是否有異常的 API 呼叫行為。
	
除此之外，後台其他還有包括安全性、OAuth 等等的設定，就留給讀者自行去挖掘了。接下來我們將透過簡單示範來帶領讀者瞭解 API Management 的操作設定以及所能提供的服務內容。

# 建立 API

首先切換到 APIs 分頁，點選**「Add API」**，輸入您的 Web Service 相關資訊便可以將 API 加入 Azure 做管理，假設您的 Web Service 端點是「[https://myapi.cloudapp.net](https://myapi.cloudapp.net)」，希望在「[https://cpbl.azure-api.net/cpbl](https://cpbl.azure-api.net/cpbl)」來提供服務，則需要使用下列設定：

- Web API name: 任意，僅辨識用
- Web service URL: [https://myapi.cloudapp.net](https://myapi.cloudapp.net)
- Web API URL suffix: cpbl

![create-api-01](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/create-api-01.png)

建立完成後便可以進到 API 的管理界面，此時可以點選**「Add Operation」**來新增一個 API 端點：

![create-api-02](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/create-api-02.png)

接下來我們可以開始輸入 API 的相關資訊，例如 HTTP 的方法以及呼叫的端點，同時也可以設定所需的參數以及其他快取設定，愈完整的內容將可以幫助您的 API 用戶更輕鬆地來上手：

![create-api-03](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/create-api-03.png)

# 建立 Product

接下來切換到 Products 分頁來管理產品，預設已經幫我們建立好「Starter」及「Unlimited」兩個產品，前者有每分鐘 5 次呼叫及每週最多 100 次呼叫的限制，為了節省時間我們直接使用預設建立好的產品。

![create-product-01](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/create-product-01.png)

此時我們分別點入兩個產品，分別按下**「Add API to Product」**將剛剛建立的 API 加入到兩個產品之中，這樣一來訂閱這兩個產品的用戶才能使用 Azure 端點呼叫您的服務：

![create-product-02](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/create-product-02.png)

# 設定 Policy

我們在上面提到「Starter」產品有呼叫次數及頻率的限制，這些原則可以在 Policies 分頁中來達成。預設的範例已經幫我們設定好這項產品之下的所有 API，都有以下的限制：

* 呼叫頻率：`<rate-limit calls="5" renewal-period="60" />`，即每分鐘 5 次
* 呼叫額度：`<quota calls="100" renewal-period="604800" />`，即每週 100 次

若您要增加原則，也可以透過右方的快速鍵來做進一步設定。

![manage-policy](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/manage-policy.png)

# 測試 API

剛剛我們所在的位置是在「Administrator Poral」，也就是最高管理者的界面。接下來我們要進入**「Developer Portal」**，也就是您的客戶的開發者所會使用到的界面。您可以從 Dashboard 的右上角點選連結進入。

![test-api-01](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/test-api-01.png)

如上圖，因為我們已經登入了 Administrator 帳號，預設已經含有這兩個產品的訂閱，所以我們可以直接在 APIs 分頁中來測試這些 API。

![test-api-02](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/test-api-02.png)

在 API 頁面中，還很貼心的提供了包括 C#、PHP、Python、Ruby... 等許多語言的範例程式，讓您可以很快速的來從 Client 端呼叫這些 API。

![test-api-03](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/test-api-03.png)

接下來，我們點選**「Open Console」**開始測試 API 的使用。在畫面上，我們可以選擇使用特定的產品訂閱來呼叫該 API，以管理者帳戶為例，因為分別訂閱了「Starter」以及「Unlimited」兩個產品，所以可以使用選單來切換使用不同的 Subscription Key。在 API Management 服務中，這個 Key 是以名為 `subscription-key` 的 Query String 來傳送的，它用來代表一個訂閱。

![test-api-04](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/test-api-04.png)

點選**「HTTP GET」**後，Console 便幫我們送出了請求並將 response 轉成結構化的資料呈現在頁面上，不需任何工具就可以測試 API 的使用。

![test-api-05](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/test-api-05.png)

如果我們持續使用「Starter」的 Key 來發送 request 的話，還記得先前提過有每分鐘 5 次呼叫的頻率限制，可以發現很快的已經達到了呼叫上限，所以回傳了**「429 Too Many Requests」**。我們完全沒有對我們的 API 做任何修改，單純地透過 API Management 的 Dashboard 就完成了這個功能，實在是相當方便！

![test-api-06](https://raw.githubusercontent.com/hungys/azure-blog/master/media/15-azure-api-management-overview/test-api-06.png)

# 後記

API Management 是一個功能相當完整的服務，若您有將 API expose 給其他客戶的需求，透過這個平台可以很輕鬆地達成許多複雜的管理及原則控制。本文只是很粗略的帶領讀者體驗 Azure 提供的便捷服務，其實在 Dashboard 中還可以看到許多功能，例如可以客製化 Developer Portal，甚至使用 [REST API](http://msdn.microsoft.com/en-us/library/azure/dn776326.aspx) 來建立自己的後台。這項 Azure 的新服務筆者相當推薦大家可以嘗試玩玩看。

# 參考資料

- API 管理, [http://azure.microsoft.com/zh-tw/services/api-management/](http://azure.microsoft.com/zh-tw/services/api-management/)