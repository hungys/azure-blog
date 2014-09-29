Day 1: 認識 Microsoft Azure
==========================

# 前言

這是筆者第一次參加鐵人賽，正好看到今年的推廣主題是由 Microsoft 所提出的 Open Source on Azure，筆者一直以來非常熱衷於微軟的 solution，但也非常喜歡 open source 的相關專案，正巧利用這次的機會，希望在這 30 天的時間內可以讓更多人認識近年來微軟在 Azure 上所做的轉變，以及對於 open source solution 日益健全的支援。

# 淺談雲端運算

近年來「雲端」一詞相當火紅，但絕大多數的人對於「雲端」的概念停留在只要可以連上網或是將服務或網站放在伺服器上就是使用到了雲端運算的技術。然而，真正雲端運算 (Cloud Computing) 的本質應該是建立在虛擬化 (Virtualization)、高延展性 (Scalability)、用多少付多少 (Pay-As-You-Go) 的概念。

藉由虛擬化的技術，主機不一定再需要一台台堆放在機架上，而是以某種形式存在於一個廣大的資源池 (Resource Pool) 中，而受益於高度延展的特性，以一個線上售票服務為例，你不再需要為了極短時間的搶票區間預備好大量的實體主機在機房中，只需要透過 Auto-Scaling 的技術，在特定高用量的時間區間啟用較多的運算實體，如此一來便可以大幅降低主機運算的閒置成本，這才是雲端運算的真正價值與意義。

# Azure 簡介

