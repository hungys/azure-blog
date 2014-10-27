Day 27: 使用 Azure Notification Hubs 建立面向數百萬裝置的推播服務
===================================

# 前言

在上一篇文章中，我們與讀者介紹了 Azure Mobile Services 這個提供全面性 App 後端的服務，而其中它支援了多平台的推播通知，然而，某些情形之下，這樣的服務可能還無法滿足需求。設想今天您的用戶數已經突破數百萬甚至上千萬，當需要將某則訊息廣播至所有裝置時，若一次 reuqest 只傳送給一個終端裝置的話，代表得要花上數百數千萬的 reuqest 才能完成，想必會相當耗時。

# 那就來自幹吧！

這時熱血的開發者一定會跳出來說，那我們就來自幹一個推播系統吧！如果要應付這樣的需求，首先要解決的便是盡可能地在單次 request 中將訊息遞送給更多的終端用戶來達到減少總 reuqest 數的目標，關於這點每個平台會有不同的作法及策略：

- Android：根據 Google 官方在 GCM [說明文件](http://developer.android.com/training/cloudsync/gcm.html)上「Send Multicast Messages Efficiently」這個主題的說明，每次向 GCM 的請求中，`registration_ids` 這個陣列最多可以填入 1000 個終端裝置的註冊 ID，如此一來便可以將 request 數量降低為原本的 1/1000，即便時間不會是線性的節省，但勢必對整體時間的減少有所助益。
- iOS：根據 Apple 官方在 APNS [說明文件](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/CommunicatingWIthAPS.html)中的敘述，若要透過 APNS 傳送推播必須建立一條 TCP streaming socket 來傳送 binary data，並將多個通知包在同一個 Frame 來傳送，也能很快速的來傳送推播訊息。
- Windows Phone：WP 平台的話就比較抱歉了，目前官方「公開」的文件指出，因為每一個裝置的推播端點都是一個 URL，所以您要向某個裝置推送訊息的話便要直接對該 URL 送 request，如此一來便沒有在單一 request 發送給多個接收者的可能性，這點相當不方便。然而不確定 Notification Hubs 本身是如何實作這一塊的，但畢竟是微軟自家的服務，或許有一些沒有明文記載的招數也說不定。

然而，前面所提到的減少 request 次數只是最基本的一步，即便將 request 數量降低至數十萬，要在推送完還是得花上不少時間，所以必須設計好一個堅固的分散式系統來處理這些請求，然而，您可能並非隨時都在大量推送通知，於是便可能會產生運算性能的閒置。

最後一個問題則是錯誤處理，並不是每個通知都能夠成功的發送至裝置端，可能會因為任何因素而發送失敗，甚至是該裝置的 token 已經失效，這部分的處理也必須自己來。綜合以上，除非您的團隊有相當充足的開發人力來處理這些需求，否則使用現有的大量推播服務可能還是比較好的選擇。除了本文要介紹的 Notification Hubs 之外，您亦可以參考如 Parse、Urban Airship 這類的服務，與其他服務相比，Notification Hubs 注重的是建立一個彈性的基礎架構，所以並不像其他服務有提供較為完整的後端 console 能夠操作。


# 什麼是 Notification Hubs?

Azure Notification Hubs 主要專注在群組廣播的推播服務上，讓您可以在數秒、數分鐘之內就把一則訊息推送至上百萬的終端裝置。此外，微軟支援了 GCM、APNS 以及自家的 WNS、MPNS 服務，讓您可以透過單一服務來處理各個平台的裝置，甚至在最近一次更新中增加了對百度推播系統的支援。

若您使用了 Notification Hubs，所有與 GCM、APNS、WNS... 等推播系統之間的溝通都可以交給 Azure 來處理，您所需要做的事情只是在 Client 端增加向 Notification Hubs 註冊的請求，並且之後要傳送推播皆透過 Notification Hubs 所提供的端點，剩餘的實作都由微軟官方所負責處理，當然甚至包含了推播過程中所產生的錯誤。

![Concept](concept.png)

整體來說，Notification Hubs 具有以下幾點特色及功能：

- **多平台支援**：目前已經支援了 Google Android、iOS 及 Windows Phone 自身的推播服務，而針對沒有 Google Play Service 的裝置，也增加了對百度推播的支援。使用 Notification Hubs 後，您便可以透過單一的端點來推送訊息至各種不同的平台。
- **註冊管理**：所有要透過 Notification Hubs 推送的裝置皆須向服務註冊，相關的實作您也不需要擔心，只需要透過單一 API 便能夠完成註冊的程序。若您選擇在 Server 端來處理這些程序的話，在 Client 端的程式碼甚至完全不需要與 Notification Hubs 有任何互動。
- **多語言支援**：您可以使用 .NET、PHP、Java、Node.js 來建構您的服務，當然也提供了 REST API 能夠直接使用，不過目前 .NET SDK 所提供的支援較為完整。
- **條件式推播**：在 Notification Hubs 中，每個裝置在註冊時都會註冊有 tag，它可以是多個，而您在推送通知時，只需使用 tag 來指定便能快速推送訊息至「某群人」的裝置上。
- **高度彈性**：您可以建立訊息通知的樣板，並指定如何轉換成各平台專屬的 payload 格式，如此一來只需要使用單一的格式便能將訊息發佈至多個平台，更可以用來發布多語言的推播通知。

# 裝置註冊管理

在 Notification Hubs 中，每個終端裝置皆需要向 Azure 註冊，而註冊主要分為兩種實作方式，第一種是直接使用官方提供的 SDK 將註冊程序置入在您的 App 中，該 SDK 會直接透過 REST API 向 Notification Hubs 註冊該裝置如圖所示：

![Register from device](register-from-device.png)

而另一種方法則是將註冊的程序寫在您自己的 Server 後端中，如此一來 App 中僅需透過您自己的 API 端點註冊即可，完全不需與 Notification Hubs 互動。而這種做法最大的好處在於裝置的 tag 是從 Server 端註冊的，所以您可以在使用者沒有打開 App 時來修改他的 tag：

![Register from backend](register-from-backend.png)

# 標籤與路由

![Tags](tags.png)

每個註冊的裝置都可以擁有各自的 tag，而且可以包含兩個以上的 tag，舉個例來說，您可以為 A 球隊的支持者們註冊一個 teamA 的標籤，B 球隊的支持者們則註冊 teamB，屆時若要推送關於 A 球隊的最新比數，直接向 teamA 這個 tag 推送即可。這個 tag 可以是任意長度小於 120 的字串，您甚至可以為每個使用者 ID 皆註冊一個 tag，如此一來便可以達到針對單一使用者推播的需求，如下圖：

![Target by tag](target-by-tag.png)

除了單一 tag 之外，您也可以使用「Tag expression」來表達所要推送的對象，它可以包含如「&&」、「||」、「!」的邏輯，以下圖為例，您可以使用 `(follows_RedSox || follows_Cardinals) && location_Boston` 這個 expression 來表達「居住在波士頓，並且追蹤紅襪或紅雀」這群使用者：

![Target by tag expression](target-by-tag-expression.png)

標籤的使用可以相當彈性，以筆者的使用方法為例，舉凡 `userid:<USER ID>`、`version:<VERSION>`、`installAt:<INSTALL DATE>` ...等等的 tag 在發送推播時都相當實用。

然而，這項功能也有所限制，如果您只使用了「||」這個 OR 運算子，則最多可以在單一 expression 中包含 20 個 tag，除了這個 case 之外，混用 AND、OR、NOT 的情況下最多只能包含 6 個不同的 tag，這點必須留意。

# 樣板

樣板這個功能主要能解決兩個目的，其中第一個是剛剛有提到過的使用單一 payload 格式來對應多種平台，以下圖為例，Windows Phone 裝置及 Android 裝置分別對樣板做了註冊，其中包含了「message」這個共同參數，未來您在推播時，僅需要給定該參數的值即可對有註冊該樣板的用戶推送：

![Template for cross-platform](template-for-xplatform.png)

另一種使用方式則是個人化或本地化的通知，以下圖的例子來說，某些裝置註冊了「華氏」的樣板，某些裝置則註冊了「攝氏」的樣板，屆時您只需要在 payload 中給定「tempF」及「tempC」參數的值，便可自動對應到這兩種樣板，您也可以使用在多國語系通知的實作上：

![Template for personalization](template-for-personalization.png)

關於樣板語法的詳細說明可以參考：[http://msdn.microsoft.com/en-us/library/azure/dn530748.aspx](http://msdn.microsoft.com/en-us/library/azure/dn530748.aspx)

# 排程通知

在付費方案中，您可以設定排程通知，他會回傳一個 scheduled notification 的 ID，您必須自行儲存這組 ID，未來若要取消排程時會使用到，目前 Management Portal 上也沒有相關的介面能夠管理排程通知，您只能透過 REST API 來存取。

更多關於 API 的細節，請參考：[http://msdn.microsoft.com/en-us/library/azure/dn495101.aspx](http://msdn.microsoft.com/en-us/library/azure/dn495101.aspx)

# 多平台支援

目前 Notification Hubs 幾乎支援所有主流平台，並且在官方網站上提供了相對應的文件可以參考：

- [Windows Universal](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/)
- [Windows Phone](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-windows-phone-get-started/)
- [iOS](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-ios-get-started/)
- [Android](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-android-get-started/)
- [Kindle](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-kindle-get-started/)
- [Baidu](http://azure.microsoft.com/en-us/documentation/articles/notification-hubs-baidu-get-started/)
- [Xamarin.iOS](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/)
- [Xamarin.Android](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/)

其中，Windows Phone 比較特別的是本身具有 MPNS 及較新的 WNS 兩種推播平台，目前官方的 Notification Hubs SDK for Windows Phone Silverlight 僅支援 MPNS，所以若您的 App 是 Windows Phone Silverlight 8.1 的專案，而且想要使用較新的 WNS 服務，則不能直接使用官方提供的 SDK，必須透過 REST API 來操作。而筆者為了專案上的需求，所以寫了一個非官方的 SDK 來支援 WNS，但目前僅支援基本功能，並不包括「範本註冊」的支援，相關原始碼皆可在 GitHub 上「[Azure Notification Hubs SDK for Silverlight 8.1 using WNS](https://github.com/hungys/azure-notificationhubs-wns-for-silverlight81)」的專案中找到，歡迎 fork 它並完善它！目前筆者有在實際的專案中使用這個 SDK，相信對於基本的推播需求是能夠正常來使用的！

# 收費機制

Azure 官方在今年九月份修改了這個服務的收費機制，這點必須要特別注意，因為有用量上及功能上的差別。目前主要分為**「免費」**、**「基本」**、**「標準」**三種層級，它們的差異可摘要如下：

- 免費：包含單月**一百萬次**的推播量，但不可再付費增加，不限制活躍裝置數，但**最多僅可建立 3000 個不同的 tag，並且每次推播時最多只能推向 10000 個裝置**，若超過的話則會使用隨機的方法推播。
- 基本：**每月 NT$311** 包含**一千萬次**的推播量，但您可以付費來增加這個限額，與免費方案的最搭差別在於支援了 auto-scaling。
- 標準：**每月 NT$6206** 包含**一千萬次**的推播量，對於**標籤數量及單次廣播對象數量皆無上限**，並且支援排成推播，可以查詢註冊裝置...等功能，詳細差異可以參考[官方網站](http://azure.microsoft.com/zh-tw/pricing/details/notification-hubs/)。

# 後記

本文簡單的介紹了 Notification Hubs 這項服務，筆者認為這是一個非常值得嘗試的服務，而且免費方案提供了滿充足的基本推播量。無奈比較可惜的是，最新的收費機制雖然對免費方案提供了更龐大的推播量，但相對的卻拔除了一些進階功能如排程推播、註冊查詢的功能，並且限制了單次廣播的裝置數上線，但低價方案與標準方案之間的價差甚大，希望官方未來能增加更多彈性的資費方案。

# 參考資料

- Notification Hubs | Windows Azure Technical Documentation Library, [http://msdn.microsoft.com/en-us/library/azure/jj891130.aspx](http://msdn.microsoft.com/en-us/library/azure/jj891130.aspx)
- Pricing Details - Notification Hubs | Microsoft Azure, [http://azure.microsoft.com/zh-tw/pricing/details/notification-hubs/](http://azure.microsoft.com/zh-tw/pricing/details/notification-hubs/)