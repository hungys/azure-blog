Day 12: 在 Azure 上快速建置 MongoDB 託管服務
====================================

# 前言

我們在最近兩篇文章中帶領讀者從建立 Linux VM 到安裝 MongoDB，是基於 Azure 所提供的 IaaS 服務來建置的，優點在於可以很彈性的對 VM 進行設定，當相反的，如果您本身沒有這麼高度客製、管理的需求，反而產生了維運 VM 的負擔。於是，Azure 在去年正式宣布與 mongolab 合作推出 MongoDB-as-a-service 的服務，是一個屬於 SaaS 層級的服務，而在今年也面向企業推出了 Enterprise 的版本，這些機器都是由 mongolab 以及 Azure 所負責管理的，開發者只需專注在應用程式的開發即可，可以說相當方便，本文將帶領讀者透過 Azure Store 來快速建置 MongoDB 託管服務。

# 從市集購買 MongoDB 服務

Azure 官方在近期開始與一些第三方廠商合作，透過 Azure Management Portal 的市集來販售/提供這些第三方服務，而且許多服務也提供了開發測試時期足夠的免費額度給消費者。如果您還沒有註冊 Azure 帳號，也可以從[官方網站](http://azure.microsoft.com/zh-tw/gallery/store/)來瀏覽目前市集所提供的服務。

1. 首先，點選 Management Portal 畫面左下的**「New」**來新增服務。

	![step01](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-01-addons.png)
	
2. 點選**「Store」**來瀏覽市集。

	![step02](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-02-store.png)
	
3. 在清單中可以看到所有在 Azure 上所提供的第三方服務，其中可以看到有兩個與 MongoDB 的選項，其中的「MongoDB」是屬於面向企業級的 Enterprise 服務，所以在此我們選擇「MongoLab」（其實是因為 MongoLab 有提供有限的免費額度可以使用）

	![step03](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-03-choose-mongolab.png)

4. 接著畫面上會列出 MongoLab 所提供的所有方案，可以看到不需花費任何金錢就可以使用 500MB 的免費資料庫，您也可以依照需求選擇 cluster 的方案。而在「Name」的欄位中，請為您的資料庫取一個名字，這個名字最後會對應到您的 MongoDB database。

	![step04](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-04-choose-plan.png)

5. 最後會請您確認您的訂購，因為我們選擇的是免費的 Sandbox 服務，所以應該都是 0 元的。

	![step05](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-05-review-purchase.png)

6. 完成後靜待數分鐘，一個全新的 database 就會被部署起來。

	![step06](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-06-creating.png)

7. 同時，您也會收到一封帳單的 email。

	![step08](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-08-mail.png)

8. 從 Portal 中的 Add-ons 就可以管理這些第三方服務，不過對於細部的設定，還是需要透過下方的**「Manage」**按鈕，會自動開啟 MongoLab 的網頁（且會自動登入）進入到您所建立的資料庫管理界面。到此，我們已經完成了資料庫的部署了。

	![step07](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-07-dashboard.png)

# 管理 MongoLab 服務

在 MongoLab 上所部署的 MongoDB，提供了一個簡單的網頁管理界面讓您可以進行一些簡單的操作，也可以查詢目前資料庫運行的狀況，只需從 Management Portal 中點選**「Manage」**便可以自動導向該管理界面並登入。

1. 一進到管理界面就可以看到剛剛所部署的主機名稱以及相關的連線字串的資訊。

	![step09](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-09-web-interface.png)

2. 接著切換到 Users 頁籤，為資料庫新增一個管理者。

	![step10](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-10-add-user.png)

3. 設定完成後，回到 Collections 頁籤，新增一個新的 Collection。

	![step11](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-11-add-collection.png)

4. 在 Collection 的管理頁面中，可以看到該 Collection 底下的所有 Document。

	![step12](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-12-manage-document.png)

5. 點選**「Add document」**按鈕後會開啟一個 JSON 的編輯器，而且這個編輯器會在送出時幫您檢查是否回合法的 BSON 格式。

	![step13](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-13-json-editor.png)

6. 若要對 Collection 中的內容作查詢，可以在 Collection 的管理頁面選擇**「[new search]」**。

	![step14](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-14-new-query.png)

7. 針對欄位的查詢條件，也就是先前所介紹過的「criteria」，可以輸入在畫面左側的「Query」欄位中，這邊也同樣支援「Query Selector」的進階查詢。

	![step15](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-15-basic-query.png)

8. 同時我們可以看到畫面右方可以設定結果的排序，以及我們在介紹 mongo shell 指令時曾經介紹過的「projection」，在網頁界面中同樣有提供相關的設定。

	![step16](https://raw.githubusercontent.com/hungys/azure-blog/master/media/12-mongodb-managed-service-on-azure/step-16-sort-projection.png)

若讀者想要透過 mongo shell 來管理這個資料庫，在管理界面首頁提供了相關的連線字串，以讀者所建立的 MongoLab 服務為例，只需要在終端機輸入下列資訊便可以透過 shell 來管理：

```
$ mongo ds045087.mongolab.com:45087/mongolab-ironman -u <dbuser> -p <dbpassword>
```

而根據 MongoLab 官方的文件記載，MongoLab 本身也提供了簡單的 [REST API](http://docs.mongolab.com/restapi/) 可以來存取資料庫，不過官方是建議只有在無法使用 MongoDB driver 搭配連線字串來管理的情形下才使用 REST API，所以在此就不多花篇幅介紹了。

# 後記

本文簡單介紹了如何在 Azure 中建立「MongoDB-as-a-service」的託管服務，是一種屬於 SaaS 層級的服務，如果讀者有興趣的話可以使用 `nslookup` 來反查資料庫伺服器的 domain name：

```
$ nslookup ds045087.mongolab.com
Server:		192.168.1.1
Address:	192.168.1.1#53

Non-authoritative answer:
ds045087.mongolab.com	canonical name = ds045087-b.mongolab.com.
ds045087-b.mongolab.com	canonical name = h002246.mongolab.com.
h002246.mongolab.com	canonical name = mongolab-eastus-002-4x3.cloudapp.net.
Name:	mongolab-eastus-002-4x3.cloudapp.net
Address: 191.238.52.208
```

可以發現其實從 Azure 上所購買的 MongoLab 服務也是部署在 Azure 的資料中心中，不過目前只有提供「East US」及「West US」兩種選擇。而針對需要高度客製化調校的使用者，則可以選擇昨天所介紹的透過 Azure VM 的 IaaS 服務來自行建置 MongoDB。筆者甚至曾在網路上看到有在 Cloud Services 中的 Worker Role 部署 MongoDB 服務的教學，不過一般來說還是建議以 IaaS 及 SaaS 兩種方案來做選擇會比較妥當。