Day 5: 使用跨平台 CLI 管理 Azure (1)
=================================

# 前言

筆者曾在 Day2 時提到所謂軟體界傳說中的「開源派 vs 微軟派」，而就筆者自身的觀察，大多數開源派的開發者比較喜歡使用 CLI (Command Line Interface) 下指令來做事情，甚至寫 code 也堅持用 vim，而且用 Linux 或 Mac OS X 的比率比較高。相反的，大部份的微軟派開發者較常使用 GUI 界面，搭配號稱地表最強大 (?) 的開發工具 Visual Studio，除了少數 IT 人員可能較常需要使用到 PowerShell。

當然，上面的說法**僅是自身的觀察，而且並不是 100% 精準、適用**，不過這一系列文章強調用開源角度來認識 Azure，當然也需要來介紹一下除了使用 Management Portal 管理 Azure 之外的 CLI 方案，那就是微軟官方所推出的跨平台 Azure CLI 了。

# Azure xplat-cli 簡介

Azure 跨平台命令列介面 (xplat-cli) 提供您一組開放原始碼的跨平台命令集合，讓您可以運用在管理 Azure 平台上。xplat-cli 與 Azure 管理入口網站上所提供的功能大多數相同，例如管理管理網站、虛擬機器、行動服務、SQL Database 與其他 Azure 平台所提供的各項服務等。

