# 淺談雲端運算

近年來「雲端」一詞相當火紅，但絕大多數的人對於「雲端」的概念停留在只要可以連上網或是將服務或網站放在伺服器上就是使用到了雲端運算的技術。然而，真正雲端運算 (Cloud Computing) 的本質應該是建立在虛擬化 (Virtualization)、高延展性 (Scalability)、用多少付多少 (Pay-As-You-Go) 的概念。

藉由虛擬化的技術，主機不一定再需要一台台堆放在機架上，而是以某種形式存在於一個廣大的資源池 (Resource Pool) 中，而受益於高度延展的特性，以一個線上售票服務為例，你不再需要為了極短時間的搶票區間預備好大量的實體主機在機房中，只需要透過 Auto-Scaling 的技術，在特定高用量的時間區間啟用較多的運算實體，如此一來便可以大幅降低主機運算的閒置成本，這才是雲端運算的真正價值與意義。

# Azure 簡介

Microsoft Azure 是微軟所打早的一個公有雲服務平台，過去稱為 Windows Azure。Azure 一開始與其競爭對手 AWS 相同，是以 IaaS 作為切入點提供基礎建構雲服務，時至今日，Azure 已經提供了橫跨 IaaS 到 PaaS 甚至 SaaS 豐富的雲服務，你可以使用各種方式來使用這個雲端平台來建置你的服務。例如，您可以將一個 Web 應用程式部署至 Azure 的網站服務，將資料庫建置在 Azure 所提供的 SQL Database 中，並利用 Storage 服務來存取二進位資料，甚至您也可以在數分鐘之內直接在 Azure 上建立一台全新的虛擬機器，透過 IaaS 服務來建立俱有高延展性的平台。

# Azure 的組成

下面這張圖以服務的特性及目的將 Azure 上所提供的所有服務分門別類，我們可以很清楚看到 Azure 從最一開始單純的服務到今天，已經是一個提供橫跨 IaaS、PaaS 到 SaaS 的完整雲端平台，在接下來的內容中，筆者將簡單介紹 Azure 中的各項服務。

![The components of Azure](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/azurecomponentsintronew780.png)

若您有興趣的話，微軟也將 Azure 所有的服務繪製成一張[巨幅海報](http://azure.microsoft.com/zh-tw/documentation/infographics/azure/)，您可以自行下載該 PDF 檔輸出，或是親自參與台灣微軟所舉辦的研討會，也有機會拿到繁體中文版的海報，筆者在先前的 TechDays 2014 便拿到了一張。

# 運算服務

執行應用程式是在雲端平台中最重要也是最基本的一項要素，在 Azure 中依照服務的彈性提供了三種主要的選擇：虛擬機器 (Virtual Machines)、雲端服務 (Cloud Services) 以及網站服務 (Websites)。

## 虛擬機器 (Virtual Machines)

![Virtual Machines](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/virtualmachinesintronew.png)

虛擬機器 (Virtual Machines) 是一種 IaaS 的服務，提供了最高度彈性的服務，您可以透過 Azure Gallery 組件庫使用預先建立好的作業系統映像檔，其中包括了各個版本的 Windows Server 甚至是 Ubuntu、CentOS 等開源的作業系統環境，或是您也可以自行上傳預先準備好的 VHD 檔，將原本的本地環境部署至虛擬機器服務。在虛擬機器服務中，您擁有了 100% 的主控權，可以透過 SSH 或是遠端桌面連線的方式管理您的伺服器。

## 網站服務 (Websites)

![Websites](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/azurewebsitesintronew.png)

網站服務 (Websites) 是一種最容易部署網站的服務，他是建構在 IIS 之上，並且提供了對 PHP、Python及 node.js 的支援，讓您可以在數分鐘之內就將一個網站應用程式部署至雲端。同時，網站服務也提供了高度延展的設定，您可以依照需求選擇不同大小/價位的服務，並且可以依照流量及 CPU 運算資源做 auto-scaling。

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

Blob 是設計用來儲存非結構化二進位資料的服務，而且單一個 Blob 就有嘟打 1 TB 的容量，適合用來儲存視訊或備份資料等等，您可以使用 Blob 作為簡單而且成本低廉的儲存體服務。