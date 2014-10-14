Day 16: 在 Azure VM 上安裝 IPython Notebook
====================================

# 前言

今天要來與讀者介紹一個比較輕鬆、有趣的主題，但嚴格來說是也沒有跟 Azure 直接相關，不過我們還是會在先前建立在 Azure 上的 Ubuntu VM 來示範如何安裝 IPython Notebook 以及其基本的使用方式。

# 什麼是 IPython?

即使讀者沒有實際動手寫過 Python，應該也知道它是近年來相當熱門的語言，而且提供了互動式的直譯器，而今天要介紹的主角 [IPython](http://ipython.org/)，本身並不是一個新的語言，而是提供了一個更強大的互動式界面，甚至可以產生圖形輸出，以及其他對平行運算的支援等等，並且提供了所謂 Notebook 的網頁界面可以使用。

在 Notebook 所提供的工作環境之下，這些 Notebook 文件可以包含任何文字、數學公式、輸入程式碼、結果、圖形、視訊，甚至是搭配 numpy、scipy、matplotlib 等等強大的 Python 科學運算與繪圖函式庫來使用。

# 安裝 IPython Notebook

首先，若讀者手邊沒有可以使用的 VM 環境的話，可以使用下面這行指令來透過 CLI 快速建立一台 Ubuntu 14.04 的虛擬機器：

```
$ azure vm create ironman-ipython b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140927-en-us-30GB hungys --location "East Asia" --ssh
```

由於 IPython Notebook 本身是一個網頁界面，所以我們事先把 port 打開來以便等等可以連線進入，IPython Notebook 預設的監聽 port 是 8888，不過待會我們會設定為 port 80，所以我們使用下面的指令允許連線：

```
$ azure vm endpoint create ironman-ipython 80 --endpoint-name HTTP
```

接下來就可以連線到 VM 開始安裝 IPython 的相關套件。接下來我們可以直接透過 `apt-get` 來安裝所需的套件，當然您也可以選擇使用 `pip`：

```
$ sudo apt-get install python-matplotlib python-tornado ipython ipython-noteboo
```

關於其他環境例如 Windows 的安裝方式，可以自行參考[官方網站](http://ipython.org/ipython-doc/stable/install/index.html)的說明文件。

# 設定 IPython Notebook

安裝完成後，首先我們要先建立一個新的 Notebook 組態檔：

```
$ ipython profile create ironman
[ProfileCreate] Generating default config file: u'/home/hungys/.ipython/profile_ironman/ipython_config.py'
[ProfileCreate] Generating default config file: u'/home/hungys/.ipython/profile_ironman/ipython_notebook_config.py'
```

接著，我們要在組態檔中設定 Notebook 的密碼以策安全，但這個密碼必須是經過加密的，在此我們使用 IPython 所提供的公用程式來產生：

```
$ python -c "import IPython;print IPython.lib.passwd()"
Enter password: 
Verify password: 
sha1:9c950cfc1806:518179afxxxxxxef4466xxxxxx74e60528xxxxxx
```

最後產生出來的整串 SHA1 便是加密過後的結果，請複製起來以便待會寫進組態檔中。接著我們將要開始編輯組態檔，該檔案的名稱是 `ipython_notebook_config.py`，以筆者的安裝路徑為例，這個組態檔會存在於 `/home/hungys/.ipython/profile_ironman/` 之下。使用文字編輯器開啟之後，請依照以下的範例來進行設定，並請將預設註解的部分取消註解：

```
c = get_config()

c.IPKernelApp.pylab = 'inline'
c.NotebookApp.password = u'sha1:9c950cfc1806:518179afxxxxxxef4466xxxxxx74e60528xxxxxx'

c.NotebookApp.ip = '*'
c.NotebookApp.port = 80
c.NotebookApp.open_browser = False
```

# 執行 IPython Notebook

到此為止已經完成了幾本所需的設定，請使用以下指令啟動 IPython Notebook 伺服器：

```
$ sudo ipython notebook --profile=ironman
```

接著便可以打開瀏覽器進入到我們的伺服器，若有成功設定的話應該可以看到一個登入畫面，代表 IPython Notebook 已經安裝完成！

![Login](https://raw.githubusercontent.com/hungys/azure-blog/master/media/16-install-ipython-notebook-on-azure/ipython-notebook-login.png)

![Index page](https://raw.githubusercontent.com/hungys/azure-blog/master/media/16-install-ipython-notebook-on-azure/ipython-notebook-index.png)

在主畫面上點選**「New Notebook」**便可以開始使用 IPython 的互動式直譯器，在上面您就可以盡情的輸入指令或程式碼，甚至是善用 IPython 的特性來進行科學運算與繪圖。

![Hello IPython](https://raw.githubusercontent.com/hungys/azure-blog/master/media/16-install-ipython-notebook-on-azure/ipython-notebook-helloworld.png)

以我們剛剛有安裝的繪圖函式庫 matplotlib 為例，我們可以透過簡單的指令來嘗試在 IPython Notenbook 上繪圖，在此我們示範產生 30 個隨機點來產生一個二維坐標圖形：

```
plot(randn(30).cumsum(), color='k', linestyle='dashed', marker='o')  
```

按下**「Shift+Enter」**後便可以立即看到結果，是不是相當方便呢？在 IPython 的互動式環境中，更支援了 auto-completion，您可以按下 Tab 來預覽該 local namespace 底下的提示，若對 library 或 function 有任何疑問的話，還可以直接在後面輸入問號 `?` 來瀏覽相關說明。

![Plot](https://raw.githubusercontent.com/hungys/azure-blog/master/media/16-install-ipython-notebook-on-azure/ipython-notebook-plot.png)

關於更多 matplotlib 的使用，可以參考[官方網站](http://matplotlib.org/index.html)的說明文件。

# 後記

本文簡短的介紹了 IPython Notebook 以及基本的安裝及操作，但它本身其實還有非常多可玩性待讀者更進一步摸索，筆者最讚嘆的地方莫過於 IPython 與科學運算的函式庫整合之下的超強運算繪圖能力，若讀者有興趣的話可以對以下幾個 library 做更深入的研究，相信可以更靈活地運用 IPython Notebook。

- **matplotlib**: [http://matplotlib.org](http://matplotlib.org)
- **numpy**: [http://www.numpy.org](http://www.numpy.org)
- **scipy**: [http://www.scipy.org](http://www.scipy.org)

# 參考資料

- Azure 上的 IPython Notebook, [http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-python-ipython-notebook/](http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-python-ipython-notebook/)
- 利用 Python 進行數據分析, [http://blog.csdn.net/ssw_1990/article/details/23739953](http://blog.csdn.net/ssw_1990/article/details/23739953)
- IPython Documents, [http://ipython.org/ipython-doc/stable/install/index.html](http://ipython.org/ipython-doc/stable/install/index.html)