Day 11: 在 Azure VM 上使用 gunicorn 部署 Flask 網站
===================================

# 前言

在上一篇文章中，我們與讀者介紹了如何在 Azure VM 上架設 nginx 及 MongoDB，並稍微介紹了一下 MongoDB 的 CRUD 操作。而大部份的情況下，Client 端的 App 都會有操作資料庫的需求，通常我們會寫一個 Web Service 來應付這樣的需求，本文首先將介紹如何使用 Python 搭配 pymongo 來操作 MongoDB，接著使用 Flask framework 來建立一個輕量化的 REST API，最後透過 gunicorn 與 nginx 來進行部署。

# 準備 Python 執行環境

本文所要介紹的 pymongo 是一個 Python 的 library，所以我們必須先在 VM 上準備好 Python 的執行環境。以 Ubuntu 14.04 為例，一開始就已經預先安裝好 Python 2.7.6 及 Python 3.4.0 兩個版本，分別可以在終端機中輸入 `python` 及 `python3` 來啟動互動式的 intepreter。在此先事先說明，接下來的範例及操作會以 **Python 2.7.6** 為例，雖然筆者是先從 Python 3 開始學的，但感覺起來寫 Python 2.7 的開發者比例及相關資源依舊比較豐富。

在安裝 pymongo 套件前，我們必須先裝好 Python 的 package 管理工具，在此會以 pip 為例，而 pip 必須先安裝好相關的套件並透過 `easy_install` 來安裝：

```
$ sudo apt-get install python-setuptools python-dev build-essential
$ sudo easy_install pip
```

此時讀者已經可以開始透過 `pip` 來快速安裝相關的函式庫，不過在這之前還要先介紹一下 virtualenv。在開發 Python 應用程式時，可能有時會面臨到不同的程式需要使用到同一套件但不同版本號的問題，甚至是一些套件之間所產生的衝突，所以如果能夠建立獨立的虛擬環境會方便許多，這就是 virtaulenv 的功用，讀者可以自行選擇透過 `easy_install` 或是 `apt-get` 來安裝：

```
$ sudo apt-get install python-virtualenv
$ sudo easy_install virtualenv
```

接下來我們便可以建立並切換到所需要的資料夾下並建立一個虛擬開發環境：

```
$ mkdir demo
$ cd demo
$ virtualenv venv
New python executable in venv/bin/python
Installing setuptools, pip...done.
```

上述的「venv」是一個可以自定的名稱，作為虛擬環境的簡稱。接著我們便可以切換到該執行環境，請讀者自行替換掉 venv 為自己剛剛所命的名稱：

```
$ source venv/bin/activate
(venv)$
```

預設情況下終端機會提示目前所在的虛擬環境，將可以方便我們識別。啟動虛擬環境後除了會換掉 $PATH 環境變數之外，還會把安裝的套件放到 `venv/lib/python2.7/site-packages` 目錄下，當虛擬環境中的 Python 執行時，便會使用該環境下的套件，所以讀者可以透過這樣的方式在不同的虛擬環境中安裝個別需要的套件及版本。若要退出虛擬環境，只需輸入 `deactivate` 即可。

# 使用 pymongo 操作 MongoDB

pymongo 是官方推薦在 Python 底下操作 MongoDB 的套件，首先我們可以透過 `pip` 套件管理工具來安裝，若是在虛擬環境下安裝請不要再輸入 `sudo` 了：

```
(venv)$ pip install pymongo
Downloading/unpacking pymongo
  Downloading pymongo-2.7.2.tar.gz (381kB): 381kB downloaded
  Running setup.py (path:/home/hungys/demo/venv/build/pymongo/setup.py) egg_info for package pymongo
    
Installing collected packages: pymongo
...
```

若要列出目前環境下所安裝的套件，可以透過 `pip list` 指令來查詢：

```
(venv)$ pip list
argparse (1.2.1)
pip (1.5.4)
pymongo (2.7.2)
setuptools (2.2)
wsgiref (0.1.2)
```

