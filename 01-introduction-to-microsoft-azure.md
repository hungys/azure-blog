Day 1: 認識 Microsoft Azure
==========================

# 前言

這是筆者第一次參加鐵人賽，正好看到今年的推廣主題其中之一是由 Microsoft 所提出的 Open Source on Azure，由於筆者一直以來非常熱衷於微軟的 solution，但同時也非常關注 open source 的相關專案，正巧可以利用這次的機會，希望在這 30 天的時間內可以讓更多人認識近年來微軟在 Azure 上所做的轉變，以及對於 open source solution 日益健全的支援。

這一系列文章將著重在從 open source 角度及非微軟自家技術的角度來介紹 Microsoft Azure，前半段主要著重在 IaaS 與 PaaS 服務的架設及使用，後半段則會著重在使用 open source SDK 來操作 Azure 所提供的 PaaS 服務，這部分將會以筆者較熟悉的 Python 為例，當然您也可以在 Azure 官網找到其他語言如 node.js 對應的學習資源。這 30 天暫定的內容主題如下：

- 認識 Microsoft Azure
- 當 Azure 擁抱開放
- 在 Azure 上快速架設開源 CMS 專案
- 虛擬機器、雲端服務、網站服務比較
- 使用跨平台 CLI 管理 Azure (1)
- 使用跨平台 CLI 管理 Azure (2)
- 在 Azure 上使用 Git 分散式版本控制
- 使用 Dropbox 發佈專案至 Azure Web Sites
- 在 Azure 上架設 Linux VM 及 LAMP 服務
- 在 Azure VM 上架設 nginx、mongodb 服務
- 在 Azure VM 上使用 gunicorn 部署 Flask 網站
- 在 Azure 上快速建置 MongoDB 託管服務
- 在 Azure 上快速建置 Redis Cache 服務
- 在 Azure 上使用內容傳遞網路 (CDN)
- Azure API Management 服務簡介
- 在 Azure VM 上安裝 IPython Notebook
- 在 Azure Web Sites 上設定 Python 執行環境
- 部署 Django 網站 - 以 VM 及 Azure Web Sites 為例
- 使用 Azure SDK for Python 管理 Azure 服務
- 使用 Python 操作 Azure Blob Service
- 使用 Python 操作 Azure Table Service
- 使用 Python 操作 Azure Queue Storage Service
- 使用 Python 操作 Azure Service Bus Queues
- 使用 Python 操作 Service Bus Topics/Subscriptions
- 使用 Python 操作 Azure DocumentDB
- 使用 Azure Mobile Services 快速建立 App 後端服務
- 使用 Azure Notification Hubs 建立面向數百萬裝置的推播服務
- 在 Azure 上使用 Docker
- 探討以開源方案在 Azure 上建置完整服務的可行性
- Azure 學習資源


# 淺談雲端運算

近年來「雲端」一詞相當火紅，但絕大多數的人對於「雲端」的概念停留在只要可以連上網或是將服務或網站放在伺服器上就是使用到了雲端運算的技術。然而，真正雲端運算 (Cloud Computing) 的本質應該是建立在虛擬化 (Virtualization)、高延展性 (Scalability)、用多少付多少 (Pay-As-You-Go) 的概念。

藉由虛擬化的技術，主機不一定再需要一台台堆放在機架上，而是以某種形式存在於一個廣大的資源池 (Resource Pool) 中，而受益於高度延展的特性，我們以一個線上售票服務為例，你不再需要為了極短時間的搶票區間預備好大量的實體主機在機房中，只需要透過 Auto-Scaling 的技術，在特定高用量的時間區間啟用較多的運算資源及實體，如此一來便可以大幅降低主機閒置時間的運算成本，這才是雲端運算的真正價值與意義。

# Azure 簡介

