Day 3: 在 Azure 上快速架設開源 Web App
===================================

# 前言

從本文開始，筆者將開始深入介紹 Azure 中各個服務及元件的使用，首先第一個主題會由較容易上手的網站服務 (Websites) 開始，不過是要帶領讀者透過 Azure 所提供的 Gallery，在數分鐘內快速部署一個 Web App 專案至雲端平台，今天筆者將會以相當多人使用的部落格專案 WordPress 作為範例。若您有實際從安裝 Linux VM、安裝 LAMP 到完成 WordPress 的安裝，甚至是網域的設定，在體驗過 Azure 的便利之後，您一定會非常有感覺。

# 透過 Azure Gallery 安裝 Web App

1. 首先您必須先使用您的 Azure 帳號進入 Management Portal，接著點選左下角的**「New」**。在這邊先向讀者說明，由於筆者比較習慣使用英文的界面，所以文中的截圖大多會以英文為主，若使用中文的界面請自行比對。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step01-new.png)

2. 接著點選**「Compute」**>**「Web Site」**開始建立一個網站服務，在這邊我們點選**「From Gallery」**，直接從微軟預先準備好的 Web Application 專案部署一個網站。若您未來想要自行部署自己的網站應用程式至網站服務上，可以直接點選**「Quick Create」**部署一個網站服務，並使用 FTP 或是其他版本控制途徑將應用程式部署至雲端。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step02-from-gallery.png)


3. 在 Azure Gallery 中，我們可以看到許多微軟為我們準備好的網頁應用程式，從部落格、CMS、討論區甚至是 Python 界著名的 Framework Flask 也有，在此我們選擇**「WordPress」**。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step03-gallery-wordpress.png)

4. 接下來需要對我們的網站服務做初步的設定，這邊最重要的是選定一個您想使用的網域名稱，每個網站服務實體都可以免費擁有一個 azurewebsites.net 底下的子域名。當然，您未來也可自行選擇設定自定義域名。資料庫部分由於 WordPress 大多是搭配 MySQL 使用，所以我們使用預設的設定。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step04-wordpress-configure.png)

5. 接著設定精靈會要求我們輸入一個 MySQL 資料庫的名稱，Azure 便會自動為我們註冊一個免費的 ClearDB 託管資料庫。勾選左下角的同意便可以繼續設定流程。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step05-mysql.png)

6. 設定完成之後，Azure 會開始為我們部署網站，通常這個流程只需要數分鐘就可以完成。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step06-deploying.png)

7. 部署完成之後，在 Management Portal 中的 Websites 便可以看到我們剛剛所部署好的網站服務。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step07-portal.png)

8. 點選該網站服務後，會顯示關於這個服務的細部設定以及統計數據。其中，我們可以看到右方的 Site Url，Azure 已經幫我們對應到剛剛所設定的域名了，透過這個域名我們就可以連進剛剛所架設的 WordPress。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step08-detail.png)

# 設定 WordPress

1. 打開剛剛的網址後，就可以進入到 WordPress 的設定頁面，令筆者比較驚訝的是，目前的 Azure 上的 WordPress 專案，已經預設內建繁體中文語系了，過去我們還需要自行下載語言包安裝。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step09-language.png)

2. 接著會要求我們輸入管理者的帳號密碼，請不要學筆者取像 ironman/ironman 如此簡單的帳密，否則會有安全性的疑慮。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step10-blog-settings.png)

3. 最後這邊的提示相當的口語化... 沒錯！我們已經完成了 WordPress 的架設，前前後後並不超過五分鐘。而事實上，這個 WordPress 是被 host 在 Windows 環境下的 IIS，但網站服務已經幫我們 cover 了所有的設定。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step11-finish.png)

4. 輸入帳號密碼後，我們就可以進入部落格的後台，到此為止已經全部完成。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step12-login.png)

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step13-dashboard.png)

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step14-home-page.png)

# 與 Storage 整合

在介紹本節之前，首先要請讀者留意一下網站服務的[計價規則](http://azure.microsoft.com/zh-tw/pricing/details/websites/)，如果您選擇的是免費的網站服務，會受到每天對外流量 **165 MB** 的限制，如果是一般純文字、流量普通的部落格倒還好，但如果有大量的圖片而且存放在該網站服務的目錄中，有可能會輕鬆破表（這是筆者曾經遇過的慘痛經驗）。

![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/websites-pricing.png)

也因為如此，我們必須尋找一個更適合存放這些多媒體檔案的空間，Azure 上所提供的 Storage 儲存服務就再適合不過了。我們在 Day 1 曾經介紹到 Blob 的儲存體服務，受益於 Storage 相當低廉的成本，如果我們將這些較大的檔案存放在 Azure Storage，就可以有效降低網站對外的流量，在一定程度的訪客數之下，您的免費層級服務仍可以維持住服務。接下來的內容，將帶領大家整合 WordPress 與 Azure Storage，透過 MS Open Tech 所開發的外掛插件。

1. 首先，進入到 WordPress 的後台，點選**「外掛」**並搜尋**「Azure」**，搜尋結果第一個**「Windows Azure Storage for WordPress」**便是我們所需要的插件，點選**「立刻安裝」**。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step15-extensions.png)

2. 下載並安裝外掛後，點選**「啟用外掛」**。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step16-extensions-finish.png)

3. 這時在外掛列表中可以看到我們剛剛所安裝的插件，您也可以隨時在此停用或刪除。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step17-extensions-list.png)

4. 接著從畫面左側的**「設定」**>**「Windows Azure」**開始進行相關設定。由於我們還沒有深入介紹到 Storage 的建立以及使用，這部分讀者可以自行研究如何從 Management Portal 建立一個儲存體服務並取得所需的**「Storage Account Name」**及**「Primary Access Key」**。不過這個外掛的流程上有點奇怪，如果您要選擇現有的 Container 需要先點選**「Save Changes」**讓畫面 reload 才會出現選項。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step18-storage-configure.png)

5. 這邊我們選擇建立一個新的 Container，輸入名稱後按下**「Create」**。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step19-create-container.png)

6. 設定完成後，這時候當我們進入到發文頁面，可以看到新增媒體旁多了一個 Azure 的標誌。按下這個 icon 便可以開始上傳圖片至 Blob 儲存體。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step20-new-post.png)

7. 在上傳的頁籤中，選擇相對應的 Container，選取欲上傳的圖檔並按下**「Upload」**。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step21-upload.png)

8. 上傳成功之後，在 Browse 頁籤便出現了剛剛上傳的圖片，點選以加入文章中。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step22-select-photo.png)

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step23-send-post.png)

9. 最後我們用 Chrome 的開發者工具檢查這則文章圖檔的 source 位置，可以看到是存放在 *.blob.core.windows.net 與網站本身不同的位置，這樣一來這個圖片的流量便不會計入 165MB 的限制了。

	![install tutorial](https://raw.githubusercontent.com/hungys/azure-blog/master/media/03-install-open-source-cms-on-azure/step24-check-url.png)

到此，我們已經完成了 WordPress 的架設並針對多媒體檔案部分與 Azure Blob Storage 做了結合，相信讀者也感受到在 Azure 網站服務上架設開源 Web App 專案有多麼簡單了！

# 參考資料

- Enterprise Class WordPress on Azure Websites, [http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-php-enterprise-wordpress/](http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-php-enterprise-wordpress/)