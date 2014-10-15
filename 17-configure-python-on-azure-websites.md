Day 17: 在 Azure Websites 上設定 Python 執行環境
====================================

# 前言

過去說到寫動態網頁，一定會聯想到的是 PHP、ASP.NET 甚至是 JSP，但近幾年來也愈來愈多使用 Python 或 Ruby on Rails 的 Web Framework 來寫，而就筆者的觀察，許多年輕而且擁抱開源的公司尤其喜歡使用這兩種語言及相對應的網頁框架來寫動態網頁，也因為如此，Azure 也加入了許多針對 Python 的支援，本文將介紹的是如何在 Azure Websites 上設定 Python 的執行環境。

# 建立 Websites

首先，請讀者自行從 Management Portal 或是 CLI 建立一個空的 Websites 網站服務，接著建立一個 Local Git 並 clone 到本機端，關於 Local Git 的設定可以參考本系列文章的第七篇曾經介紹過的：在 Azure 上使用 Git 分散式版本控制。

此外，也要請讀者在 Management Portal 該網站服務底下的**「Configure」**分頁，選擇您所要使用的 Python 版本，目前 Azure Websites 所支援的版本分別是 2.7.3 及 3.4.0。

![Choose Python version](https://raw.githubusercontent.com/hungys/azure-blog/master/media/17-configure-python-on-azure-websites/choose-python-version.png)

# 從 WSGI 說起

在開始部署 Python 網頁應用程式之前，首先應該先從 WSGI 開始說起，WSGI 是「Web Server Gateway Interface」的縮寫，它是由 Python 官方在 [PEP333](http://www.python.org/dev/peps/pep-0333/) 所定義出來的一個網頁開發標準。

簡單來說，WSGI 為 Python 定義了網頁程式和伺服器溝通的介面，它就類似 CGI (Common Gateway Interface) 的作用，而且同樣透過環境變數來取得外部資訊，例如 REQUEST_METHOD、SERVER_NAME 等等。而在 PEP333 中，也定義了 WSGI 以一個固定型式的函式與伺服器溝通，以下便是一個簡單的例子：

```
def simple_app(environ, start_response):
    """Simplest possible application object"""
    status = '200 OK'
    response_headers = [('Content-type','text/plain')]
    start_response(status, response_headers)
    return ['Hello world!n']
```

這個函式的介面可將環境變數從 environ 傳進去，start_response 則是伺服器端所提供，當要開始進行 response 時就呼叫該參數。而對於一個 WSGI 程式，它必須是一個 callable 的對象，並且可以傳入上面提到的兩個參數，它可以是一個函式，也可以是一個有實作 `__call__` 的類別。更多關於 WSGI 的定義與規範，包括 Middleware 中介層的定義，可以參考 [PEP333](http://www.python.org/dev/peps/pep-0333/) 中更詳細的敘述。

# 準備測試內容

接下來我們要開始嘗試在 Websites 上部署一個 Python 的網站，首先我們就依照 PEP333 的範例建立一個 WSGI 的處理程式，並儲存在根目錄下的 `ConfigurePython\ConfigurePython.py`，若您要自行取檔名，在之後的相關設定中記得自行替換掉相關字串。

```
def application(environ, start_response):
    status = '200 OK'
    response_headers = [('Content-type', 'text/plain')]
    start_response(status, response_headers)
    yield 'Hello from Azure Websites\n'
```

這段程式碼基本上就是對於任何 request，都回傳一段「Hello from Azure Websites」的字串，並且在 WSGI 的規範下實作，作為一個合法的程式進入點。

# 設定網站組態

接著，我們要設定網站的運作組態，在此有兩種方法可以選擇，第一種是透過 Management Portal 的介面來操作，但筆者推薦建立一個 `web.config` 檔案，如此一來網站在移植或搬移時會較為方便且具備高可攜性。請將以下設定檔存放在您的根目錄底下的 `web.config` 中：

```
<configuration>
      <add key="pythonpath" value="D:\home\site\wwwroot\ConfigurePython" />
      <add key="WSGI_HANDLER" value="ConfigurePython.application" />
  </appSettings> 
  <system.webServer>
     <handlers>
            <add name="PythonHandler" 
        verb="*" path="handler.fcgi" 
        modules="FastCgiModule" 
        scriptProcessor="D:\Python27\Python.exe|D:\Python27\Scripts\wfastcgi.py" 
        resourceType="Either" />
     </handlers>
     <rewrite>
        <rules>
                <rule name="Configure Python" stopProcessing="true">
                    <match url="(.*)" ignoreCase="false" />
                    <conditions>
                        <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                    </conditions>
                    <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
                </rule>
        </rules>
        </rewrite>
    </system.webServer>
</configuration> 
```

上面的設定檔中，主要是定義了相關的 Python 執行組態以及 WSGI 的 handler，在完成之前，請務必記得使用 `touch handler.fcgi` 指令來預留一個空白的 handler 檔案。到此為止，您的根目錄底下應該會有以下三個檔案：

- ConfigurePython\ConfigurePython.py
- handler.fcgi
- web.config

最後，您只要將 repo 底下的內容 push 到 Websites 上的 Local Git，就成功完成了一個符合 WSGI 規範的 Python 網頁應用程式。此時在瀏覽器中輸入您的網站服務的網址，對於一個任意的 request 應該都會看到回傳「Hello fron Azure Websites」的結果！到此，我們已經完成了所有步驟，而在往後的系列文章中，我們還會針對 django 網站的部署做更深入的介紹。

![Hello Python](https://raw.githubusercontent.com/hungys/azure-blog/master/media/17-configure-python-on-azure-websites/hello-world.png)

# 參考資料

- 在 Azure 網站上設定 Python, [http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-python-configure/](http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-python-configure/)
- 化整為零的次世代網頁開發標準: WSGI, [http://blog.ez2learn.com/2010/01/27/introduction-to-wsgi/](http://blog.ez2learn.com/2010/01/27/introduction-to-wsgi/)
- PEP 333 -- Python Web Server Gateway Interface v1.0, [http://legacy.python.org/dev/peps/pep-0333/](http://legacy.python.org/dev/peps/pep-0333/)