[Microsoft Azure](http://azure.microsoft.com) 是微軟所打早的一個公有雲服務平台，過去稱為 Windows Azure。Azure 一開始與其競爭對手 AWS 相同，是以 IaaS 作為切入點提供基礎建構雲服務，時至今日，Azure 已經提供了橫跨 IaaS 到 PaaS 甚至 SaaS 豐富的雲服務，您可以使用各種方式來使用這個雲端平台來建置服務。例如，您可以將一個 Web 應用程式部署至 Azure 的網站服務，將資料庫建置在 Azure 所提供的 SQL Database 中，並利用 Blob 服務來存取二進位資料，甚至也可以在數分鐘之內直接在 Azure 上建立一台全新的虛擬機器，透過 IaaS 服務來建立俱有高延展性、高度彈性的平台。

# Azure 的組成

下面這張圖以服務的特性及目的將 Azure 上所提供的所有服務分門別類，我們可以很清楚看到 Azure 從最一開始單純的服務到今天，已經是一個提供橫跨 IaaS、PaaS 到 SaaS 的完整雲端平台，在接下來的內容中，筆者將簡單介紹 Azure 中的各項服務。

![The components of Azure](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/azure-components.png)

綜觀來說，Azure 的服務可以分為以下七大類：

* 計算與網路
* 網路與行動
* 資料與分析
* 儲存體與備份
* 媒體與 CDN
* 混合式整合
* 身份識別與存取管理

若您有興趣的話，微軟也將 Azure 所有的服務繪製成一張[巨幅海報](http://azure.microsoft.com/zh-tw/documentation/infographics/azure/)，您可以自行下載該 PDF 檔輸出，或是親自參與台灣微軟所舉辦的研討會，也有機會拿到繁體中文版的海報，筆者在先前的 TechDays 2014 便拿到了一張。

# 運算服務

執行應用程式是在雲端平台中最重要也是最基本的一項作業，在 Azure 中依照服務的彈性程度不同提供了三種主要的選擇：虛擬機器 (Virtual Machines)、雲端服務 (Cloud Services) 以及網站服務 (Websites)。

## 虛擬機器 (Virtual Machines)

![Virtual Machines](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/virtual-machines.png)

虛擬機器 ([Virtual Machines](http://azure.microsoft.com/zh-tw/services/virtual-machines/)) 是一種 IaaS 服務，提供了最高度彈性的服務，您可以透過 Azure Gallery 組件庫使用預先建立好的作業系統映像檔，其中包括了各個版本的 Windows Server 甚至是 Ubuntu、CentOS 等開源的作業系統環境，或是您也可以自行上傳預先準備好的 VHD 檔，將原本的本地環境部署至虛擬機器服務。在虛擬機器服務中，您擁有了 100% 的主控權，可以透過 SSH 或是遠端桌面連線的方式管理您的伺服器。其中很特別的是，微軟也預先準備好了許多預載好像 SQL Server 或 Visual Studio 的映像檔，您可以在數分鐘之內就部署好一台裝有 Visual Studio 14 CTP 的開發測試環境。

## 網站服務 (Websites)

![Websites](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/websites.png)

網站服務 ([Websites](http://azure.microsoft.com/zh-tw/services/websites/)) 是一種最容易部署網站的服務，他是建構在微軟自家的 IIS 服務之上，除了過去熟悉的 ASP.NET 之外，也同時提供了對 PHP、Python 及 node.js 等語言的支援，讓您可以在數分鐘之內就將一個網站應用程式部署至雲端。同時，網站服務也提供了高度延展的設定，您可以依照需求選擇不同大小/價位的服務，並且可以依照流量及 CPU 運算資源做 auto-scaling。最重要的是，每個 Azure 帳戶擁有 10 個免費 (Free) 量級的網站服務，您可以不需花費任何金錢就將輕量級的網站服務部署在雲端資料中心。

## 雲端服務 (Cloud Services)

![Cloud Services](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/cloud-services.png)

雲端服務 ([Cloud Services](http://azure.microsoft.com/zh-tw/services/cloud-services/)) 是一種介於前兩者之間的 PaaS 服務，它提供了比網站服務更高的彈性，但其背後的虛擬機器是由微軟資料中心所代管，您可以專注在您的應用程式及服務本身。而依照應用程式不同的需求，雲端服務提供了兩種運轉模式，分別是 Worker Role 及 Web Role，並且也提供了對 .NET 以外程式語言的支援。

關於以上三種服務的比較以及選擇，在往後會有專文做更深入的分析及介紹。

# 資料管理

大部份的應用程式都需要存取資料，您除了可以在 IaaS 服務上自行建立資料庫的儲存環境外，在 Azure 中也依照不同的需求提供了幾種主要的選擇：SQL Database、資料表 (Table) 以及 Blob。

## SQL Database

![SQL Database](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/sql-database.png)

[SQL Database](http://azure.microsoft.com/zh-tw/services/sql-database/) (過去稱為 SQL Azure) 是一個針對雲端環境優化的 SQL Server 服務，提供了關聯式資料庫的所有重要功能，而且如同過去熟悉的 SQL Server，您可以使用 Entity Framework、ADO.NET 或是其他熟悉的資料存取技術來存取 SQL Databse。如果您過去的服務是建立在 SQL Server 上，SQL Database 會是一個您很好的雲端化選擇，透過 SQL Management Studio 就可以輕鬆的將資料放上雲端。

## 資料表 (Table)

![Table](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/table-storage.png)

資料表 ([Table](http://azure.microsoft.com/zh-tw/services/storage/)) 是一種提供大量儲存 key/value 型式的 NoSQL 服務，它不提供關聯式資料庫的功能，但如果您所要存的資料量相當龐大，或是不需要對這些資料執行複雜的 SQL 查詢，那麼它會是一個簡單明瞭而且成本遠低於 SQL Database 的選擇。

## Blob

![Blob](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/blob-storage.png)

[Blob](http://azure.microsoft.com/zh-tw/services/storage/) 是設計用來儲存非結構化二進位資料的服務，而且單一個 Blob 就有多達 1 TB 的容量，適合用來儲存視訊或備份資料等等，您可以使用 Blob 作為簡單而且成本低廉的儲存體服務。

# 網路

Azure 目前在亞洲、歐洲及美洲數個資料中心內運行，您除了可以在 Azure 上部署您的雲端應用程式之外，也可以用來作為本地資料中心或網路的延伸，透過虛擬網路 (Virtual Network) 以及流量管理員 (Traffic Manager) 的服務來達成。

## 虛擬網路 (Virtual Network)

![Virtual Network](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/virtual-network.png)

透過虛擬網路 ([Virtual Network](http://azure.microsoft.com/zh-tw/services/virtual-network/)) 您可以將 Azure 當作您私有網路的延伸，在上面依需求建立虛擬機器來使用，透過 VPN 閘道裝置來串接您的私有網路以及雲端，建立一個私有雲 (Private Cloud)。例如您只需要將您所擁有的 IPv4 地址指派給雲端的虛擬機器，公司的員工便可以像存取本地端服務來取用這些部署在雲端上的應用程式。

## 流量管理員 (Traffic Manager)

![Traffic Manager](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/traffic-manager.png)

如果您的服務的使用者遍佈全球，並且將服務部署在多個國家的資料中心中運行，為了減少 latency，您會希望使用者可以自動存取到距離較近的資料中心，但若該資料中心的執行個體已經過載，這時候便應該要求導向另一座資料中心，這就是流量管理員 ([Traffic Manager](http://azure.microsoft.com/zh-tw/services/traffic-manager/)) 所可以提供的服務，您可以自行訂定規則，來決定如何將使用者導向到最適合的資料中心。

# 行動裝置

過去撰寫行動應用程式時，您可能會需要使用到資料存取、身份驗證、推播通知等功能，往往需要自行建構 API 來提供行動裝置端呼叫各項服務。而在 Azure 上，針對 Mobile App 的各種基本需求，提供了包括各式基本服務的行動服務 (Mobile Services) 以及可以大量將訊息推送至用戶端的通知中樞 (Notification Hubs)，大幅降低了開發 App 後端所需的時間，而且透過單一的服務就可以提供 Android、iOS 以及 Windows Phone 各個平台所需的服務。

## 行動服務 (Mobile Services)

![Mobile Services](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/mobile-services.png)

在行動服務 ([Mobile Services](http://azure.microsoft.com/zh-tw/services/mobile-services/)) 中，微軟提供了針對 Mobile App 最基本的三種需求，首先除了提供 SQL Database 的資料儲存空間，更直接提供了對應的 API 接口讓 Client 端可以快速來取用資料庫的資料。另外，現今的 App 經常會整合各式社交服務的帳號，過去若要自行建立這樣的帳號綁定服務，需要撰寫相當多的 OAuth 驗證或是帳號 link & unlink 的邏輯，但透過行動服務，您可以輕鬆將您的 App 與 Facebook、Twitter 甚至是 Google 服務整合，現在更提供了與企業 Active Directory 整合的支援。而在推播服務方面，微軟也提供了 GCM (Google Cloud Messaging)、APNS (Apple Notification Service)、MPNS (Microsoft Phone Notification Service) 及 WNS (Windows Notification Service) 的整合，讓您可以透過單一接口將訊息推送至各個不同的平台，省下了非常多後端開發的時間。

行動服務目前針對 iOS、Android 以及 Windows Phone 皆有提供 SDK 可以使用，而且官方也將 SDK 開源在 GitHub 上，您幾乎可以在短短幾行程式碼內就將 App 接上 Azure Mobile Services。

## 通知中樞 (Notification Hubs)

![Notification Hubs](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/notification-hubs.png)

前面的行動服務雖然提到提供了各平台推播通知的整合，但如果今天的需求是在短時間內大量廣播至各個平台的裝置，例如您需要在數秒內要完成上百萬條訊息的推送，這時候便需要完整的分散式服務架構。相較於知名的 BaaS (Backend-as-a-Service) 服務 Parse，通知中樞 ([Notification Hubs](http://azure.microsoft.com/zh-tw/services/notification-hubs/)) 提供了相當完整的基礎服務以及 API，讓您可以在這個架構之下建立一個快速大量推播的平台，而且可以為您省下整合各平台推播服務的時間。

# 訊息服務

## 佇列 (Queues)

![Queues](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/queues.png)

佇列 ([Queues](http://azure.microsoft.com/zh-tw/services/storage/)) 是一種 FIFO (First-In-First-Out) 的設計概念，一個應用程式將訊息放入佇列中，而另一個應用程式來讀取該訊息並進行進一步的處理。舉一個簡單的例子，目前最熱門的相簿服務 Flickr 在使用者將照片上傳後，會自動將照片做各種尺寸的縮圖，這種服務變可以透過佇列來達成，在網頁應用程式接收到上傳的照片後，便將該照片資訊放入佇列當中，而在背景工作的另一個角色便不停的從佇列中讀取新上傳的照片，並進行一些需要較長時間的縮圖處理。

## 服務匯流排 (Service Bus)

![Service Bus](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/service-bus.png)

服務匯流排 ([Service Bus](http://azure.microsoft.com/zh-tw/services/service-bus/)) 與上面所提到的佇列不同的地方在於，服務匯流排的目的是讓應用程式在任何地方都能交換資料。除了佇列所能提供的一對一通訊之外，服務匯流排還提供了發佈與訂閱 (pub/sub) 的機制，應用程式可以將訊息傳送到某個主題，而有訂閱該主題的多位收件者可以同時讀取相同訊息，達成一對多的通訊。此外，服務匯流排也提供了轉送 (Relay) 的機制，提供通過防火牆的安全通訊方式。

# 快取

應用程式可能會一再存取相同的資料，若要提升服務的效能，最直覺的做法就是將大量被取用的資料就近保留一份，這就是快取的概念。Azure 提供了兩種不同的快取架構，分別是針對應用程式記憶體的內部快取以及針對 Blob 資料的內容傳遞網路 (CDN)。

## Azure 快取 (Azure Caching)

![Cache](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/azure-cache.png)

Azure 快取 ([Azure Caching](http://azure.microsoft.com/zh-tw/services/cache/)) 的基本概念便是將大量被取用的內容或資料儲存於記憶體內部，如此一來便可以獲得比資料庫存取或是硬碟存取更高的效能。值得注意的是，Azure 現在也支援了開源專案 redis cache。

## 內容傳遞網路 (CDN)

![CDN](https://raw.githubusercontent.com/hungys/azure-blog/master/media/01-introduction-to-microsoft-azure/cdn.png)

[CDN](http://azure.microsoft.com/zh-tw/services/cdn/) 在全球各地有許多網站，當某個地區的使用者第一次存取特定 Blob 物件時，其內容會從 Azure 資料中心複製到該地區中的 CDN 儲存體，在此之後，來自該地區的 request 將會使用在 CDN 中快取的 Blob 複本，而不需直接連接最近的 Azure 資料中心。

# 其他服務

前文已經介紹了許多 Azure 所提供的雲端服務，橫跨了 IaaS 到 PaaS，但其實 Azure 還提供了更多服務我們還沒有介紹。以資料分析為例，[HDInsight](http://azure.microsoft.com/zh-tw/services/hdinsight/) 便是一個建立在 Azure 上的 Hadoop 叢集，可以提供大量的資料分析。此外，Azure 在近期也推出了機器學習 ([Machine Learning](http://azure.microsoft.com/zh-tw/services/machine-learning/)) 的服務，您可以直接利用雲端平台來進行資料的分析。其餘還包括了一些媒體服務以及驗證服務，細節就留給讀者自行前往 [Azure 官方網站](http://azure.microsoft.com)探索。

# 立即試用

為了讓更多用戶可以體驗 Azure 完整且方便的雲端平台，現在針對新註冊的用戶提供了免費一個月多達台幣 6300 元的免費試用額度，而且過程中會受到消費限制的保護，您不必擔心在試用期間會被微軟收取任何一毛錢。若試用覺得滿意，您可以註冊一個隨付即用 (Pay-As-You-Go) 的帳戶，體驗雲端運算所帶來的用多少付多少的概念，現在就開始體驗吧：[http://azure.microsoft.com/zh-tw/pricing/free-trial/](http://azure.microsoft.com/zh-tw/pricing/free-trial/)

# 參考資料

- 各服務示意圖皆取自 [Microsoft Azure 官方網站](http://azure.microsoft.com)
- Azure 簡介, [http://azure.microsoft.com/zh-tw/documentation/articles/fundamentals-introduction-to-azure/](http://azure.microsoft.com/zh-tw/documentation/articles/fundamentals-introduction-to-azure/)
- Introducing Microsoft Azure, [http://azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/](http://azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/)