若讀者有興趣的話可以使用 `deactivate` 指令退出環境後再輸入 `pip list`，可以發現並不會看到我們剛剛在虛擬環境下所安裝的 pymongo，這就時 virtualenv 的妙用。

接下來我們直接進入 `python` 的互動式執行環境中來練習使用 pymongo 操作 MongoDB，首先先 import 接著建立一個連線到本機的資料庫：

```
>>> import pymonogo
>>> conn = pymongo.Connection('localhost', 27017)
```

嘗試獲取該伺服器下的所有資料庫列表：

```
>>> conn.database_names()
[u'azure', u'admin', u'local', u'test']
```

接著連接到先前所建立好的 azure 資料庫，驗證後獲取該資料庫下的所有 collection 名稱：

```
>>> db = conn.azure
>>> db.authenticate('azure', 'azure')
True
>>> db.collection_names()
[u'product', u'system.indexes']
```

接下來連接到 product collection 並透過 `find_one` 來獲取一筆 document：

```
>>> products = db.product
>>> products.find_one()
{u'rating': 10.0, u'_id': ObjectId('5433d6b4ef08f752c1d1809c')}
```

透過 `find()` 方法可以獲取該 collection 下的所有 document，但其實他回傳的是一個 Cursor object，在此我們透過 for 來將所有 document 印出：

```
>>> type(products.find())
<class 'pymongo.cursor.Cursor'>
>>> for i in products.find():
...     print i
... 
{u'rating': 10.0, u'_id': ObjectId('5433d6b4ef08f752c1d1809c')}
{u'url': u'http://azure.microsoft.com/zh-tw/services/websites/', u'rating': 8.0, u'_id': ObjectId('5433d76def08f752c1d1809e'), u'name': u'Websites', u'pricing': u'http://azure.microsoft.com/zh-tw/pricing/details/websites/'}
{u'rating': 5.0, u'_id': ObjectId('5433d772ef08f752c1d1809f'), u'name': u'Mobile Services', u'newfield': u'newvalue'}
{u'url': u'http://azure.microsoft.com/zh-tw/services/notification-hubs/', u'rating': 8.0, u'_id': ObjectId('5433d778ef08f752c1d180a0'), u'name': u'Notification Hubs', u'pricing': u'http://azure.microsoft.com/zh-tw/pricing/details/notification-hubs/'}
```

若要針對特定條下（即 criteria）來查詢的話，其實與 mongo shell 的操作方式大同小異：

```
>>> products.find_one({"name": "Mobile Services"})
{u'rating': 5.0, u'_id': ObjectId('5433d772ef08f752c1d1809f'), u'name': u'Mobile Services', u'newfield': u'newvalue'}
```

