Day 21: 使用 Python 操作 Azure Table Service
===============================

# 前言

在前一篇文章中，我們與讀者介紹了 Blob Service API 的操作，它是歸類在 Azure Storage 服務中的其中一個功能。本文將要介紹的是 Azure Storage 中所提供的 Table Service 資料表服務，並示範如何使用 SDK 來操作這個服務。

# 什麼是 Table Service?

![Concept](https://raw.githubusercontent.com/hungys/azure-blog/master/media/21-azure-table-service-using-python/concept.png)

Azure Table Service 是一個被設計來儲存大量結構化資料，是一種 NoSQL 的實作，所以本身並不具備關聯式資料庫的功能。這種 NoSQL 服務通常被用來儲存大量甚至上看 TB 級的資料，而且不需要做複雜的查詢。在 Table Service 中，主要包含了三個元件，依據層級區分分別為 Account、Table 及 Entity：

- **儲存體帳戶 (Account)**: 一個儲存體帳戶代表一個儲存體，在使用相關 API 時均需要提供該帳戶的名稱以及 Key。
- **資料表 (Table)**: 資料表示一組實體，而且它與一般 SQL 資料庫不同的地方在於並沒有 schema 的設計，所以每個實體可以擁有不同的屬性，僅受到儲存體容量的限制。
- **實體 (Entity)**: 實體是一組屬性，也是一個資料列，它並不受到像傳統 SQL 的 schema 結構限制，但每個實體有 252 個屬性、大小 1MB 的上限，而為了加快查詢速度，本身具備了三個系統屬性：資料分割索引鍵 (PartitionKey)、資料列索引鍵 (RowKey) 以及時間戳記 (Timestamp)。

若與本系列文章曾經介紹過的 MongoDB 相比的話，Table 正好可以對應到 MongoDB 中的**「Collection」**，而 Entity 則是對應到**「Document」**，兩者都屬於 NoSQL 的儲存型態。

# Table Service API

Table Service API 包含在 Azure SDK for Python 當中，相關的原始碼存放在 [azure-sdk-for-python / azure / storage](https://github.com/Azure/azure-sdk-for-python/tree/master/azure/storage) 底下。與先前介紹過的 Blob Service API 相同，在 `storageclient.py` 中的 `StorageClient` 類別實作了基本的 REST 操作方法，而開發者則可以直接透過 `tableservice.py` 中繼承 `StorageClient` 的 `TableService` 類別來產生一個物件操作儲存體服務。

# 建立 BlobService 實體

在 Python SDK 中關於 Blob Service 的操作皆是透過 BlobService 這個類別，在建立服務實體之前您需要準備好儲存體帳戶的名稱以及 Access Key，這些資訊可以從 Management Portal 取得，接下來便可以傳入必要的參數來建立一個 BlobService 實體：

```
from azure.storage import *
table_service = TableService(account_name='myaccount', account_key='mykey')
```

完成之後您就可以使用 table_service 這個物件來進行相關操作。

# 建立 Table

若要建立一個資料表，可以呼叫 TableService 底下的 `create_table()` 方法：

```
table_service.create_table('products')
```

根據原始碼的定義，若沒有傳入 `fail_on_exist` 這個參數的話，預設為 False，若設為 True 則當資料表已經存在時會引發例外。

# 新增 Entity

若要新增實體，您可以呼叫 `insert_entity()` 方法，而且有兩種使用方式，共同點在於每個實體都需要指定 PartitionKey 及 RowKey 的值。第一種呼叫方法是傳入一個 Dictionary 物件：

```
vm = {'PartitionKey':'product', 'RowKey':'1', 'name' :'Virtual Machines', 'rating' : 8}
table_service.insert_entity('products', vm)
```

您也可以選擇傳入一個 Entity 物件：

```
vm = Entity()
vm.PartitionKey = 'product'
vm.RowKey = '1'
vm.name = 'Virtual Machines'
vm.rating = 8
table_service.insert_entity('products', vm)
```

# 批次新增 Entity

若您有一次提交大量 Entity 的需求，可以在一連串 API 呼叫前後分別加上 `begin_batch()` 及 `commit_batch()`：

```
table_service.begin_batch()
table_service.insert_entity('products', product1)
...
table_service.insert_entity('products', product10)
table_service.commit_batch()
```

在 SDK 中的原始碼可以發現，當呼叫了 `begin_batch()` 時，會建立一個名為 \_batchclient 的 BatchClient 物件，而在提交 API 請求時 (如 insert_entity)，在 `_perform_request()` 方法中首先會檢查目前 \_batchclient 是否為 None，若非則代表目前是批次處理狀態，則將此請求加入 batch 的隊列中，直到 `commit_batch()` 被呼叫了，才一次性的處理這些請求，並將 \_batchclient 再次設為 None。

# 更新 Entity

若要更新實體的內容，您可以呼叫 `update_entity()` 方法：

```
vm = {'name' :'Virtual Machines', 'rating' : 9}
table_service.update_entity('products', 'product', '1', vm)
```

其中，第一個參數為 Table name、第二個為 Partition key、第三個為 Row key，最後一個則是 Entity 物件或 Dictionary 物件。另外，這個方法在 Entity 不存在時會拋出例外，若要達到更新時發現不存在則新增，可以改用 `table_service.insert_or_replace_entity(table_name, partition_key, row_key, entity)` 方法。

# 查詢 Entity

若要使用 PartitionKey 及 RowKey 來查詢某個實體，可以呼叫 `get_entity()` 方法：

```
product = table_service.get_entity('products', 'product', '1')
```

此外，若要使用較為進階的方法來查詢實體，則可以使用 `query_entities()` 方法，該方法的定義如下：

```
def query_entities(self, table_name, filter=None, select=None, top=None, next_partition_key=None, next_row_key=None):
```

其中，filter 是過濾的條件，可以傳入一個條件字串，您可以在 [MSDN 文件](http://msdn.microsoft.com/library/azure/dd894031.aspx)中查詢到所支援的運算子。而 select 則是指定要取出的欄位、top 則是限定查詢的上限筆數（也就是回傳前 n 筆的意思）。如以下範例，會查詢出所有 rating 大於等於 8 的實體，回傳的是一個清單：

```
results = table_service.query_entities('products', 'rating ge 8')
for product in results:
    print(product.name)
    print(product.rating)
```

# 刪除 Entity 或 Table

若要刪除特定的實體，可以呼叫 `delete_entity()` 方法：

```
table_service.delete_entity('products', 'product', '1')
```

而 `delete_table()` 方法則可以刪除指定的資料表：

```
table_service.delete_table('products')
```

# 後記

本文針對如何使用 Azure SDK for Python 來操作 Table Service 做了大致的介紹，若您有瞭解更細部的 API 呼叫相關說明，例如 optional 的參數設定，建議您直接前往 GitHub 上 [azure-sdk-for-python / azure / storage](https://github.com/Azure/azure-sdk-for-python/tree/master/azure/storage) 的部分參閱 `tableservice.py` 中對於各個 API 方法的定義。

# 參考資料

- 如何從 Python 使用資料表儲存體服務, [http://azure.microsoft.com/zh-tw/documentation/articles/storage-python-how-to-use-table-storage/](http://azure.microsoft.com/zh-tw/documentation/articles/storage-python-how-to-use-table-storage/)
- Microsoft Azure SDK for Python, [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python)