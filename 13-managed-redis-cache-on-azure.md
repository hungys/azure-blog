Day 13: 在 Azure 上快速建置 Redis Cache 服務
===================================

# 前言

微軟在今年五月正式宣布 Azure Redis Cache 進入技術預覽階段，開始提供用戶測試使用，同時也建議 Azure 用戶所有新開發的應用程式，未來皆採用 Azure Redis Cache，等於要正式取代過去建立在 AppFabric 上的快取服務，取而代之的是在 open source 圈有非常豐富資源的 redis。本文將介紹如何在新的 Azure Portal 上建置 Redis Cache 服務，以及基本的操作方式。此外，微軟官方也已經宣布 Azure Redis Cache 已經在筆者撰文的本週正式 GA！

# Redis 簡介

![Redis logo](https://raw.githubusercontent.com/hungys/azure-blog/master/media/13-managed-redis-cache-on-azure/redis-logo.png)

Redis 是由 Pivotal 公司所贊助的一個開源項目，是一種基於記憶體、以 key-value 鍵值對儲存的 in-memory 資料庫。Redis 本身最大的特色除了因為一個基於記憶體的資料庫所以擁有優異的效能之外，它也常被認為是一種儲存資料結構的伺服器，這是因為對於一個 key 值所儲存的 value 可以是字串 (strings)、雜湊 (hashes)、清單 (lists)、資料集 (sets) 或可排序資料集(sorted sets)。

Azure 正式推出以 Redis 為基礎的快取服務除了它本身優異的性能之外，另一個重要的原因便是 Redis 本身在開放原始碼圈相當受到歡迎，所以有相當健全的社群體系。此外，針對各種不同平台或程式語言也都有對應的函式庫可以使用，以 .NET 開發者為例，只需要從 NuGet 下載「[StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)」便可以將應用程式與 Redis 做整合，而 Python 也有對應的「[redis-py](https://pypi.python.org/pypi/redis/)」可以使用，更多其它的 client 以及官方所推薦的 driver 可以在 Redis 的[官方網站](http://redis.io/clients)做查詢。

# 從 VM 安裝 (IaaS)

若您想要擁有較高的管理權限或較彈性的部署，可以先在 Azure 上使用 IaaS 服務建立一台 Linux VM，再手動安裝 Redis，以 Ubuntu 為例，可以透過 `apt-get` 來安裝：

```
$ sudo apt-get install redis-server
```

本文將著重在接下來所介紹的，從 Preview Portal 來建立由微軟所代管的 Redis 服務。

# 從 Portal 建立 (SaaS)

即便 Azure Redis Cache 已經進入 GA 的階段，但現階段只有對應到新的預覽版 Portal，所以若要在 Azure 上建置 Redis 快取服務必須先連上 [Preview Portal](https://portal.azure.com/) 而非舊有的 Management Portal。新的 Portal 融入了許多動態磚的元素在裡面，而且整個操作模式與過去相當不同，是從左至右展開的方式來呈現，整個畫面也比較 touch-friendly，針對關注的服務也可以釘選動態磚在 Portal 的主畫面上隨時監控，這是筆者相當讚賞的功能。不過，目前整體來說操作體驗還不是很理想，尤其是在網頁讀取的速度上面還有很大的加強空間。

1. 進入 Preview Portal 後，點選左下角的**「New」**，並選擇建立**「Redis Cache」**服務。

	![step01](https://raw.githubusercontent.com/hungys/azure-blog/master/media/13-managed-redis-cache-on-azure/step-01-new.png)


2. 等待瀏覽器讀取完成後，會在右方出現建立的精靈，除了輸入 DNS 的名稱之外，還可以依序設定所需要的價格等級、Resource group 及部署地區等等。

	![step02](https://raw.githubusercontent.com/hungys/azure-blog/master/media/13-managed-redis-cache-on-azure/step-02-dns-name.png)

3. 點選**「Pricing Tier」**，可以進一步看到所有 Redis Cache 的服務層級，主要分為基本 (Basic)、標準 (Standard) 兩大類，又依據所支援的功能及快取容量有價錢上的差別，面板上也有提供月費的台幣換算作為參考。

	![step03](https://raw.githubusercontent.com/hungys/azure-blog/master/media/13-managed-redis-cache-on-azure/step-03-pricing-tier.png)

4. 在**「Location」**的設定中，依據您的部署需求選擇，目前 Redis Cache 已經正式上市，所以在全球的資料中心都有提供服務。完成設定之後按下「Create」開始部署，Redis 的部署相較於其他服務所需等待時間較長，請耐心等候，不過基本上還是可以在十分鐘內完成。

	![step04](https://raw.githubusercontent.com/hungys/azure-blog/master/media/13-managed-redis-cache-on-azure/step-04-location.png)

5. 部署完成後，如果剛剛有勾選釘選在主畫面可以直接從主畫面點選開啟剛部署好的 Azure Redis Cache，在面板上可以看到相關的統計數據，例如 cache hit、cache miss 的統計（因為筆者事先有測試過這台所以已經有數據），另外也有關於 get、set 及其他相關的流量統計。

	![step05](https://raw.githubusercontent.com/hungys/azure-blog/master/media/13-managed-redis-cache-on-azure/step-05-panel.png)

6. 點選**「Properties」**，可以看到我們剛部署好的這台 Redis 服務的主機名稱以及連線的 port。

	![step06](https://raw.githubusercontent.com/hungys/azure-blog/master/media/13-managed-redis-cache-on-azure/step-06-properties.png)

7. 點選**「Keys」**，則可以看到要連線到這台 Redis Cache 所需要的 Key。事實上預設手動安裝的 Redis 是沒有密碼保護的，不過 Azure 為了安全考量預先已經產生了 Key 幫我們啟動密碼保護。

	![step07](https://raw.githubusercontent.com/hungys/azure-blog/master/media/13-managed-redis-cache-on-azure/step-07-keys.png)

到此，我們已經在幾個 click 間把 Redis Cache 部署起來了。

# 使用 redis-cli 連線

若您是自行在 Linux 上安裝 Redis server 的話，會連同安裝一個管理用的 redis-cli shell 界面，如果要在 Ubuntu 上單純安裝 CLI 及管理工具的話，可以透過 `apt-get` 或直接從原始碼抽出 redis-cli 來編譯：

```
$ sudo apt-get install redis-tools
```

接下來便可以透過 redis-cli 連線到我們所部署的快取服務：

```
$ redis-cli -h <hostname>.redis.cache.windows.net
```

在上一節有提到過，從 Azure 所建立的 Redis Cache 本身已經有密碼保護，可以在 Portal 中的**「Keys」**取得，取得之後可以在 CLI 中使用 `auth` 指令來認證：

```
> auth <YOUR PRIMARY KEY HERE>
OK
```

接下來我們便可以開始透過 `get`、`set` 指令來操作 Redis，將字串儲存到資料庫中：

```
> set test "azure"
OK
> get test
"azure"
```

使用 `del` 指令則可以刪除特定的資料，如果再透過 `get` 來取得的話便會回傳 nil (即 null)：

```
> del test
(integer) 1
> get test
(nil)
```

如果要在新增資料時設定快取的過期時間，則可以透過 `setex` 的指令，並以秒為單位來指定過期的時間：

```
> setex test 60 "azure"
OK
> get test
"azure"
... after 60 seconds ...
> get test
(nil)
```

針對數字的資料，還支援直接使用 `incr` 及 `decr` 來進行遞增及遞減的動作：

```
> set count 1
OK
> incr count
(integer) 2
> incr count
(integer) 3
> get count
"3"
> decr count
(integer) 2
> get count
"2"
```

最後，若要查詢目前所有儲存的 key 的話，可以使用 `keys *` 的指令：

```
> set key1 "value1"
OK
> set key2 "value2"
OK
> set key3 "value3"
OK
> set key4 "value4"
OK
> set key5 "value5"
OK
> keys *
1) "key4"
2) "key2"
3) "key3"
4) "key5"
5) "key1"
```

稍早我們也提到過，Redis 除了基本的字串之外，還提供了對清單 (List)、雜湊 (Hash) 等其他資料結構儲存的支援，由於不在本文所要介紹的範疇之內，所以讀者可以自行前往 Redis 官方網站的 [Commands](http://redis.io/commands) 頁面查詢相關指令。

# 後記

本文透過簡短篇幅介紹如何在 Azure 上建置 Redis Cache 服務，而除了最基本的快取用途之外，Redis 也常常用來作為 Session 資料庫的使用，如果您是一個 .NET 網頁開發者的話，Azure Redis Cache 更直接提供了 ASP.NET Session State Provider 的套件可以在 NuGet 下載安裝，只要對 Web.config 做簡單的設定就可以將 Redis 作為 Session 的儲存資料庫，相關設定細節可以參考 [MSDN 文件](http://msdn.microsoft.com/en-us/library/azure/dn690522.aspx)。

# 參考資料

- Microsoft Azure 快取, [http://azure.microsoft.com/zh-tw/services/cache/](http://azure.microsoft.com/zh-tw/services/cache/)
- 我該選擇哪一種 Azure 的分散式快取 (Cache) 方案?, [http://blogs.technet.com/b/azuretw/archive/2014/08/09/azure-cache.aspx](http://blogs.technet.com/b/azuretw/archive/2014/08/09/azure-cache.aspx)
- Redis, [http://redis.io/](http://redis.io/)
- 在Ubuntu中安装Redis, [http://blog.fens.me/linux-redis-install/](http://blog.fens.me/linux-redis-install/)