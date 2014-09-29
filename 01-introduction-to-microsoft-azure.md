# 淺談雲端運算

近年來「雲端」一詞相當火紅，但絕大多數的人對於「雲端」的概念停留在只要可以連上網或是將服務或網站放在伺服器上就是使用到了雲端運算的技術。然而，真正雲端運算 (Cloud Computing) 的本質應該是建立在虛擬化 (Virtualization)、高延展性 (Scalability)、用多少付多少 (Pay-As-You-Go) 的概念。

藉由虛擬化的技術，主機不一定再需要一台台堆放在機架上，而是以某種形式存在於一個廣大的資源池 (Resource Pool) 中，而受益於高度延展的特性，以一個線上售票服務為例，你不再需要為了極短時間的搶票區間預備好大量的實體主機在機房中，只需要透過 Auto-Scaling 的技術，在特定高用量的時間區間啟用較多的運算實體，如此一來便可以大幅降低主機運算的閒置成本，這才是雲端運算的真正價值與意義。

# Azure 簡介

Microsoft Azure 是微軟所打早的一個公有雲服務平台，過去稱為 Windows Azure。Azure 一開始與其競爭對手 AWS 相同，是以 IaaS 作為切入點提供基礎建構雲服務，時至今日，Azure 已經提供了橫跨 IaaS 到 PaaS 甚至 SaaS 豐富的雲服務，你可以使用各種方式來使用這個雲端平台來建置你的服務。例如，您可以將一個 Web 應用程式部署至 Azure 的網站服務，將資料庫建置在 Azure 所提供的 SQL Database 中，並利用 Storage 服務來存取二進位資料，甚至您也可以在數分鐘之內直接在 Azure 上建立一台全新的虛擬機器，透過 IaaS 服務來建立俱有高延展性的平台。

# Azure 的組成

下面這張圖以服務的特性及目的將 Azure 上所提供的所有服務分門別類，我們可以很清楚看到 Azure 從最一開始單純的服務到今天，已經是一個提供橫跨 IaaS、PaaS 到 SaaS 的完整雲端平台。

![The components of Azure](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/fundamentals-introduction-to-azure/20140924060441/includes/intro-to-azure/azurecomponentsintronew780.png)

若您有興趣的話，微軟也將 Azure 所有的服務繪製成一張[巨幅海報](http://azure.microsoft.com/zh-tw/documentation/infographics/azure/)，您可以自行下載該 PDF 檔輸出，或是親自參與台灣微軟所舉辦的研討會，也有機會拿到繁體中文版的海報，筆者在先前的 TechDays 2014 便拿到了一張。