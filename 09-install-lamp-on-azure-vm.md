Day 9: 在 Azure 上架設 Linux VM 及 LAMP 服務
================================

# 前言

![LAMP](https://raw.githubusercontent.com/hungys/azure-blog/master/media/09-install-lamp-on-azure-vm/lamp-logo.png)

LAMP 這個名詞大家應該不陌生，其實就是 **Linux + Apache + MySQL + PHP** 的簡稱，因為許多人使用這樣的組合因而得名，而目前許多中小型服務也是基於這樣的架構。另外，網路上關於 LAMP 的資源相當多，所以大多數初學者也都會使用這樣的組合來學習架設動態網站。本文將會帶領讀者在 Azure 上使用 IaaS 服務建立一台 Linux 的虛擬機器，並在上面建置 LAMP 的環境。

# 使用 Management Portal 圖形化界面

若要在 Azure 上建立一台虛擬機器最直覺的方式便是透過 Management Portal，這部分的操作由於相當簡單，筆者並不打算一步一步細講，重點會著重在使用 CLI 來操作相關指令。在 Portal 中依序點選**「New」**>**「Compute」**>**「Virtual Machine」**>**「Quick Create」**，從這裡就可以直接選擇微軟事先準備好的 Image 來安裝，在接下來的內容將會以 Ubuntu 為例。

![Quick Create](https://raw.githubusercontent.com/hungys/azure-blog/master/media/09-install-lamp-on-azure-vm/quick-create.png)

如果在 Quick Create 的地方沒有找到想要使用的映像檔，可以選擇從**「From Gallery」**來選擇欲安裝的作業系統，裡面除了今天要使用到的 Ubuntu 之外，也有 CentOS-based 及 SUSE 等 Linux 選擇，當然也包括了各式 Windows Server 映像檔甚至是有預裝軟體的作業系統等等。

![From Gallery](https://raw.githubusercontent.com/hungys/azure-blog/master/media/09-install-lamp-on-azure-vm/from-gallery.png)

# 使用 CLI 建立虛擬機器

除了透過上一節提到的圖形化界面可以輕鬆建立 VM 之外，本文將要帶領讀著熟悉先前所介紹做的 xplat-CLI，使用輸入指令的方式來建立一台虛擬機器，接下來的步驟都可以在終端機中完成。

在建立 VM 之前，由於我們需要使用到微軟預先準備好的 Image 檔案，所以讀者可以透過 `azure vm image list` 的指令來將目前所有可用的映像檔列出，也可以使用 `grep` 指令來過濾出我們所要找的 Ubuntu：

```
$ azure vm image list | grep Ubuntu-14_04
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140414-en-us-30GB                                   Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140414.2-en-us-30GB                                 Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140416.1-en-us-30GB                                 Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140528-en-us-30GB                                   Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140606.1-en-us-30GB                                 Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140618.1-en-us-30GB                                 Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-en-us-30GB                                   Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140909-en-us-30GB                                 Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140924-en-us-30GB                                 Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140926-en-us-30GB                                 Public    Linux  
data:    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140927-en-us-30GB                                 Public    Linux
```

中間一長串就是 Image 的名稱，稍後可以透過這個名稱來建立 VM，在此我們將選擇最新的版本 `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140927-en-us-30GB` 來安裝使用。

要建立一台全新的虛擬機器可以透過 `azure vm create` 的指令來達成，並依序需要「Host name」、「Image name」及「Username」三個參數。另外，雖然文件中並沒有提到「location」是必要的，但若沒有設定好地區的話會出現失敗訊息。最後要特別注意的是，由於 Linux VM 預設沒有開啟 SSH 的功能，請務必加上 `--ssh` 的選項，否則等會會無法連線進入該 VM。其餘細部設定請透過 `--help` 自行查詢。

```
$ azure vm create ironman-lamp b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140927-en-us-30GB ironman --location "East Asia" --ssh
info:    Executing command vm create
+ Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_1-LTS-amd64-server-20140927-en-us-30GB
Enter VM 'ironman' password:
Confirm password: 
+ Looking up cloud service                                                     
info:    cloud service ironman-lamp not found.
+ Creating cloud service                                                       
+ Retrieving storage accounts                                                  
+ Creating VM                                                                  
info:    vm create command OK
```

下完指令後稍待片刻，Azure 就會將一台全新裝有 Ubuntu 14.04 的 VM 部署在東亞的資料中心，過程中可以依序看到還自動建立了一個雲端服務以及儲存 VHD 用的儲存體。

建立完成之後，在此要向讀者說明，剛建立起來的 VM 會封鎖所有對內的連線，除了因為我們剛剛有下 `--ssh` 的選項所以已經幫我們預設打開 port 22。若要查詢目前所有開放的 endpoint，可以透過 `azure vm endpoint list` 的指令：

```
$ azure vm endpoint list ironman-lamp
info:    Executing command vm endpoint list
+ Getting virtual machines                                                     
data:    Name  Protocol  Public Port  Private Port  Virtual IP    EnableDirectServerReturn  Load Balanced
data:    ----  --------  -----------  ------------  ------------  ------------------------  -------------
data:    ssh   tcp       22           22            XX.XXX.XX.XX  false                     No           
info:    vm endpoint list command OK
```

最後，我們嘗試使用 SSH 來連進剛剛所建立的 VM 來驗證是否有成功部署起來，每一個 VM 都配有一組「<hostname>.cloudapp.net」，而 Host name 便是剛剛建立 VM 時所帶的參數：

```
$ ssh ironman@ironman-lamp.cloudapp.net
The authenticity of host 'ironman-lamp.cloudapp.net (XX.XXX.XX.XX)' can't be established.
RSA key fingerprint is 3b:20:40:3b:60:75:xx:xx:xx:xx:bf:ff:a4:fa:4f:96.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'ironman-lamp.cloudapp.net,23.101.12.26' (RSA) to the list of known hosts.
ironman@ironman-lamp.cloudapp.net's password: 
Welcome to Ubuntu 14.04.1 LTS (GNU/Linux 3.13.0-36-generic x86_64
```

到此，已經完成 VM 的部署了，比起過去還要自己從頭安裝還要輕鬆許多！

# 安裝 LAMP 服務

接下來，我們要在 Ubuntu 中安裝 Apache, MySQL 以及 PHP。如果讀者打算使用 apt-get 來分別安裝美個套件的話，可以使用下列指令：

```
$ sudo apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear
```

不過在此我們要介紹使用另一個 tasksel 的套件軟體安裝程式來透過一個指令就完成所有元件的安裝，Azure 所建立的 Ubuntu VM 已經內建了 tasksel，如果沒有的話可以透過 apt-get 來安裝：

```
$ sudo apt-get update
$ sudo apt-get install tasksel
```

確認已經安裝好 tasksel 後，輸入下面這行指令，就可以一次將 LAMP 所需的套件一次安裝完畢：

```
sudo tasksel install lamp-server
```

過程中會要求取一個 MySQL root 帳戶的密碼，輸入完畢之後就只需等待安裝程式完成安裝囉！

![LAMP Setup](https://raw.githubusercontent.com/hungys/azure-blog/master/media/09-install-lamp-on-azure-vm/lamp-setup-1.png)

![LAMP Setup](https://raw.githubusercontent.com/hungys/azure-blog/master/media/09-install-lamp-on-azure-vm/lamp-setup-2.png)

安裝完成後打開瀏覽器輸入「http://ironman-lamp.cloudapp.net/」，會發現沒有任何回應，原因是我們剛剛曾經跟讀者提到過，Azure 預設會擋掉特殊規則之外的所有連線，所以我們需要透過 `azure vm endpoint` 的指令來設定允許 TCP port 80 的連線，別忘了請先離開 VM 的終端機：

```
$ azure vm endpoint create ironman-lamp 80 --endpoint-name HTTP --endpoint-protocol tcp
info:    Executing command vm endpoint create
+ Getting virtual machines                                                     
+ Reading network configuration                                                
+ Updating network configuration                                               
info:    vm endpoint create command OK
```

這一回我們就可以成功連進我們所架設的 Apache 伺服器了！

![Apache default page](https://raw.githubusercontent.com/hungys/azure-blog/master/media/09-install-lamp-on-azure-vm/apache-default-page.png)

最後我們將 `/var/www/html` 底下的 `index.html` 刪除，並建立一個 `index.php` 來測試 PHP 是否能夠正常執行：

```
<?php
echo "Hello LAMP!"
?>
```

此時，打開瀏覽器若文字有順利印出，就代表我們的 PHP 有正常運作囉！如果讀者還有興趣驗證 MySQL 是否有安裝成功，可以透過 apt-get 先安裝 phpMyAdmin：

```
$ sudo apt-get install phpmyadmin
```

要特別注意的是第一個畫面，請記得用**「空白鍵」**選取**「apache2」**，這個步驟相當重要，否則沒辦法正確設定 phpMyAdmin 與 Apache 之間的關聯。

![phpMyAdmin install](https://raw.githubusercontent.com/hungys/azure-blog/master/media/09-install-lamp-on-azure-vm/phpmyadmin-install.png)

安裝完成後，在瀏覽器輸入「http://ironman-lamp.cloudapp.net/phpmyadmin」，並用我們安裝 LAMP 時所設定的 root 帳戶登入便可以開始管理我們的 MySQL 資料庫囉！

![phpMyAdmin login](https://raw.githubusercontent.com/hungys/azure-blog/master/media/09-install-lamp-on-azure-vm/phpmyadmin-login.png)

![phpMyAdmin](https://raw.githubusercontent.com/hungys/azure-blog/master/media/09-install-lamp-on-azure-vm/phpmyadmin-index.png)

# 後記

本文帶領讀者使用 CLI 指令界面來建立與管理一台 Linux 虛擬機器，並在上面安裝 PHP 動態網頁經常使用到的 LAMP 組合，若讀者還有時間的話，可以試這在剛剛安裝的 LAMP 機器上動手安裝 WordPress，可以順便體驗一下從 IaaS 來建立一個部落格系統，與直接使用 Azure Websites 搭配 Gallery 所需的時間之差別。

# 參考資料

- Azure command-line tool for Mac and Linux, [http://azure.microsoft.com/en-us/documentation/articles/command-line-tools/](http://azure.microsoft.com/en-us/documentation/articles/command-line-tools/)
- 在 Azure 中的 Linux 虛擬機器上安裝 LAMP 堆疊, [http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-linux-install-lamp-stack/](http://azure.microsoft.com/zh-tw/documentation/articles/virtual-machines-linux-install-lamp-stack/)
- ApacheMySQLPHP, [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)