![Node.js](https://raw.githubusercontent.com/hungys/azure-blog/master/media/05-using-azure-xplat-cli-1/node-js.png)

xplat-cli 是使用 Node.js 撰寫的，底層是使用 Azure SDK for Node.js 來實作，並以 Apache 2.0 授權來發行，有興趣參考或貢獻該專案原始碼的可以前往 Azure 官方的 GitHub：[https://github.com/WindowsAzure/azure-sdk-tools-xplat](https://github.com/WindowsAzure/azure-sdk-tools-xplat)

# 安裝 Azure xplat-cli

## 使用預編譯的安裝檔

如果您懶得自己下指令甚至自己編譯 Azure xplat-cli 的話，微軟也準備好了預編譯好的安裝檔，請您先自行設定好 Node.js 環境後下載安裝：

- Windows 安裝程式：[http://go.microsoft.com/fwlink/?linkid=275464&clcid=0x404](http://go.microsoft.com/fwlink/?linkid=275464&clcid=0x404)
- OS X 安裝程式：[http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x404](http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x404)

對於安裝檔的使用筆者就不多做介紹，相信只需要一直下一步下一步就可以了，本文重點將著重在使用命令列做事情！如果您已經安裝好，可以直接忽略本節的剩餘內容。

在繼續介紹如何安裝之前，先說明本文將會以 Mac OS X 為例，不過筆者也才剛使用 OS X 兩個多月，如果有錯誤的地方也歡迎指正。Windows 的讀者可以上網自行尋找 Node.js 相關的安裝教學。

## 安裝 Node.js 執行環境 (Mac OS X)

### 安裝 Xcode

首先假設是在一台乾淨的 Mac 上，為了使用 Homebrew 來安裝相關套件，首先您必須先從市集安裝 Xcode 以及其 Command Line Tools。

![Xcode](https://raw.githubusercontent.com/hungys/azure-blog/master/media/05-using-azure-xplat-cli-1/xcode.png)

您可以在終端機底下用指令確認是否已經安裝：

```
$ xcode-select -p
/Applications/Xcode.app/Contents/Develope
```
假如沒有回傳一個正確的路徑，可以直接下指令 `xcode-select --install` 安裝。

### 安裝 Homebrew

Homebrew 是 OS X 中最熱門的套件管理工具，就像 Ubuntu 底下 apt-get 那麼神奇、方便、簡單。如果還沒安裝的讀者，可以下一行 ruby 指令來進行安裝：

```
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

由於筆者也才接觸 Homebrew 不久，簡單記錄一下相關的指令：

* 檢查 Homebrew 狀態：`brew doctor`
* 搜尋套件：`brew search`
* 安裝套件：`brew install <application-name>`
* 列出已安裝的套件：`brew list`
* 移除套件：`brew remove <application-name>`
* 更新 Homebrew：`brew update`
* Homebrew 文件：`man brew`

### 安裝 nvm

在 OS X 上安裝 Node.js 有很多種方法，例如直接在終端機下 `brew install node` 的指令，但這是不太建議的做法。就筆者事前在網路上搜尋相關教學的結論，會推薦使用 nvm (node version management) 這套工具來安裝、管理及切換 Node.js 的版本。

首先使用 Homebrew 來安裝 nvm：

```
$ brew install nvm
```

為了可以直接在 shell 使用 nvm 指令，必須先設定好 .bashrc 或 .bash_profile，並且讓新的設定生效：

```
$ echo "source $(brew --prefix nvm)/nvm.sh" >> .bashrc
$ . ~/.bashrc
```

以上就完成了 nvm 的安裝！

### 使用 nvm 安裝 Node.js

前面提到 nvm 最大的優點就是可以快速在不同的版本做切換，首先我們可以透過指令來看目前有哪些版本可以安裝：

```
$ nvm ls-remote
    v0.10.32
    v0.11.0
    v0.11.1
    v0.11.2
    v0.11.3
    v0.11.4
    v0.11.5
    v0.11.6
    v0.11.7
    v0.11.8
    v0.11.9
    v0.11.10
    v0.11.11
    v0.11.12
    v0.11.13
    v0.11.14
```

筆者已經截去了很多輸出，可以發現 Node.js 的版本更迭相當快，這也是為什麼建議使用 nvm 來安裝、切換版本。

接下來使用 `nvm install <version>` 來安裝目前[官網](http://nodejs.org/)所建議的版本：

```
$ nvm install v0.10.32
######################################################################## 100.0%

Now using node v0.10.32
```

假如要使用指令來查詢目前所有安裝的版本，可以使用 `nvm ls` 指令：

```
$ nvm ls

  v0.10.32
  v0.11.10
current:    v0.11.10
```

如果要在版本之間做切換的話，可以使用 `nvm use <version>` 指令：

```
$ nvm use v0.10.32
Now using node v0.10.32
```

不過需要注意的是 `nvm use` 只對當前的 shell 有效，如果要設定預設版本的話則需要使用 `nvm alias default <version>` 的指令：

```
$ nvm alias default 0.10.32
default -> v0.10.32
```

到此為止我們已經完成了前置作業，安裝完 Node.js 執行環境。

### 安裝 xplat-cli

最後我們需要使用 npm 來安裝 xplat-cli，npm 目前已經內建在新版的 Node.js 了，所以不需要額外安裝：

```
$ npm install -g azure-cli
```

當然您也可以選擇下載 source code 來安裝：

```
$ git clone https://github.com/Azure/azure-sdk-tools-xplat.git
$ cd ./azure-sdk-tools-xplat
$ npm install
```

### 設定自動完成

Azure 官方也很貼心的提供了自動完成的功能，您可以透過設定 .bashrc 或 .bash_profile 來開啟這個功能：

```
$ azure --completion >> ~/azure.completion.sh
$ echo 'source ~/azure.completion.sh' >> .bashrc
```

# 設定 Azure 訂閱帳戶

## 下載訂閱設定檔

在使用 CLI 管理 Azure 之前，我們必須先設定好自己的訂閱帳戶資訊，基本上 xplat-cli 提供了兩種驗證方式，第一種大多用於企業的 Active Directory 就不多做介紹，這邊要介紹的是使用匯入 publish settings 的方式，首先在終端機下 `azure account download` 的指令，會開啟瀏覽器並帶領您下載您的設定檔。

如果您有多個訂閱帳戶，而且瀏覽器所導向的並不是您要下載的訂閱，請依照畫面的指示來下載：

![Download publish settings](https://raw.githubusercontent.com/hungys/azure-blog/master/media/05-using-azure-xplat-cli-1/download-settings.png)

如果您的 Microsoft Account 底下有多個訂閱，便可以透過列表來選擇要下載的 publish settings：

![Multiple subscriptions](https://raw.githubusercontent.com/hungys/azure-blog/master/media/05-using-azure-xplat-cli-1/multiple-subscriptions.png)

## 匯入訂閱設定檔

前一小節下載好 publish settings 後，只需透過一行指令來完成匯入即可：

```
$ azure account import <file location>
```

在 Part 1 中我們並不會針對 CLI 的使用多做介紹，但在此為了測試帳戶有正確設定，您可以透過 `azure site create <site name>` 指令來測試建立一個網站服務 (Websites)：

```
$ azure site create --location "East Asia" ironman-cli-test
info:    Executing command site create
+ Getting sites                                                                
+ Getting locations                                                            
info:    Creating a new web site at ironman-cli-test.azurewebsites.net
\info:    Created website at ironman-cli-test.azurewebsites.net                
+
info:    site create command OK
```

也可以透過 `azure site list` 的指令來查詢目前所有的網站服務狀態：

```
$ azure site list
info:    Executing command site list
+ Getting locations                                                            
+ Getting sites                                                                
data:    Name                             Slot  Status   Location   Mode  URL                                              
data:    -------------------------------  ----  -------  ---------  ----  -------------------------------------------------                   
data:    tlc2013                                Running  East Asia  Free  tlc2013.azurewebsites.net                        
data:    ironman-cli-test                       Running  East Asia  Free  ironman-cli-test.azurewebsites.net               
data:    eecscamp2014                           Running  East Asia  Free  eecscamp2014.azurewebsites.net                   
info:    site list command OK
```

## 多帳號切換

如果您同時匯入了超過一個訂閱帳戶，例如個人的測試帳戶以及公司專用的帳戶，也可以透過相關指令來切換，如果對 Azure CLI 不熟的話都可以在指令後加上 `-h` 或 `--help` 來查詢相關文件。

在 CLI 中可以透過 `azure account list` 來查詢目前所有匯入的訂閱：

```
$ azure account list
info:    Executing command account list
data:    Name                                         Id                                    Current
data:    -------------------------------------------  ------------------------------------  -------
data:    Windows Azure MSDN - Visual Studio Ultimate  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true   
data:    Pay-As-You-Go                                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
info:    account list command OK
```

若要切換到另一個訂閱可以使用 `azure account set <subscription>` 指令：

```
$ azure account set Pay-As-You-Go
info:    Executing command account set
info:    Setting subscription to "Pay-As-You-Go"
info:    Changes saved
info:    account set command OK
```

## account 相關指令匯整

最後筆者將 `azure account` 底下的相關指令稍微整理出來：

* 下載訂閱設定檔：`azure account download`
* 列出所有訂閱：`azure account list`
* 顯示目前訂閱資訊：`azure account show`
* 切換訂閱：`azure account set <subscription>`
* 匯入訂閱設定檔：`azure account import <file path>`
* 清除訂閱：`azure account clear`

其他更細部的設定可以透過 `azure account --help` 來查詢，而如果是單獨對 set 指令有疑問的話則是透過 `azure account set --help`，其他的則以此類推。到目前為止，讀者應該也有發現所有的指令都是從 `azure` 起頭的，這是 Azure xplat-cli 的設計，每次的指令都是獨立運作的。

# 後記

這篇文章雖然長篇大論了一番，但其實很大部份都是關於前置環境的設定，就當做筆者初入 OS X 環境的一些心得筆記，正好可以讓一些新手參考。在明天的 Part 2 將會繼續對 xplat-cli 如何管理 Azure 做簡單的介紹。

# 參考資料

- Installing Homebrew on OS X Mavericks 10.9, Package Manager for Unix Apps, [http://coolestguidesontheplanet.com/setting-up-os-x-mavericks-and-homebrew/](http://coolestguidesontheplanet.com/setting-up-os-x-mavericks-and-homebrew/)
- Node.js 安裝與版本切換教學 (for MAC), [http://icarus4.logdown.com/posts/175092-nodejs-installation-guide](http://icarus4.logdown.com/posts/175092-nodejs-installation-guide)
- 如何在 Mac OSX Lion 上設定 node.js 的開發環境, [http://dreamerslab.com/blog/tw/how-to-setup-a-node-js-development-environment-on-mac-osx-lion/](http://dreamerslab.com/blog/tw/how-to-setup-a-node-js-development-environment-on-mac-osx-lion/)
- 安裝與設定 Azure 跨平台命令列介面, [http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/](http://azure.microsoft.com/zh-tw/documentation/articles/xplat-cli/)
- Microsoft Azure Cross Platform Command Line, [https://github.com/Azure/azure-sdk-tools-xplat](https://github.com/Azure/azure-sdk-tools-xplat)