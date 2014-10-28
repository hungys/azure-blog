Day 28: 在 Azure 上使用 Docker
============================

# 前言

Docker 是最近相當熱門的一個名詞，它是一個基於 Linux Container 的輕量化的虛擬技術，而微軟也相當積極與 Docker 合作，在 Azure 上支援這個正夯的技術，並且提供簡便的方式來建立 Docker Host，本文將會介紹如何在 Azure 上使用 Docker。

# Docker 簡介

![Logo](https://raw.githubusercontent.com/hungys/azure-blog/master/media/28-using-docker-on-azure/docker-logo.png)

Docker 是一個 open-source 的專案，主要的特點是能將應用程式包裝在一個 LXC (Linux Container) 容器中，當這些應用被包裝進容器後，部署、遷移都變得更為簡單。與傳統的虛擬化技術相比，虛擬機器需要安裝作業系統才能執行應用程式，而 Container 則不需要安裝作業系統就能執行應用程式。Container 技術是一種在 OS 內的 Kernel 層所打造虛擬執行環境，所以 Container 彼此之間共用了 Host OS 的 Kernel，但透過 namespace 區分來達到隔離每個容器的目的。

![Concept](https://raw.githubusercontent.com/hungys/azure-blog/master/media/28-using-docker-on-azure/docker-concept.png)

本文並不會針對 Docker 這個技術做深入的介紹，主要著重在 Azure 對於 Docker 所提供的支援做介紹，若讀者想要快速瞭解他與傳統虛擬化技術的差異，可以參考 iThome 上「[10個Q&A快速認識Docker](http://www.ithome.com.tw/news/91847)」這篇文章。

# 在 Azure 上建立 Docker Host

Docker 可以執行在 Linux 作業系統之下，所以若要在 Azure 上使用 Docker，您也可以自行建立 VM，並在上面安裝 Docker Deamon 並執行。而微軟在正式宣佈與 Docker 密切合作後，推出了對 Docker 的直接支援，您除了可以在 Preview Portal 上建立 Docker VM Extension 外，若選擇透過 xplat-cli 甚至可以透過一行指令就部署指定的 Linux 作業系統，並且預載了 Docker Deamon 可以直接使用。

xplat-cli 的安裝與設定請參考先前的文章，在此假設讀者已經熟悉相關的指令。若要建立 Docker Host，首先請先確認您所要安裝的 Linux 作業系統，可以透過 `azure vm image list` 將所有映像檔列出：

```
$ azure vm image list | grep Ubuntu-14_04
...
...
...
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140927-en-us-30GB                                 Public    Linux
```

在此我們就選用最新的 Ubuntu 14.04 作為 OS，接下來便可使用 `azure vm docker create` 的指令來建立 Docker Host，這個指令的使用方法跟建立 VM 相當類似，但它會幫我們設定好相關的 Docker 執行環境：

```
azure vm docker create -e 22 -l "East Asia" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140927-en-us-30GB" <username> <password>
```

到此，我們已經成功部署好一台 VM 並自動設定好相關的 Docker 執行環境。

# 在本地端安裝 Docker Client

Docker 的核心在於 Docker Deamon，而若要管理 Docker 服務則需要使用 Docker Client 來進行，以 Mac OS X 為例，可以使用 homebrew 快速安裝：

```
brew install docker
```

安裝完成後，我們便可以嘗試使用 `docker` 指令來操作 Azure 上的 Docker Host，首先先以查詢 Docker 的資訊為例：

```
$ docker --tls -H tcp://ironman-docker.cloudapp.net:4243 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
Operating System: Ubuntu 14.04.1 LTS
WARNING: No swap limit support
```

請務必記住若要操作遠端的 Docker 環境，必須下 `-H` host 參數，否則預設會是連線到本地的 Docker 環境。（註：若要在 Mac 上建立 Docker 環境請參考 [boot2docker](http://boot2docker.io/)）

# 安裝虛擬化應用程式

在 Docker 中應用程式是被包裝在 Container 中的，您可以自己手動安裝建立這些 Container，或是透過 Dockerfile 的設定檔來 build 出一個 Container image，而 Docker 官方也建立了一個完整的生態系讓您可以分享您的 Container image 或是將別人的 image pull 下來，可以前往[https://registry.hub.docker.com/](https://registry.hub.docker.com/) 瞧瞧。

以 redis 為例，官方便提供了 Dockerfile 在他們的 repo 中，若要使用的話只需要透過 `docker pull redis` 指令即可，再次提醒若要 pull 到遠端的 Docker Host 請指定 host 參數。

若不透過 pull 指令的話，也可以手動透過 Dockerfile 來建立 image，以 MongoDB 為例，您可以在 [GitHub](github.com/dockerfile/mongodb) 上找到其 Dockerfile。在這份 Dockerfile 中主要包含了一些安裝 MongoDB 所需的指令，但特別注意到 `FROM dockerfile/ubuntu` 這行，代表這個 Container 是基於另一個 ubuntu 的 Dockerfile，所以他們之間有 dependency 存在，基本上 Docker 的 Container 就是這種層層堆疊上去的概念。

接下來我們可以來嘗試使用這個 Dockerfile 來 build 出 image：

```
$ docker --tls -H tcp://ironman-docker.cloudapp.net:4243 build -t="dockerfile/mongodb" github.com/dockerfile/mongodb
Sending build context to Docker daemon 69.63 kB
Sending build context to Docker daemon
Step 0 : FROM dockerfile/ubuntu
Pulling repository dockerfile/ubuntu
b4e54ddfb2af: Download complete
511136ea3c5a: Download complete
d497ad3926c8: Download complete
ccb62158e970: Download complete
e791be0477f2: Download complete
3680052c0f5c: Download complete
22093c35d77b: Download complete
5506de2b643b: Download complete
b08854b89605: Download complete
d0ca2a3c0233: Download complete
1716e82f74f0: Download complete
b41d25703535: Download complete
e95dbc5735e1: Download complete
5992007b07de: Download complete
......
```

Docker Client 會依據 Dockerfile 來建立 image，並且會自動下載相關的 dependency。建立完成後可以使用 `images` 指令來瀏覽目前 Docker Host 上所擁有的所有映像檔：

```
$ docker --tls -H tcp://ironman-docker.cloudapp.net:4243 images
REPOSITORY                    TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
dockerfile/mongodb            latest              ac595fa4f77d        57 seconds ago      711.5 MB
dockerfile/ubuntu             latest              b4e54ddfb2af        2 days ago          419.7 MB
```

最後只需要使用 `run` 指令，就能夠將 Container 給執行起來，MongoDB 就會被執行在一個隔離的容器中了：

```
$ docker --tls -H tcp://ironman-docker.cloudapp.net:4243 run dockerfile/mongodb
mongod --help for help and startup options
2014-10-28T13:57:20.746+0000 [initandlisten] MongoDB starting : pid=1 port=27017 dbpath=/data/db 64-bit host=80811c03f82b
2014-10-28T13:57:20.747+0000 [initandlisten] db version v2.6.5
2014-10-28T13:57:20.747+0000 [initandlisten] git version: e99d4fcb4279c0279796f237aa92fe3b64560bf6
2014-10-28T13:57:20.748+0000 [initandlisten] build info: Linux build8.nj1.10gen.cc 2.6.32-431.3.1.el6.x86_64 #1 SMP Fri Jan 3 21:39:27 UTC 2014 x86_64 BOOST_LIB_VERSION=1_49
2014-10-28T13:57:20.748+0000 [initandlisten] allocator: tcmalloc
2014-10-28T13:57:20.748+0000 [initandlisten] options: {}
2014-10-28T13:57:20.771+0000 [initandlisten] journal dir=/data/db/journal
2014-10-28T13:57:20.771+0000 [initandlisten] recover : no journal files present, no recovery needed
2014-10-28T13:57:23.328+0000 [initandlisten] preallocateIsFaster=true 18.86
2014-10-28T13:57:25.734+0000 [initandlisten] preallocateIsFaster=true 15.2
2014-10-28T13:57:29.157+0000 [initandlisten] preallocateIsFaster=true 16.98
2014-10-28T13:57:29.157+0000 [initandlisten] preallocateIsFaster check took 8.385 secs
2014-10-28T13:57:29.158+0000 [initandlisten] preallocating a journal file /data/db/journal/prealloc.0
......
```

關於 Docker 的其他操作及進階使用本文並不會做更深入介紹，以下整理了一些相關資源供讀者參考：

- 在 Ubuntu 上安裝 Docker 及基本操作介紹, [http://hungmingwu-blog.logdown.com/posts/196996-introduction-to-docker](http://hungmingwu-blog.logdown.com/posts/196996-introduction-to-docker)
- 門外漢的 Docker 小試身手, [http://www.codedata.com.tw/social-coding/docker-layman-abc/]
- Docker - 能夠運行任何應用的 "PaaS" 雲, [http://www.yankay.com/docker-paas-for-any-application/](http://www.yankay.com/docker-paas-for-any-application/)
- 進階內容：深入淺出 Docker, [http://www.infoq.com/cn/dockers](http://www.infoq.com/cn/dockers)

# 參考資料

- Using the Docker VM Extension from Azure Cross-Platform Interface (xplat-cli), [http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-docker-with-xplat-cli/](http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-docker-with-xplat-cli/)
- Simplified Setup and Use of Docker on Microsoft Azure, [http://msopentech.com/blog/2014/06/09/docker-on-microsoft-azure/](http://msopentech.com/blog/2014/06/09/docker-on-microsoft-azure/)