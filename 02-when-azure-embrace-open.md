Day 2: 當 Azure 擁抱開放
======================

# 開源 vs 微軟

在筆者整理關於這個主題的相關資料時，正好看到官網上一段影片主題是「[開源開發人員為什麼會使用微軟 Azure 公有雲](http://www.microsoft.com/zh-cn/showcase/details.aspx?uuid=0bbc8d44-5cdf-405e-8d07-70bed2b55cb6)」，其中受訪的技術長，滿有趣且中肯的提到了過去開源界對於微軟的認知與印象。

> * 我之前理解的微軟，是一個埋頭打造自己生態系統的一家軟體公司。
> * 其實在業界，一直以來都有「開源」和「微軟」這兩個技術體系，大家基本上就是一個老死不相往來的狀態，你做你的，我做我的。
> * 我們的系統是完全使用開源軟體來搭建的，在 Azure 上已經運行了好幾個月，這段時間用下來，給我的感覺這並不是微軟的一貫作風。

的確，過去 Azure 公有雲平台也幾乎清一色的都是微軟自家的方案，但從近幾年來的轉變，可以很明顯看到對於開源專案的支持已經大幅提升，例如將 redis cache 整合進 Azrue 的快取服務、與 mongolab 合作推出 MongoDB 的託管服務，也針對各個服務提供了包含 Python、node.js、PHP... 在內的 SDK，甚至這些 SDK 都是以開源型式公開在 GitHub 上，這也是為什麼前些陣子微軟要正名 Azure，從「Windows Azure」走向「Microsoft Azure」，因為 Azure 已經不再只是 Microsoft solution only 的雲服務了。


# 從 MS Open Tech 說起

![MS Open Tech](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/ms-open-tech.png)

微軟在 2012 年跨出了不尋常的第一步，宣布成立一家獨立的子公司專注參與開源專案、開放標準的制定，公司名稱為「微軟開放技術有限公司」(Microsoft Open Technologies Inc.)。這一步棋很顯然微軟希望能夠擺脫過去大家所抱持的封閉、獨斷印象，開始著手 open source 專案的貢獻。而這兩年來，微軟也確實廣泛參與了多個開源專案，甚至躋身前 20 大 Linux Kernel contributer（筆者去年參加 COSCUP 時聽到 Linux Kernel 的 keynote 上提到這點也相當訝異），這都是過去大家所不知道而且無法想像的微軟。

而在 [MS Open Tech](http://msopentech.com/) 的官方網站也可以看到，目前公司已經參與了多項知名的 open source 專案，例如非常知名的快取專案 redis cache，微軟也參與了開發，並且讓它可以更穩定、更相容的執行在自家的 Azure 平台上。而 App 相關的專案中，也可以看到近幾個月來微軟對於 Apache Cordova 這個跨平台 App 開發的專案也有相當多的著墨以及推廣。另外，如果有關注 Azure 發佈的一系列 open source SDK，可以發現微軟尤其與 Node.js 的社群有深入的合作，幾乎所有服務都提供了相對應的 SDK，可見微軟對於 Node.js 的重視與愛戴。除此之外，自家的 .NET Micro Framework 以及下一代的 ASP.NET vNext 也都已經成為開源的專案，所有原始碼都可以在 GitHub 或是 CodePlex 上找到，關於這方面的更多消息可以參考 [.NET Foundation](http://www.dotnetfoundation.org/) 這個組織的網站。

# 與 Google 及 Docker 合作

![Docker](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/docker.png)

> 在上個月，我們宣佈支援部署 Docker 上 Azure 虛擬機器上，並且應用我們的技術，讓客戶能夠盡快地使用 Docker 的威力。為了持續為我們的客戶提供更好的支援，我們宣佈將與 Google 及 Docker 合作，預計將在 Microsoft Azure 上支援 Kubernetes 及 libswarm 這兩個開源專案。

這應該只是不久前的[新聞](http://blogs.msdn.com/b/msdntaiwan/archive/2014/07/12/google-docker-microsoft-azure.aspx)，微軟為了讓 Azure 的用戶能夠更快享受到最近很夯的 Docker 的威力，正式宣布與宿敵 Google 以及 Docker 合作來投入相關 open source 專案的開發，並提供在 Microsoft Azure 上的支援。到這裡為止，相信大家已經感受到現在的微軟，已經與我們過去的刻板印象有所差別。在往後的文章中，筆者也預計會對 Azure 及 Docker 做初步的介紹。

# Azure 所支援的開源/開放服務

前面說了這麼多，現在正式帶大家來看看究竟目前在 Azure 上提供了哪些開源的服務，或是支援了哪些開放社群所鍾愛的語言。

## 虛擬機器 (Virtual Machines)

從 Azure 的 Management Portal 中建立一台新的虛擬機器時，可以選擇從 Gallery 選擇微軟預先建立好的映像檔來部署，而不需自己準備安裝映像檔全新安裝，所以只需要幾分鐘之內就可以部署好一台可以使用的開發測試環境。

在 Gallery 中，我們可以看到除了自家的 Windows Server 之外，也可以看到一系列對於 Linux VM 的支援，包括了與 Canonical 合作針對 Azure 優化的 Ubuntu Server，也有 CentOS-based 或是 SUSE 系列的選擇。開發人員或 IT 人員可以輕鬆的部署 server 環境，而且是微軟預先針對 Azure 及雲端環境所優化過的版本。

![Virtual Machines Gallery](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/virtual-machine-gallery.png)

## 雲端服務 (Cloud Services)

從 Azure 官方網站中可以看到，雖然雲端服務是基於 Windows Server 的 PaaS 服務，但除了自家的 .NET 之外也都分別提供了對 Java、Node.js、Python、Ruby 的支援，您只需要在 Management Portal 選擇所需要的版本即可快速部署相關應用程式。

![Supported language](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/websites-language.png)

## 網站服務 (Websites)

在 Management Portal 中，微軟提供了許多現成的 open source Web App，讓使用者可以快速從 Gallery 中佈建這些開源的網路應用程式或是 CMS 系統，例如非常多人使用的 WordPress、Drupal 以及 Joomla! 等等，都可在幾個按鍵之內就完成安裝並且部署在微軟的資料中心中。

![Websites apps](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/wordpress.png)

## Azure 快取 (Azure Cache)

![Redis cache](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/redis.png)

在前些陣子，微軟官方正式宣布將使用開源的 redis cache 來作為 Azure 快取服務的基底，您只需要使用新的 [Azure Portal](https://portal.azure.com/) 就可以快速建置 redis 服務，並且輕鬆地監視快取的健全狀況和效能。

## 資料庫服務

![Azure Store](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/management-portal-store.png)

從 Azure Management Portal 進入 Azure 的市集我們可以看到，目前針對資料庫服務也提供了 open source solution 的支援。例如微軟與 ClearDB 合作推出了 MySQL 資料庫的託管服務，另外前些陣子也宣布與 mongolab 合作，在 Azure 上推出 MongoDB 的服務。由此可見，在 Azure 上的資料庫存取不再僅限於 SQL Database 或是 Table 資料表，而是可以直接透過 Management Portal 快速佈建近年來相當熱門的 NoSQL solution。

![mongolab with Azure](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/mongolab.png)

## Azure SDK

我們也知道，大多數使用開源方案的公司或開發者，是不會使用 .NET 作為開發語言的，以國內許多年輕的公司為例，不外乎是使用 Python、Ruby 或是 Node.js 來開發。即使選擇這些語言不能直接與開放、開源劃上等號，但在微軟擁抱開放的策略之下，依然針對各個語言提供了相對應的 SDK，讓開發人員可以輕鬆來取用 Azure 上的服務。

就目前官方的 SDK 下載頁面來看，已經提供了針對 .NET、Java、Node.js、PHP、Python 及 Ruby 的支援，而行動服務方面也提供了針對 Android、iOS 及 Windows Phone 的全平台支援。詳情請見：[http://azure.microsoft.com/zh-tw/downloads/](http://azure.microsoft.com/zh-tw/downloads/)

![Azure SDK](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/azure-sdk.png)

# 後記

本文針對了微軟在 Azure 上所提供的 open source 服務做了概略性的介紹，在接下來的連載當中，會挑選其中較多人使用的服務來做介紹，希望可以讓大家看到擁抱開放後、很不一樣的 Microsoft Azure！

另外，今年 TechDays 2014 有一場由 Eric 所主講的 session 在介紹 Microsoft Azure 上所支援的開源技術，並且著重在與 Java 開發技術的整合，目前錄影檔已經上傳至 MVA (Microsoft Virtual Academy) 上，有興趣的可以前往：[http://www.microsoftvirtualacademy.com/training-courses/techdays-taiwan-2014-breakout-sessions-dev](http://www.microsoftvirtualacademy.com/training-courses/techdays-taiwan-2014-breakout-sessions-dev)

![Azure supported open technologies](https://raw.githubusercontent.com/hungys/azure-blog/master/media/02-when-azure-embrace-open/azure-supported-open.png)

# 參考資料

- Microsoft Open Technologies, [http://msopentech.com/](http://msopentech.com/)
- 開源開發人員為什麼會使用微軟 Azure 公有雲, [http://www.microsoft.com/zh-cn/showcase/details.aspx?uuid=0bbc8d44-5cdf-405e-8d07-70bed2b55cb6](http://www.microsoft.com/zh-cn/showcase/details.aspx?uuid=0bbc8d44-5cdf-405e-8d07-70bed2b55cb6)
- 微軟為何鍾情開源技術？, [http://oss.org.cn/html/79/n-86179.html](http://oss.org.cn/html/79/n-86179.html)
- 微軟 Windows Azure 的開源之道, [http://www.cnetnews.com.cn/2013/0522/2160615.shtml](http://www.cnetnews.com.cn/2013/0522/2160615.shtml)
- 我們宣佈與 Google 及 Docker 合作以在 Microsoft Azure 上支援新的開源專案, [http://blogs.msdn.com/b/msdntaiwan/archive/2014/07/12/google-docker-microsoft-azure.aspx](http://blogs.msdn.com/b/msdntaiwan/archive/2014/07/12/google-docker-microsoft-azure.aspx)
- 認識 Microsoft Azure 上的 Java 平台以及各種開源技術, [https://www.youtube.com/watch?v=ckrb6NjNWA8](https://www.youtube.com/watch?v=ckrb6NjNWA8)