Day 8: 使用 Dropbox 發佈專案至 Azure Websites
=====================================

# 前言

今天會是個極短篇，在規劃每天的進度時沒有想到這個主題這麼短如此簡單，也讓筆者可以稍微喘息一下正式進入第二周。我們在昨天曾介紹了使用 Git 分散式版本控制來部署網站至 Azure 網站服務，也介紹了與 GitHub 的整合，今天要介紹的是同樣在去年發佈的小而美的新功能，透過 Dropbox 來部署網站服務，你沒看錯，就是 Dropbox！

# 設定 Dropbox 部署

首先，您需要一個 Dropbox 帳號，這邊應該不需要多說，接著只需要登入 Management Portal 就可以開始設定。一開始，我們同樣在網站的細節頁面的 quick glance 處點選**「Set up deployment from source countrol」**。

![Step1](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-01-quick-glance.png)

接著會進入到我們熟悉的畫面，不過這次我們選擇的是**「Dropbox」**。

![Step2](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-02-choose-dropbox.png)

點下去之後會跳出一個新的分頁要求登入 Dropbox 並給予 Azure 授權，只需照著步驟執行即可。

![Step3](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-03-login-to-dropbox.png)

![Step4](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-04-dropbox-authorize.png)

驗證完成之後會要求我們選擇一個欲部署的資料夾，這個資料夾必須存在於您的 Dropbox 根目錄底下的 **「Apps(應用程式)\Azure」**底下，在此您也可以選擇建立一個新的資料夾。

![Step5](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-05-create-new-folder.png)

特別注意到此頁面有個選項**「Enable deployment rollbacks」**，若未來希望能夠回復到過去的 deployment，則請勾選這個選項，本文為了展示這個功能所以也將它勾選。

![Step6](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-06-enable-deployment-rollbacks.png)

完成設定之後約一分鐘之內就可以看到畫面上顯示已經將網站服務與 Dropbox 建立好連結，一切設定就已經完成囉！

![Step7](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-07-dropbox-linked.png)

# 將網站部署至 Dropbox

接下來我們要開始測試透過 Dropbox 來部署網站，首先透過編輯器建立一個簡單的網頁：

```
<html>
	<body>
		<h1>Hello ironman! (Dropbox)</h1>
		<h2>This website is deployed with Dropbox<h2/>
	</body>
</html>
```

在前一小節我們有提過，Azure 能夠存取的目錄是位在**「Apps(應用程式)\Azure」**底下，而網站所應該存放的位置需要與剛剛設定部署時選擇的相同，所以以筆者的範例應該是將 index.html 存放在「應用程式\Azure\ironman-dropbox」。

![Step8](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-08-dropbox-folders.png)

將網站都複製到對應的資料夾後，回到 Management Portal 的地方按下下方的**「Sync」**按鈕。

![Step9](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-09-sync-button.png)

接下來只需等待數秒鐘的時間，當然這個時間會取決於您部署的檔案大小，Azure 會自動將目錄底下的內容同步至網站服務上。

![Step10](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-10-deploying.png)

![Step11](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-11-deployed.png)

最後打開瀏覽器連到我們所建立的網站後，便可以看到剛剛所編輯的內容已經順利部署至網站服務中了。

![Step12](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-12-websites-ver1.png)

# 更新網頁並同步

接來我們要展示的是修改網頁內容並重新同步，在此我們簡單修改剛剛的 index.html，加入 ver2 的字樣：

```
<html>
	<body>
		<h1>Hello ironman! (Dropbox, ver2)</h1>
		<h2>This website is deployed with Dropbox<h2/>
	</body>
</html>
```

回到 Management Portal 同樣按下**「Sync」**按鈕稍作等待之後，便可以看到一個新的部署已經產生，按下小箭頭也可以看到細部的記錄及日誌。

![Step13](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-13-new-deployment.png)

此時回到我們的網站，可以發現內容也已經同步被修改了，相當容易就可以完成同步。

![Step14](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-14-websites-ver2.png)

不過讀者要注意的是，使用 Dropbox 同步並不具備我們在昨天所介紹的 GitHub 連續部署 (Continuous Deployment)，原因是對於 Dropbox 而言，一筆檔案的修改就是一次 Sync 的記錄，不像 Git 有所謂的「Commit」，所以 Azure 沒辦法準確的知道哪些檔案的更新是屬於這次的 deployment，於是未來每次要同步至網站服務時都需要按下**「Sync」**按鈕才可以執行同步。希望未來可以將這個功能整合至 CLI 中，應該會更加方便。

# 回溯至舊的部署

在 Git 中，我們可以很容易的切換至過去舊的 commit，而讀者應該也還記得我們在建立與 Dropbox 的連結時，曾經勾選了**「Enable deployment rollbacks」**，如此一來我們便可以輕鬆的回復到過去的 deployment。

若要回溯到舊的部署，只需要在 Management Portal 中選擇要回復的項目，並點選下方的「Redeploy」，稍待一段時間就完成部署回復囉！不過比較可惜的是，Azure 在每次 Sync 時並沒有要求填入類似 Git 中 commit 的註解，所以當部署一多時，我們便很難明確指出每一次部署所新增或修改的內容，這也是美中不足之處。

![Step15](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-15-redeploy.png)

![Step16](https://raw.githubusercontent.com/hungys/azure-blog/master/media/08-using-dropbox-deploy-to-azure-websites/step-16-redeploy-success.png)

# 後記

今天這篇文章透過很簡短的篇幅介紹了網站服務與 Dropbox 的部署整合，而在筆者的觀點，它適合給沒有專業開發經驗但需要維護靜態網站的人員使用，透過這個功能便可以很輕鬆的同步網站到 Azure 上，而且還具備了回溯的功能。而對於專業的開發人員，還是建議選擇前篇所介紹的 Git 分散式版控的方式，或是搭配連續部署來使用，才是一個更好的部署流程。

# 參考資料

- New! Deploy to Windows Azure Web Sites from Dropbox, [http://azure.microsoft.com/blog/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox/](http://azure.microsoft.com/blog/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox/)