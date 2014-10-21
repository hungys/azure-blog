Day 22: 使用 Python 操作 Azure Queue Storage Service
===============================

# 前言

在最近兩篇文章中，我們介紹了如何使用 Python 來操作 Azure 上的相關儲存服務，包含了 Blob 以及 Table 兩種類型。在接下來的文章中，我們要開始介紹 Queue 的開發模型，Azure 上的佇列服務主要分為 Queue Storage 以及 Service Bus Queue 兩種，而本文將要介紹的 Queue Storage 也歸類在儲存體服務之中。

# 什麼是 Queue Storage Service?

Azure Queue Storage Service 是一種用來儲存大量訊息的服務，每則訊息的大小上限是 **64 KB**，但最多可以儲存到 200 TB 的訊息總量。通常在系統實作上，我們會運用佇列來非同步處理一些比較耗時的動作。

設想您要建立一個類似 Flickr 的相簿服務，當使用者上傳照片至您的服務時，您會需要為使用者產生許多不同大小的縮圖，但這種比較耗時的工作我們不可能在 HTTP 請求時一起處理，所以便可以透過 Queue 來傳遞這個請求，並由另一個程式來處理這些縮圖的工作。

此外， Queue Storage Service 本身提供了 REST 介面讓您可以隨時隨地存取佇列中的內容，其 URL 端點格式為 `http://<storage account>.queue.core.windows.net/<queue>`。

# Queue Storage Service API

Queue Storage Service API 包含在 Azure SDK for Python 當中，相關的原始碼存放在 [azure-sdk-for-python / azure / storage](https://github.com/Azure/azure-sdk-for-python/tree/master/azure/storage) 底下。類似於之前介紹的 Blob Service API，在 `storageclient.py` 中的 `StorageClient` 類別實作了基本的 REST 操作方法，而開發者則可以直接透過 `queueservice.py` 中繼承 `StorageClient` 的 `QueueService` 類別來產生一個物件操作儲存體服務。

# 建立 QueueService 實體

在 Python SDK 中關於 Blob Service 的操作皆是透過 BlobService 這個類別，在建立服務實體之前您需要準備好儲存體帳戶的名稱以及 Access Key，這些資訊可以從 Management Portal 取得，接下來便可以傳入必要的參數來建立一個 BlobService 實體：

```
from azure.storage import *
queue_service = QueueService(account_name='myaccount', account_key='mykey')
```

完成之後您就可以使用 queue_service 這個物件來進行相關操作。

# 建立 Queue

若要建立一個佇列，可以呼叫 QueueService 底下的 `create_queue()` 方法：

```
queue_service.create_queue('myqueue')
```

根據原始碼的定義，若沒有傳入 `fail_on_exist` 這個參數的話，預設為 False，若設為 True 則當佇列已經存在時會引發例外。

# 插入訊息至 Queue

若要新增一條訊息至佇列中，可以使用 `put_message()` 方法：

```
queue_service.put_message('myqueue', 'Hello World')
```

而這個方法本身還有兩個 optional 參數，其中 `visibilitytimeout` 可以設定多少秒之後才讓這則訊息顯示（可以被取出），而 `messagettl` 可以設定 TTL 時間，預設會是最大值 7 天，也就是說，一則訊息在佇列中最長可以保存七天。

# 取出 Queue 中的訊息

呼叫 `get_messages()` 方法，您可以取出佇列中一個或多個訊息，而預設情形下，這些訊息會保持 30 秒不被其他 consumer 看見，在您完成相關處理要清除訊息時，必須呼叫 `delete_message()` 方法。而這個方法本身也有兩個 optional 參數，預設會回傳一個訊息，而您也可以透過設定 `numofmessages` 參數來指定要回傳的數量，最多可以 32 筆，另外若想要改變預設的 30 秒 visibility timeout，可以設定 `visibilitytimeout` 參數，這個值必須大於 1 秒並小於 7 天。

```
messages = queue_service.get_messages('myqueue')
for message in messages:
    print(message.message_text)
    queue_service.delete_message('myqueue', message.message_id, message.pop_receipt)
```

除了刪除之外，您當然也可以透過 `update_message()` 來更新訊息的內容：

```
queue_service.update_message('myqueue', message.message_id, 'Hello World Update', message.pop_receipt, 0)
```

# 查看 Queue 中的訊息

若您不希望影響到訊息的 visibility，單純只是要 peek 下幾則訊息，則可以使用 `peek_messages()` 方法，並且同樣可以透過 `numofmessages` 這個 optional 參數來決定要回傳幾筆訊息：

```
messages = queue_service.peek_messages('myqueue')
for message in messages:
    print(message.message_text)
```

# 取得 Queue 的長度

若您有取得目前佇列長度的需求，可以呼叫 `get_queue_metadata()` 方法來取得該佇列的中繼資訊，其中一個 Header 值 `x-ms-approximate-messages-count` 即為長度的**近似值**。

queue_metadata = queue_service.get_queue_metadata('myqueue')
count = queue_metadata['x-ms-approximate-messages-count']

# 刪除 Queue

最後，您當然也可以使用 API 來刪除一個佇列：

```
queue_service.delete_queue('myqueue')
```

若單純只是要清空佇列中的內容，則可以使用 `clear_messages()` 方法：

```
queue_service.clear_messages('myqueue')
```

# 後記

在最近三篇文章中，我們已經向讀者介紹了如何使用 Python 來操作 Azure 的儲存體服務，包括了 Blob、Table 到本文的 Queue，可以發現到即便您不是一個 .NET 開發者，依然可以透過官方提供的 SDK 及 API 來操作 Azure 上的服務，以佇列的使用來說，您也不必侷限一定要使用 AWS 所提供的 SQS，Azure 也會是您的其中一個可行選項！

# 參考資料

- 如何從 Python 使用佇列儲存體服務, [http://azure.microsoft.com/zh-tw/documentation/articles/storage-python-how-to-use-queue-storage/](http://azure.microsoft.com/zh-tw/documentation/articles/storage-python-how-to-use-queue-storage/)
- Microsoft Azure SDK for Python, [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python)