[Microsoft Azure](http://azure.microsoft.com) 是微軟所打早的一個公有雲服務平台，過去稱為 Windows Azure。Azure 一開始與其競爭對手 AWS 相同，是以 IaaS 作為切入點提供基礎建構雲服務，時至今日，Azure 已經提供了橫跨 IaaS 到 PaaS 甚至 SaaS 豐富的雲服務，您可以使用各種方式來使用這個雲端平台來建置服務。例如，您可以將一個 Web 應用程式部署至 Azure 的網站服務，將資料庫建置在 Azure 所提供的 SQL Database 中，並利用 Blob 服務來存取二進位資料，甚至也可以在數分鐘之內直接在 Azure 上建立一台全新的虛擬機器，透過 IaaS 服務來建立俱有高延展性、高度彈性的平台。

# Azure 的組成

下面這張圖以服務的特性及目的將 Azure 上所提供的所有服務分門別類，我們可以很清楚看到 Azure 從最一開始單純的服務到今天，已經是一個提供橫跨 IaaS、PaaS 到 SaaS 的完整雲端平台，在接下來的內容中，筆者將簡單介紹 Azure 中的各項服務。

![The components of Azure](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/azurecomponentsintronew780.png)

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

執行應用程式是在雲端平台中最重要也是最基本的一項作業，在 Azure 中依照服務的彈性提供了三種主要的選擇：虛擬機器 (Virtual Machines)、雲端服務 (Cloud Services) 以及網站服務 (Websites)。

## 虛擬機器 (Virtual Machines)

![Virtual Machines](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/virtualmachinesintronew.png)

虛擬機器 (Virtual Machines) 是一種 IaaS 服務，提供了最高度彈性的服務，您可以透過 Azure Gallery 組件庫使用預先建立好的作業系統映像檔，其中包括了各個版本的 Windows Server 甚至是 Ubuntu、CentOS 等開源的作業系統環境，或是您也可以自行上傳預先準備好的 VHD 檔，將原本的本地環境部署至虛擬機器服務。在虛擬機器服務中，您擁有了 100% 的主控權，可以透過 SSH 或是遠端桌面連線的方式管理您的伺服器。

## 網站服務 (Websites)

![Websites](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/azurewebsitesintronew.png)

網站服務 (Websites) 是一種最容易部署網站的服務，他是建構在 IIS 之上，並且提供了對 PHP、Python 及 node.js 的支援，讓您可以在數分鐘之內就將一個網站應用程式部署至雲端。同時，網站服務也提供了高度延展的設定，您可以依照需求選擇不同大小/價位的服務，並且可以依照流量及 CPU 運算資源做 auto-scaling。

## 雲端服務 (Cloud Services)

![Cloud Services](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/cloudservicesintronew.png)

雲端服務 (Cloud Services) 是一種介於前兩者的 PaaS 服務，它提供了比網站服務更高的彈性，但背後的虛擬機器是由微軟資料中心所代管，您可以專注在您的應用程式及服務本身。

關於以上三種服務的比較以及選擇，在往後會有專文做更深入的分析及介紹。

# 資料管理

大部份的應用程式都需要存取資料，您除了可以在 IaaS 服務上自行建立資料庫的儲存環境外，在 Azure 中也依照不同的需求提供了幾種主要的選擇：SQL Database、資料表 (Table) 以及 Blob。

## SQL Database

![SQL Database](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/storageazuresqldatabaseintronew.png)

SQL Database (過去稱為 SQL Azure) 是一個針對雲端環境優化的 SQL Server 服務，提供了關聯式資料庫的所有重要功能，而且如同過去熟悉的 SQL Server，您可以使用 Entity Framework、ADO.NET 或是其他熟悉的資料存取技術來存取 SQL Databse。如果您過去的服務是建立在 SQL Server 上，SQL Database 會是一個您很好的雲端化選擇，透過 SQL Management Studio 就可以輕鬆的將資料放上雲端。

## 資料表 (Table)

![Table](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/storagetablesintronew.png)

資料表 (Table) 是一種提供大量儲存 key/value 型式的 NoSQL 服務，它不提供關聯式資料庫的功能，但如果您所要存的資料量相當龐大，或是不需要對這些資料執行複雜的 SQL 查詢，那麼它會是一個簡單明瞭而且成本遠低於 SQL Database 的選擇。

## Blob

![Blob](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/storageblobsintronew.png)

Blob 是設計用來儲存非結構化二進位資料的服務，而且單一個 Blob 就有多達 1 TB 的容量，適合用來儲存視訊或備份資料等等，您可以使用 Blob 作為簡單而且成本低廉的儲存體服務。

# 網路

Azure 目前在亞洲、歐洲及美洲數個資料中心內執行，您除了可以在 Azure 上部署您的雲端應用程式之外，也可以用來作為本地資料中心或網路的延伸，透過虛擬網路 (Virtual Network) 以及流量管理員 (Traffic Manager) 的服務來達成。

## 虛擬網路 (Virtual Network)

![Virtual Network](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/virtualnetworkintronew.png)

您可以將 Azure 當作您私有網路的延伸，在上面依需求建立虛擬機器來使用，透過 VPN 閘道裝置來串接您的私有網路以及雲端，建立一個私有雲 (Private Cloud)。例如您只需要將您所擁有的 IPv4 地址指派給雲端的虛擬機器，公司的員工便可以像存取本地端服務來取用這些部署在雲端上的應用程式。

## 流量管理員 (Traffic Manager)

![Traffic Manager](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/trafficmanagerintronew.png)

如果您的服務的使用者遍佈全球，並且將服務部署在多個國家的資料中心中運行，為了減少 latency，您會希望使用者可以自動存取到距離較近的資料中心，但若該資料中心的執行個體已經過載，這時候便應該要求導向另一座資料中心，這就是流量管理員 (Traffic Manager) 所可以提供的服務，您可以自行訂定規則，來決定如何將使用者導向到最適合的資料中心。

# 行動裝置

過去撰寫行動應用程式時，您可能會需要使用到資料存取、身份驗證、推播通知等功能，需要自行建構 API 來提供行動裝置端呼叫各項服務。而在 Azure 上，針對 Mobile App 的各種基本需求，提供了包括各式基本服務的行動服務 (Mobile Services) 以及可以大量將訊息推送至用戶端的通知中樞 (Notification Hubs)，大幅降低了開發 App 後端所需的時間，而且透過單一的服務就可以提供 Android、iOS 以及 Windows Phone 各個平台所需的服務。

## 行動服務 (Mobile Services)

![Mobile Services](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/mobileservicesintronew.png)

在行動服務 (Mobile Services) 中，微軟提供了針對 Mobile App 最基本的三種需求，除了提供 SQL Database 的資料儲存空間，更直接提供了對應的 API 接口讓 Client 端可以快速來取用資料庫的資料。另外，現今的 App 經常會整合各式社交服務的帳號，過去若要自行建立這樣的帳號綁定服務，需要撰寫相當多的 OAuth 驗證或是 link & unlink 的邏輯，但透過行動服務，您可以輕鬆將您的 App 與 Facebook、Twitter 甚至是 Google 服務整合，現在更提供了與企業 Active Directory 整合的支援。而在推播服務方面，微軟也提供了 GCM、APNS、MPNS 及 WNS 的整合，讓您可以透過單一接口將訊息推送至各個不同的平台，省下了非常多後端開發的時間。

行動服務目前針對 iOS、Android 以及 Windows Phone 皆有提供 SDK 可以使用，您幾乎可以在短短幾行程式碼內就將 App 接上 Azure Mobile Services。

## 通知中樞 (Notification Hubs)

![Notification Hubs](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/notificationhubsintronew.png)

前面的行動服務雖然提到提供了各平台推播通知的整合，但如果今天的需求是在短時間內大量廣播至各個平台的裝置，例如在數秒內要完成上百萬條訊息的推送，這時候變需要完整的分散式服務架構。與知名的 BaaS (Backend-as-a-Service) 服務 Parse 不同之處在於，通知中樞 (Notification Hubs) 提供了相當完整的基礎服務以及 API，讓您可以在這個架構之下建立一個快速大量推播的平台，而且可以為您省下整合各平台推播服務的時間。

# 訊息服務

## 佇列 (Queues)

![Queues](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/queuesintronew.png)

佇列 (Queues) 是一種 FIFO (First-In-First-Out) 的設計概念，一個應用程式將訊息放入佇列中，而另一個應用程式來讀取該訊息並進行進一步的處理。舉一個簡單的例子，目前最熱門的相簿服務 Flickr 在使用者將照片上傳後，會自動將照片做各種尺寸的縮圖，這種服務變可以透過佇列來達成，在網頁應用程式接收到上傳的照片後，便將該照片資訊放入佇列當中，而在背景工作的另一個角色便不停的從佇列中讀取新上傳的照片，並進行一些需要較長時間的縮圖處理。

## 服務匯流排 (Service Bus)

![Service Bus](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/servicebustopicssubsintronew.png)

服務匯流排 (Service Bus) 與上面所提到的佇列不同的地方在於，服務匯流排的目的是讓應用程式在任何地方都能交換資料。除了佇列所能提供的一對一通訊之外，服務匯流排還提供了發佈與訂閱 (pub/sub) 的機制，應用程式可以將訊息傳送到某個主題，而有訂閱該主題的多位收件者可以同時讀取相同訊息，達成一對多的通訊。此外，服務匯流排也提供了轉送 (Relay) 的機制，提供通過防火牆的安全通訊方式。

# 快取

應用程式可能會一再存取相同的資料，若要提升服務的效能，最直覺的做法就是將大量被取用的資料就近保留一份，這就是快取的概念。Azure 提供了兩種不同的快取架構，分別是針對應用程式記憶體的內部快取以及針對 Blob 資料的內容傳遞網路 (CDN)。

## Azure 快取 (Azure Caching)

![Cache](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/azurecacheintronew.png)

Azure 快取 (Azure Caching) 的基本概念便是將大量被取用的內容或資料儲存於記憶體內部，如此一來便可以獲得比資料庫存取或是硬碟存取更高的效能。而除了這項服務之外，Azure 現在也支援了開源專案 redis cache。

## 內容傳遞網路 (CDN)

![CDN](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/cdnintronew.png)

CDN 在全球各地有許多網站，當某個地區的使用者第一次存取特定 Blob 物件時，其內容會從 Azure 資料中心複製到該地區中的 CDN 儲存體，在此之後，來自該地區的 request 將會使用在 CDN 中快取的 Blob 複本，而不需直接連接最近的 Azure 資料中心。

# 其他

前文已經介紹了許多 Azure 所提供的雲端服務，橫跨了 IaaS 到 PaaS，但其實 Azure 還提供了更多服務我們還沒有介紹。以資料分析為例，HDInsight 便是一個建立在 Azure 上的 Hadoop 服務，可以提供大量的資料分析。此外，Azure 在近期也推出了機器學習 (Machine Learning) 的服務，您可以直接利用雲端平台來進行資料的分析。其餘還包括了一些媒體服務以及驗證服務，細節就留給讀者自行前往 Azure 官方網站探索。