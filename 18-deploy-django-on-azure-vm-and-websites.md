Day 18: 部署 Django 網站 - 以 VM 及 Azure Websites 為例
================================

# 前言

![Django](https://raw.githubusercontent.com/hungys/azure-blog/master/media/18-deploy-django-on-azure-vm-and-websites/django-logo.png)

我們在前一篇文章中與讀者介紹了如何在 Azure Websites 上設定 Python 執行環境並簡單部署了一個符合 WSGI 規範的 Python 網站應用程式到網站服務上。而本文將更進一步的介紹當前最受 Python 開發者喜愛，在社群間也最為活躍的 Django 網頁開發框架，帶領讀者認識在 Azure 上部署 Django 網站的幾種可行方式。

# 從 VM 開始

第一種最直覺的做法便是建立一台全新的 Linux VM，在上面設定好 Python 的執行環境，並如同我們在此系列文章中曾經介紹的在 VM 上安裝 nginx 網站伺服器等等一系列的步驟，雖然所需花費的時間最多，但您卻也可以獲得較高的彈性與控制權。

在此，筆者假設您已經有一台 Ubuntu 14.04 的執行環境，並且在上面安裝了 nginx，且設定了反向代理到 gunicorn 的 port 上（也就是稍待會執行 Django 應用程式時所監聽的 port），若您還不熟悉相關的操作或設定，建議先行閱讀本系列的第 10、11 篇介紹 nginx 與 gunicorn 的文章。

首先，請您建立好一個虛擬執行環境 virtualenv 並使用 `pip` 來安裝 Django 套件，建議您先至 Django [官方網站](https://www.djangoproject.com/download/)查詢目前最新的穩定版本號，在此以 1.7 版為例：

```
(venv)$ pip install Django==1.7
```

若您想要透過原始碼來安裝，可以自行從 GitHub 上 clone 一份最新的原始碼：

```
(venv)$ git clone https://github.com/django/django.git
```

未確保您的 Django 有正確被安裝，可以在命令列輸入以下的 Python 程式來顯示出目前所安裝的 Django 版本號：

```
(venv)$ python -c "import django; print(django.get_version())"
1.7
```

接下來，我們可以透過 Django 內建的 `django-admin.py` 來協助我們進行一些初始化的設定，並同時會產生一些基本的程式碼：

```
(venv)$ django-admin.py startproject <SITE NAME>
```

其中，`<SITE NAME>` 應該避免 Django 套件的相關 module 名稱例如「django」，而也應避免如「test」這種會與 Python 本身內建的 module 衝突的名稱。成功建立好一個 Django 專案後，在資料夾底下應該會自動產生以下檔案：

```
<SITE NAME>/
    manage.py
    <SITE NAME>/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

在 Django 所自動產生的檔案中，`manage.py` 是一個提供您與 Django 專案互動的工具程式，`settings.py` 則是關於專案的相關設定，`urls.py` 為專門設定網址路由的程式，最後 `wsgi.py` 則是一個與 WSGI 規範相容的進入點，稍後我們使用 gunicorn 部署網站時也會直接使用到這個 module。關於 Django 的其他細節由於並非本文重點，可以參考官方網站所提供的[說明文件](https://docs.djangoproject.com/en)，其中還包括了更進階的資料庫設定等等。

在開發過程中，若您想要測試 Django 網站，可以直接使用本身所內置的輕量化測試用伺服器，您只需要透過 `manage.py` 就可以啟動測試伺服器，但務必注意請勿將此 development server 用於 production 環境中：

```
(venv)$ python manage.py runserver <PORT NUM>
```

最後，我們將直接示範使用先前所介紹過的 gunicorn 來部署 Django 網站，還記得 gunicorn 在起動時所帶的參數應該為該網站的 WSGI 進入點，在此便是剛剛所提到過的 `wsgi.py` 這個 module，請透過下列指令啟動網站伺服器：

```
(venv)$ gunicorn mysite.wsgi -b 127.0.0.1:5000
```

筆者在 nginx 上已經設定了反向代理，所以所有 80 port 的請求都會導向 port 5000，此時在瀏覽器上打開網站，應該可以成功看到「It worked!」，代表 Django 網站應用程式已經成功執行起來。

![It works](https://raw.githubusercontent.com/hungys/azure-blog/master/media/18-deploy-django-on-azure-vm-and-websites/django-works.png)

# 部署至 Websites

根據 Azure 官方網站中「[利用 Django 建立網站](http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-python-create-deploy-django-app/)」此篇文章的講解，您也可以將本地 repo 連同 django package 一起打包，並如同我們在前一篇介紹過的 web.config 設定檔一起 push 至網站服務的 Local Git，便可以完成 Django 網站應用程式的部署。**然而，這個方法筆者嘗試了多次仍無法成功，若有最新進展會持續更新本文。**

# 從 Gallery 建立

最後，也是最簡單的一種方法，是透過 Azure Websites 的 Gallery 來建立 Django 網站，您只需要在建立網站時選擇**「From Gallery」**，便可以在**「App Framework」**分類底下找到 Django，不過目前官方僅支援到 1.4 版。透過這個方法建立網站後，您不需要再做任何其他設定，便可以將 Django 網站部署在 Websites 服務上。

![From gallery](https://raw.githubusercontent.com/hungys/azure-blog/master/media/18-deploy-django-on-azure-vm-and-websites/from-gallery.png)

# 參考資料

- 利用 Django 建立網站, [http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-python-create-deploy-django-app/](http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-python-create-deploy-django-app/)
- Writing your first Django app, [https://docs.djangoproject.com/en/1.7/intro/tutorial01/](https://docs.djangoproject.com/en/1.7/intro/tutorial01/)