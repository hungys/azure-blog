Day 29: 探討以開源方案在 Azure 上建置完整服務的可行性
==================================

# 前言

其實說「探討」可能太嚴肅了點，在鐵人賽結束前的倒數第二篇文章，主要是要重新 highlight 一次我們在這一系列文章中所介紹過的服務，並呼應這次的主題來檢視是否一個擁抱 open source 的開發者或開發團隊能夠將其系統部署在 Azure 雲端平台上，或是可以如何來使用 Azure 所提供的服務及平台。

# 開始紙上談兵

![Flickr](https://raw.githubusercontent.com/hungys/azure-blog/master/media/29-possibility-to-build-open-source-service-on-azure/flickr-logo.png)

由於筆者的興趣是攝影，所以在此筆者假定我們要來建立一個類似於 Flickr 這樣的照片分享平台，倒先不用考慮到應付數億用戶的規模，主要著重在分析我們可以如何來架構這個服務，並且以 open source 的 solution 為主。也因為如此，以目前業界的生態跟派別來看，我們可能就得先除去掉微軟自家的 solution 及 .NET 開發平台了，這是個不爭的事實。

# 選定程式語言

在一開始我們必須先決定好要用什麼程式語言來開發這套系統，主要可以分成網頁端及後端程式兩大部分，在網頁部分，目前很高比例的網站是使用 PHP 來開發，而使用微軟 solution 的話則可能是使用 ASP.NET Web Form 或是 ASP.NET MVC（事實上下一代的 MVC 已經完全走向開源了），最後則是許多年輕公司喜歡使用的 Python 搭配像 Django、Flask 這種 Web Framework，也有不少團隊使用 Ruby on Rails 來開發，當然也有 Node.js 的擁護者。

在此，由於本系列文章後半段主要著重在介紹如何使用 Python 來操作 Azure 上的服務，所以我們就暫且選定 Python 作為主要的開發語言。

![Python](https://raw.githubusercontent.com/hungys/azure-blog/master/media/29-possibility-to-build-open-source-service-on-azure/python-logo.png)

# 照片儲存

一個照片分享平台最重要的核心當然是照片的儲存，在 Azure 上用來儲存二進位資料的服務是**「Blob Storage」**，並且也提供了包含 Python 在內的 SDK 能夠使用，所以我們可以選擇將用戶上傳的照片保存在這個空間。

另外，為了降低回應時間，我們可以搭配使用**「Azure CDN」**將 Storage Service 的資料放置在各個 CDN 節點，如此一來使用者在請求時可以從最近的節點中取得該檔案，有利於提升網頁的讀取速度。

- Azure Storage：[http://azure.microsoft.com/zh-tw/services/storage/](http://azure.microsoft.com/zh-tw/services/storage/)
- Azure CDN：[http://azure.microsoft.com/zh-tw/services/cdn/](http://azure.microsoft.com/zh-tw/services/cdn/)
- 對應的 AWS 服務：[Amazon S3](http://aws.amazon.com/s3/)、[Amazon CloudFront](http://aws.amazon.com/cloudfront/)

# 資料庫

為了應付龐大的資料量，您可能會選擇使用 NoSQL 來當作資料庫的平台，在 Azure 上除了可以選擇**「Table Storage」**外，也可以選擇**「Azure DocumentDB」**，不過考量到相關的資源多寡，您也可以考慮比較熱門的**「MongoDB」**做為資料庫，除了在虛擬機器上建置之外，Azure 在市集上也直接與 MongoLab 合作提供了 MongoDB 的託管服務，甚至是企業級的資料庫方案。

- Azure Storage：[http://azure.microsoft.com/zh-tw/services/storage/](http://azure.microsoft.com/zh-tw/services/storage/)
- Azure DocumentDB：[http://azure.microsoft.com/zh-tw/services/documentdb/](http://azure.microsoft.com/zh-tw/services/documentdb/)
- MongoLab on Azure Marketplace：[http://azure.microsoft.com/zh-tw/marketplace/partners/mongolab/mongolab/](http://azure.microsoft.com/zh-tw/marketplace/partners/mongolab/mongolab/)
- MongoDB (Enterprise) on Azure Marketplace：[http://azure.microsoft.com/zh-tw/marketplace/partners/mongodb/mongodb/](http://azure.microsoft.com/zh-tw/marketplace/partners/mongodb/mongodb/)
- 對應的 AWS 服務：[Amazon S3](http://aws.amazon.com/s3/)、[Amazon DynamoDB](http://aws.amazon.com/dynamodb/)

# 網站平台

照片分享平台直接與使用者有接觸或互動的便是一個網站，無論您選擇使用什麼語言來實作，一般來說有兩種主要的 hosting 方式，第一種是最簡單的也就是透過**「Azure Websites」**來部署您的網站，Websites 服務本身也支援了 auto-scaling 能夠應付高流量的情形，不過即便它支援了包含 Python、Node.js 在內等許多程式語言，但對於非 .NET 的網站在設定上還是稍嫌麻煩了點。

而另一種高彈性的選擇則是**「Virtual Machines」**虛擬機器服務，這種 IaaS 服務您可以直接建立一個乾淨的作業系統，可以是 Windows 也可以是 Linux 系列，並手動在上面安裝像 nginx 這樣的靜態伺服器，若要部署 Python 所撰寫的網站的話，則可搭配 gunicorn 或 uWSGI 來做反向代理及部署，所有操作就如同在本地的 Linux 機器相同。

為了增加 request 處理的時間，在必要的情形下您也可以搭配使用**「Redis Cache」**來做快取，透過記憶體來儲存可以大幅的降低處理時間。最後則是關於流量的控制，您可以使用**「Traffic Manager」**流量管理員來進行負載平衡的控制，或是將各地區的使用者導向最適合的資料中心。

- Azure Websites：[http://azure.microsoft.com/zh-tw/services/websites/](http://azure.microsoft.com/zh-tw/services/websites/)
- Azure Virtual Machines：[http://azure.microsoft.com/zh-tw/services/virtual-machines/](http://azure.microsoft.com/zh-tw/services/virtual-machines/)
- Azure Redis Cache：[http://azure.microsoft.com/zh-tw/services/cache/](http://azure.microsoft.com/zh-tw/services/cache/)
- Azure Traffic Manager：[http://azure.microsoft.com/zh-tw/services/traffic-manager/](http://azure.microsoft.com/zh-tw/services/traffic-manager/)
- 對應的 AWS 服務：[AWS Elastic Beanstalk](http://aws.amazon.com/elasticbeanstalk/)、[Amazon EC2](http://aws.amazon.com/ec2/)、[Amazon ElastiCache](http://aws.amazon.com/elasticache/)、[AWS Elastic Load Balancing](http://aws.amazon.com/elasticloadbalancing/)


# 後端佇列

在介紹 Queue 的時候筆者曾經以照片分享平台為例，因為以 Flickr 來說，在使用者上傳完照片之後還會縮圖成好幾種大小來存放，但縮圖這種動作基本上是比較耗時的，所以並不適合放在 HTTP request 中一起來處理，較適合的方式是在接收到新照片後將它送進 Queue 中，再由一個背景運作的程式來處理佇列中的請求。在 Azure 上您可以選擇使用叫基本的**「Queue Storage」**或是基礎架構較為完整的**「Service Bus Queues」**來實作。這兩種服務都分別提供了包含對 Python 的支援，並且 SDK 皆開源在 GitHub 上。

- Azure Storage：[http://azure.microsoft.com/zh-tw/services/storage/](http://azure.microsoft.com/zh-tw/services/storage/)
- Azure Service Bus：[http://azure.microsoft.com/zh-tw/services/service-bus/](http://azure.microsoft.com/zh-tw/services/service-bus/)
- 對應的 AWS 服務：[Amazon SQS](http://aws.amazon.com/sqs/)

# 公開 API

大部份的大型服務都有提供第三方開發者申請 API 來進行服務的加值，關於對外 API 的管理您可以自己來實作，但若要包含更進階的控制例如 API 使用者的權限、用量限制或是呼叫頻率等等，要實作起來又是一個相當龐大的專案，Azure 提供了**「API Management」**這個服務，作為一個 API Proxy 可以讓您很輕鬆的來達到這些需求，並且也提供了相當方便的 Portal 可以讓您或是第三方開發者來取用或測試您所要 expose 的 REST API。

- Azure API Management：[http://azure.microsoft.com/zh-tw/services/api-management/](http://azure.microsoft.com/zh-tw/services/api-management/)

# 推播通知

若您的服務還有打算建立 Mobile App 給使用者來使用，那可能有時還會有 Push Notification 的需求，例如照片回應的通知，或是追蹤對象上傳了照片等等，考慮到要支援跨平台的推播，包含了 Android 的 GCM、iOS 的 APNS 以及 Windows 平台的 MPNS、WNS 等等，您可以選擇使用**「Notification Hubs」**來幫您處理這些大量的推播通知，免去建立可靠的分散式平台的人力及時間成本。

- Azure Notification Hubs：[http://azure.microsoft.com/zh-tw/services/notification-hubs/](http://azure.microsoft.com/zh-tw/services/notification-hubs/)
- 對應的 AWS 服務：[Amazon SNS](http://aws.amazon.com/sns/)

# 結論

從上文簡單的分析來看，基本上大多數的需求在 Azure 上都能夠實現，並且也可以使用非 .NET 的語言來實作，並且可以用上許多 open source 的工具例如 MongoDB 或 Redis。除了上面提到的服務之外，若您有更多分析的需求，在 Azure 上也提供了 Hadoop（即 Azure HDInsight）及 Azure Machine Learning 等服務，您可以在上面進行更進一步的資料分析。總體來說，比起過去的 Windows Azure，今天的 **Microsoft Azure** 已經提供了相當友善的支援讓您可以使用開源的方案在上面建構服務了！