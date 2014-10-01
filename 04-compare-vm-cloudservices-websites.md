Day 4: 虛擬機器、雲端服務、網站服務比較
=================================

# 前言

Microsoft Azure 提供了相當多種裝載、部署應用程式的選擇，從虛擬機器 (Virtual Machines)、雲端服務 (Cloud Services) 到網站服務 (Websites)，但大多數剛接觸 Azure 的使用者可能還難以理解這三者之間的差別，又或者是不了解雲端運算中 IaaS、PaaS、SaaS 的差異性。本文將就這三個服務做初步的介紹以及比較，希望未來讀者能夠更輕鬆地做選擇。

# 初步比較

![Comparison](https://raw.githubusercontent.com/hungys/azure-blog/master/media/04-compare-vm-cloudservices-websites/comparison-1.png)

上圖是取自 Azure 官方網站對於這三個服務差異的說明，很明顯的可以看到用了兩個因子來比較他們的差異，以上手的容易程度來比較的話：網站服務 > 雲端服務 > 虛擬機器，而反過來說，如果以可以控制的程度來比較的話，結果恰好是反過來的結果：網站服務 < 雲端服務 < 虛擬機器，這樣的結論應該可以接受。

假如用更細的元素、架構，從 Application、Data 到 Firewall、Network 甚至是 OS 本身，下面這張圖也可以明顯的看出三者之間的差別。他們之間的關係有點類似 IaaS、PaaS、SaaS 的層次之分，但值得注意的是，即使是最容易上手的網站服務，也應該歸類在 PaaS。 綜合來看，如果需要對服務有最高的控制權、最彈性的設定，則選擇虛擬機器，反之，想要最快速部署，則使用網站服務。

![Comparison](https://raw.githubusercontent.com/hungys/azure-blog/master/media/04-compare-vm-cloudservices-websites/comparison-2.png)

# 網站服務 (Websites)

![Websites](https://raw.githubusercontent.com/hungys/azure-blog/master/media/04-compare-vm-cloudservices-websites/websites.png)

網站服務是屬於 **PaaS** 層級的服務，其背後是建構在微軟自家的伺服器平台 IIS 之上，也就是在 Windows Server 的環境，但同時也具備了對各種程式語言的支援，不僅僅是 ASP.NET 而已。最大的特點是可以在很短時間內就部署好一個應用程式。

## 特色

- 快速、輕鬆部署一個可高度擴展的雲端環境，而且可以從很小的規模開始
- **支援 .NET、Java、PHP、Node.js、Python**
- 內建自動調整規模與負載平衡
- 具備自動修補功能的高可用性
- 使用 Git、TFS 和 GitHub 進行部署
- 與 Azure 其他服務連結：SQL Databases、MySQL、DocumentDB、搜尋、MongoDB
- 透過 Gallery 快速安裝開源的網頁應用程式如 WordPress、Joomla!
- **(最新)** 透過 Azure WebJobs 快速建立背景工作及服務 

## 適用情境

- 專注在應用程式本身，無需對 OS 做繁雜的設定或安裝
- 現代化的 Web 應用程式
- 直接從 Git、TFS 部署網站進行連續開發
- 單純且輕量的 REST API (如 ASP.NET Web API 最容易部署至 Websites 上)
- 快速建立一個 CMS 網站 (如 WordPress、Drupal、Joomla! 等)

更多細節可參考官方網站：[http://azure.microsoft.com/zh-tw/services/websites/](http://azure.microsoft.com/zh-tw/services/websites/)


# 雲端服務 (Cloud Services)

![Cloud Services](https://raw.githubusercontent.com/hungys/azure-blog/master/media/04-compare-vm-cloudservices-websites/cloud-services.png)

雲端服務同樣是屬於 **PaaS** 層級的服務，而且是建立在 Windows Server 的基礎之上，與 Websites 相同提供了對多種語言的支援，較大的差別是依照服務需求分為 Web Role 及 Worker Role，前者同樣是建立在 IIS 底下的 Web 服務，後者則是適合一些背景運行的工作。

## 相較於網站服務

- 針對應用程式特性提供不同角色 (Web Role & Worker Role)
- 必要時可遠端連線進入 VM 進行管理
- 應用程式整合 Diagnostics 診斷功能
- **需要將應用程式針對 Cloud Services 架構進行微調，並打包成特定格式部署**

## 特色

- 專注在應用程式本身而非 OS、硬體
- 支援 Java、Node.js、PHP、Python、.NET 和 Ruby
- 自動調整以符合需求和節省成本
- 整合式健全狀況、監視和負載平衡
- 自動修補 OS 和應用程式

## 適用情境

- 多層次、架構的服務，針對各個元件部署在適合的角色中
- **無狀態性 (stateless) 的服務**

更多細節可參考官方網站：[http://azure.microsoft.com/zh-tw/services/cloud-services/](http://azure.microsoft.com/zh-tw/services/cloud-services/)


# 虛擬機器 (Virtual Machines)

![Virtual Machines](https://raw.githubusercontent.com/hungys/azure-blog/master/media/04-compare-vm-cloudservices-websites/virtual-machines.png)

虛擬機器與前兩者最大差異在於它是一種 IaaS 層級的服務，提供了基礎架構給開發者及 IT 人員。如同您運行在本地的 VM，您擁有完全的主導權，可以在上面安裝各種服務、運行各種應用程式，提供了最彈性的服務。最重要的是，除了 Windows 之外，您也可以選擇諸如 Ubuntu、CentOS 或 SUSE 的 Linux 系統。

## 相較於雲端服務

- 需要自行管理 OS 本身，例如安全性更新等
- 靈活的鏡像轉移，例如直接從本地的環境部署至雲端的 VM 中
- 應用程式無需為了架構特別修改

## 特色

- **支援 Windows 或 Linux**
- 支援直接將本地的 VHD 上傳至雲端部署
- 內建虛擬網路、負載平衡
- 可自行安裝各種開放式軟體：Oracle、MySQL、Redis、MongoDB
- 按分鐘數計費，關機即停止收費

## 適用情境

- 需要高度自訂的作業環境
- 直接將本地環境遷移至雲端
- 運行網站服務及雲端服務所不支援的程式語言

更多細節可參考官方網站：[http://azure.microsoft.com/zh-tw/services/virtual-machines/](http://azure.microsoft.com/zh-tw/services/virtual-machines/)

# 結論

前文已經針對三個服務做了簡單的介紹以及比較，若想要看更完整的比較表格可以參考官網的文章：[Azure 網站、雲端服務與虛擬機器之比較](http://azure.microsoft.com/zh-tw/documentation/articles/choose-web-site-cloud-service-vm/)。最後用下面兩張圖作為總結，讓讀者能對三個服務定位的差異有更深刻的印象，他們之間就好比自己「買房」、「租房子」或是「住旅館」的差異，可以說是相當貼切！

![Comparison](https://raw.githubusercontent.com/hungys/azure-blog/master/media/04-compare-vm-cloudservices-websites/comparison-3.png)

![Comparison](https://raw.githubusercontent.com/hungys/azure-blog/master/media/04-compare-vm-cloudservices-websites/comparison-4.png)

# 參考資料

- Azure 虛擬機器, [http://azure.microsoft.com/zh-tw/services/virtual-machines/](http://azure.microsoft.com/zh-tw/services/virtual-machines/)
- Azure 雲端服務, [http://azure.microsoft.com/zh-tw/services/cloud-services/](http://azure.microsoft.com/zh-tw/services/cloud-services/)
- Azure 網站服務, [http://azure.microsoft.com/zh-tw/services/websites/](http://azure.microsoft.com/zh-tw/services/websites/)
- Azure 網站、雲端服務與虛擬機器之比較, [http://azure.microsoft.com/zh-tw/documentation/articles/choose-web-site-cloud-service-vm/](http://azure.microsoft.com/zh-tw/documentation/articles/choose-web-site-cloud-service-vm/)
- Introducing Windows Azure WebJobs, [http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
- Windows Azure 案例分析：選擇虛擬機或雲服務？, [http://www.infoq.com/cn/articles/virtual-machine-or-cloud-service](http://www.infoq.com/cn/articles/virtual-machine-or-cloud-service)
- 如何選擇 Windows Azure 託管服務的類型？Websites, Cloud Service 還是 Virtual Machine, [http://www.cnblogs.com/threestone/archive/2012/12/23/2830041.html](http://www.cnblogs.com/threestone/archive/2012/12/23/2830041.html)
