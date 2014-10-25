Day 26: 使用 Azure Mobile Services 快速建立 App 後端服務
==================================

# 前言

以目前的 App 發展趨勢來說，一般而言除了 Cient 端的程式以外一定也需要後端 Backend 的配合才能建構起一個完整的服務。然而，從最基本的資料庫存取，到使用者驗證的邏輯，甚至是推播通知的系統，若要從無到有打造勢必得花費許多人力與時間成本。對於一個希望快速建立 prototype 或是輕量化服務的團隊，使用 Azure Mobile Services 便再適合不過了，僅需要在幾個步驟之間就可以建立好一個 App 後端，並且使用精簡的程式碼來存取這些服務。

# 什麼是 Mobile Services?

![Concept](https://raw.githubusercontent.com/hungys/azure-blog/master/media/26-azure-mobile-services-overview/concept.png)

若讀者對 App 開發有一定的認識的話，應該多多少少有聽過「[Parse](http://www.parse.com)」這個服務，它除了提供了 Parse Push 能夠讓您快速的發送推播通知到各平台外，透過 Parse Core 也能夠快速建立起一個後端的資料庫，並提供了簡單的 API 可以在 Client 端來存取資料。而 Azure Mobile Services 便是微軟基於 Azure 上 PaaS 服務所建立的一種 Backend-as-a-Service，同樣提供了**「資料存取」**、**「身份驗證」**、**「推播通知」**三大項目的支援，而且也面向 Android、iOS、Windows Phone、Windows、HTML 甚至 Xamarin 提供了 SDK 讓您能夠快速開發。

# 跨平台支援

微軟在 Azure 上打造了一整套的 Backend solution，當然不單單只支援自家的 Windows Phone 平台，目前 Mobile Services 已經針對以下行動平台提供了直接的支援：

- iOS
- Windows Phone
- Windows Store using C#
- Windows Store using JavaScript
- Xamarin iOS
- Xamarin Android
- Android
- HTML
- PhoneGap

針對各個平台的相關文件都能夠在官方網站上查詢到，若沒有您所需要的 SDK，您依舊可以透過 [REST API](http://msdn.microsoft.com/library/azure/jj710108.aspx) 來存取相關服務。

# 資料

資料是大部份 App 最基本的基礎，每個 Mobile Services 都配有免費 20MB 的 MSSQL 資料庫，當然您也可以建立一個付費的資料庫來供 Mobile Services 使用。而在沒有行動服務時，您必須要自己實作 REST API 或任何 Web Service 來提供一個端點讓 Client 來呼叫，但有了 Mobile Services，這些 API 都由 Azure 後端所提供，更能夠直接使用各平台的 SDK 來直接存取。

假如您有對於 API 需要更客製化的需求，您也可以直接在 Management Portal 來自定這些 REST API 的指令碼，目前 Azure 支援使用 .NET 或 JavaScript 來撰寫這些後端指令碼，並且提供直接與推播通知系統整合的服務，甚至是整合其他第三方服務例如 Email 寄送及簡訊發送等等。而針對 App 的離線使用行為，SDK 也實作了本地與遠端資料同步的功能，讓您透過簡單的一個 API call，將本地端離線情況下的資料庫變動與遠端同步，同時從伺服器抓取最新的資料庫版本。

除了 MSSQL 之外，您也可以整合 Azure 的 Table Storage 及 Blob Storage，甚至選擇使用 MongoDB 作為後端的資料庫。

# 使用者驗證

![Identity](https://raw.githubusercontent.com/hungys/azure-blog/master/media/26-azure-mobile-services-overview/identity.png)

若您打造的一個 App 本身是一個「服務」，那麼必定會有使用者驗證的設計存在，而近年來受到社群網路發展的影響，許多服務紛紛開始整合 Social Account 的整合，例如直接使用 Facebook 來登入，這些相關的實作 Mobile Services 皆已經提供了直接的 SDK 支援，Facebook、Twitter、Microsoft 或 Google 帳戶都能夠使用相關 API 來實作，不需要再套用各平台的 SDK 或是實作 OAuth 驗證流程。而針對企業客戶，微軟現在也提供了與 Active Directory 整合的支援，讓您可以直接與現有的認證平台整合。

當然，為了提供更高的彈性，您同樣可以在 Management Portal 中編輯相關的指令碼，讓您達到更客製化的需求，例如在註冊完成時寄發驗證信件等等。

# 推播通知

![Push](https://raw.githubusercontent.com/hungys/azure-blog/master/media/26-azure-mobile-services-overview/push.png)

若您建立的 App 是屬於社交平台服務，或是新聞資訊提供的 App，必定少不了透過 Push 來通知 user 的功能，然而，Google 擁有自己的 GCM 服務、Apple 則是 APNS、微軟自家也有 MPNS 與 WNS 這兩種推播系統，若您要自己在後端實作這些 Push，勢必得花上好一番功夫。Azure Mobile Services 目前提供了對 GCM、APNS、MPNS、WNS 的推播支援，您只需要在 Management Portal 上設定好相關的 token 及 key，便可以直接在後端指令碼使用 `push.apns|gcm|mpns|wns.send{payload}` 簡單的 API 來傳送推播通知至任何平台的終端裝置。

對於要在數秒鐘內推送至數十萬、百萬計的裝置的情境，微軟也建構了另一個 Notification Hubs 服務，並且能夠與 Mobile Services 直接整合，相關服務細節筆者將在下一篇文章中做更深入介紹。

# 後端指令碼

![Script](https://raw.githubusercontent.com/hungys/azure-blog/master/media/26-azure-mobile-services-overview/script.png)

針對前文所提到的服務，為了提供更客製化的支援，您皆可在 Management Portal 中來編輯相關 API 的指令碼，在 Portal 上支援使用 JavaScript 來撰寫伺服器指令碼（即 Node.js），除了基本的 CRUD 新增刪修之外，也可以使用 push API 快速與推播服務整合，也可以使用第三方套件如 SendGrid 來寄送電子郵件。

![Custom API](https://raw.githubusercontent.com/hungys/azure-blog/master/media/26-azure-mobile-services-overview/custom_api.png)

此外，您也可以在 Portal 上建立自己的 API 端點來提供服務，並且能夠指定各個 API 是否需要帳戶驗證才能被呼叫。而對於一些需要被排程的指令碼，您也可以建立 Scheduled Job 來處理這些需求。

# 現在就開始

Azure Mobile Services 提供了相當完整的 App 後端即服務，若您欲建立一個中小型的應用程式或服務，或是希望能快速建立 prototype，行動服務絕對是您建立跨平台 App 的最佳選擇。對於各個平台的實作微軟官方皆提供了豐富的教學資源，請參閱：[http://azure.microsoft.com/zh-tw/documentation/services/mobile-services/](http://azure.microsoft.com/zh-tw/documentation/services/mobile-services/)

# 參考資料

- Azure 行動服務, [http://azure.microsoft.com/zh-tw/services/mobile-services/](http://azure.microsoft.com/zh-tw/services/mobile-services/)