最後則是基本的新增、修改、刪除，基本上如果熟悉了 mongo shell 的操作方式，應該也會很容易上手，以下將示範基本的操作，更細節的使用方法可以參考官方的[說明文件](http://api.mongodb.org/python/current/)：

```
>>> products.insert({"name": "Redis Cache", "rating": 8})
ObjectId('543602a1acfac334e154253a')
>>> products.update({"name": "Redis Cache"}, {"$set": {"url": "http://azure.microsoft.com/zh-tw/services/cache/"}})
>>> products.find_one({"name": "Redis Cache"})
{u'url': u'http://azure.microsoft.com/zh-tw/services/cache/', u'rating': 8, u'_id': ObjectId('543602a1acfac334e154253a'), u'name': u'Redis Cache'}
>>> products.remove({"name": "Redis Cache"})
>>> products.find({"name": "Redis Cache"}).count()
0
```

# 使用 Flask 建立輕量化 REST API

Flask 是 Python 世界中一個用來作 Web 相關開發的 Micro Framework，之所以稱「Micro」是因為它本身相當輕量化，本文將使用它來建造一個簡單的 REST API，提供一個接口讓其他裝置能夠存取 MongoDB 中的資料。首先，同樣先透過 `pip` 來安裝 Flask 套件：

```
(venv)$ pip install flask
```

接著我們建立一個簡單的 Hello World 網站：

```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

上面的程式中先透過 `import` 匯入了 Flask 的 module，並建立一個 Flask 實體。接著透過 decorator 的方式定義了一個「/」的路由對應到 hello_world() 這個方法。最後則是在 `__name__ == '__main__'` 成立時，也就是程式是直接被 Python 直譯器執行而非被當作 module import 時才將 server 跑起來。

儲存之後，我們透過 Python 直譯器來執行，並將它丟到背景去跑：

```
(venv)$ python hello_world.py &
* Running on http://127.0.0.1:5000/
```

可以看到預設會 bind 到 port 5000，我們可以透過 `netstat -lnptu` 來驗證是否有 process 在 listen 該 port：

```
(venv)$ netstat -lnptu
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -               
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -               
tcp        0      0 10.62.144.55:16001      0.0.0.0:*               LISTEN      -               
tcp        0      0 127.0.0.1:5000          0.0.0.0:*               LISTEN      13612/python    
tcp        0      0 0.0.0.0:27017           0.0.0.0:*               LISTEN      -               
tcp6       0      0 :::80                   :::*                    LISTEN      -               
tcp6       0      0 :::22                   :::*                    LISTEN      -               
udp        0      0 0.0.0.0:60172           0.0.0.0:*                           -               
udp        0      0 0.0.0.0:68              0.0.0.0:*                           -               
udp6       0      0 :::31555                :::*                                -               
```

接著使用 `curl` 來存取剛剛所建立的網站，若順利的話應該會看到回傳「Hello World!」字串：

```           
(venv)$ curl localhost:5000
127.0.0.1 - - [09/Oct/2014 04:06:23] "GET / HTTP/1.1" 200 - # log from Flask
Hello World!
```

其中有一行 request log 是剛剛跑在背景的 Flask 程式所輸出到 console 上的訊息，到這邊讀者可以發現，Flask 本身就已經內建了輕量化開發測試專用的 web server，我們還沒有使用到昨天所安裝的 nginx 就可以來測試剛剛所建立的 Flask 網站。

關於 Flask 的細節在本系列文章中不多談，可以直接查閱[官方文件](http://flask.pocoo.org/docs/)，以下我們將直接實作一個簡單的 REST API 並透過上面介紹得 pymongo 存取 MongoDB 中的資料。

```
from flask import Flask, make_response
import pymongo
import json

app = Flask(__name__)
db = pymongo.Connection('localhost', 27017)

@app.route('/api/products')
def get_all_products():
    collection = db.azure.product
    products = list(collection.find({}, {"_id": 0}))

    resp = make_response(json.dumps(products), 200)
    resp.headers["Content-Type"] = "application/json"

    return resp

if __name__ == '__main__':
    app.run()
```

上面的程式碼的細節我們在先前都已經介紹過了，這次只是將它們整合起來寫成一個 REST API，唯一比較特別的是從 MongoDB 取出 document 時，筆者在 `find()` 方法中加入了 projection 的參數，這裡的 projection 與我們昨天在 mongo shell 中所介紹的用途及用法相同，由於預設的 _id 欄位存放的 ObjectId 型別無法直接被序列化為 json，所以筆者在此刻意將它設為隱藏不回傳。而取出所有 document 並轉成 list 後，便可以透過 make_response 這個 helper method 來建立並回傳一個 HTTP response。

到此我們先將這個程式擱置，先進入下一個小節設定好 gunicorn 部署之後再來進行測試。

# 使用 nginx+gunicorn 部署網站

gunicorn 是一個開源的 Python WSGI HTTP 伺服器，雖然我們剛剛有提到 Flask 本身已經內建了 web server 的功能，但基本只是開發測試堪用而已，所以這邊我們將介紹如何搭配 gunicorn 來使用，首先可以透過 pip 來安裝：

```
(venv)$ pip install gunicorn
```

接著我們要對剛剛的檔案加入兩行程式碼來為 gunicorn 的代理做設定：

```
from werkzeug.contrib.fixers import ProxyFix # new

app = Flask(__name__)
db = pymongo.Connection('localhost', 27017)

@app.route('/api/products')
...
...

app.wsgi_app = ProxyFix(app.wsgi_app) # new

if __name__ == '__main__':
...
...
```

習慣上我們會在 gunicorn 前再加上一層靜態伺服器，在這邊就以昨天帶領讀者的 nginx 為例，首先先在 `/etc/nginx/sites-available/` 底下新增一個虛擬伺服器的設定檔：

```
$ sduo vim /etc/nginx/sites-available/demo
```

接著在這個檔案中加入以下內容，基本上這邊的設定是指定 listen 80 port，並且做一個反向代理到 gunicorn 所監聽的 port 5000：

```
server {
    listen 80;
    server_name ironman-nginx.cloudapp.net;

    root /home/hungys/demo;

    location / {
        proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        if (!-f $request_filename) {
            proxy_pass http://127.0.0.1:5000;
            break;
        }
    }
}
```

接著，要將此設定檔建立一個 symbolic link 到 `/etc/nginx/sites-enabled` 資料夾底下才會生效，記得存檔後要將 nginx 重啟：

```
$ sudo ln -s /etc/nginx/sites-available/demo /etc/nginx/sites-enabled/demo
$ sudo service nginx restart
```

此時我們從瀏覽器進入我們的伺服器時，應該會出現「502 Bad Gateway」的錯誤，如果還是看到 nginx 的設定頁面代表設定上有問題。

現在，我們只要將 gunicorn 啟動，nginx 就可以自動把請求轉發到 gunicorn 所監聽的 port 上，不過在這之前請讀者修改一下 server code 的檔名，以筆者先前所取的 `demo_server.py` 為例，請改成 `demo_server_wsgi.py`，否則 gunicorn 會無法正常啟動。這個問題先前筆者在線上服務的 server 沒有遇過，不過不曉得為什麼這次安裝就遇到這個問題，還是在 [stackoverflow](http://stackoverflow.com/questions/24488891/gunicorn-errors-haltserver-haltserver-worker-failed-to-boot-3-django) 發現有人遇到同樣問題才解決的。修改完成後透過下面的指令將 server 跑起來，請務必注意這次不需要將 `_wsgi` 帶入：

```
$ gunicorn demo_server:app -b 127.0.0.1:5000
```

最後，順便向讀者介紹一套用來測試 REST API 相當好用的工具 [Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm)，他算是 Chrome 的一個外掛套件，開啟後輸入「[http://ironman-nginx.cloudapp.net/api/products](http://ironman-nginx.cloudapp.net/api/products)」發送一個 GET 請求便可以來驗證我們的 REST API 是否有正常運作。

![POSTMAN](https://raw.githubusercontent.com/hungys/azure-blog/master/media/11-deploy-flask-by-gunicorn-on-azure/postman.png)

# 後記

本文簡單的從操作 MongoDB 用的 pymongo、Python 的網頁框架 Flask 一直到與 nginx、gunicorn 的部署整合做了全面但粗淺的介紹，礙於篇幅的關係其中還有許多細節待讀者自行探索，例如以下關鍵字：

- pymongo 的進階操作
- 在 Flask 中實作 POST、PUT、DELETE 的 API 以及其他進階功能
- gunicorn、nginx 設定檔優化及調校（如 worker 數量...等）
- 使用 supervisor 管理 python process

# 參考資料

- flask+gevent+gunicorn+nginx 初試, [http://blog.csdn.net/angel22xu/article/details/25638477](http://blog.csdn.net/angel22xu/article/details/25638477)
- pymongo 官方文件, [http://api.mongodb.org/python/current/](http://api.mongodb.org/python/current/)
- Flask 官方文件, [http://flask.pocoo.org/docs/](http://flask.pocoo.org/docs/)