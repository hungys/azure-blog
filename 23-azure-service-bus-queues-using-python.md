Day 23: 使用 Python 操作 Azure Service Bus Queues
===================================

# 前言

在上一篇文章中，我們介紹了如何使用 Azure Queue Storage 來作為前後端或程式之間傳遞訊息的佇列，而本文要介紹的是 Azure 上另一個服務所提供的佇列服務，相較於比較單純的 Queue Storage，他擁有更完整的基礎建設跟架構，所以一般來說能夠提供較可靠的佇列服務。

# 什麼是 Service Bus Queues?

![Concept](https://raw.githubusercontent.com/hungys/azure-blog/master/media/23-azure-service-bus-queues-using-python/concept.png)

Service Bus Queues 是 Azure 中服務匯流排所提供的一種佇列模型，基本上跟先前介紹過的 Queue Storage 有相同的用途，也就是負責不同角色的應用程式間的通訊，並且擁有**先進先出 (FIFO)** 的特性，其中較大的差異在於，Service Bus Queues 能夠確保一則訊息只能由一個取用者接收和處理。

到此讀者一定會產生疑問，那究竟這兩個服務有什麼差異。微軟官方在 [MSDN 文件](http://msdn.microsoft.com/en-us/library/azure/hh767287.aspx)中有針對這個議題做了一些比較以及建議，大體來說 Service Bus Queues 擁有較可靠的基礎架構設計，相較於 Queue Storage 也更能確保訊息之間的先後順序 (也就是 FIFO 的順序)。然而，若用訊息量來做比較的話，Service Bus Queues 僅能支撐 80GB 的訊息量，即便也不是個小數字了，但 Queue Storage 可以支援到最多 200TB 的數量。另外，Service Bus Queues 支援了 long-polling 的取用模式，提供 push-style 的 [event-driven API](http://msdn.microsoft.com/en-us/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx)，不過目前僅有 .NET 平台上支援此特性。**總體而言，若您有極高的需求確保佇列的穩定性及訊息之間的先後順序，Service Bus 所提供的佇列會是您的最佳選擇。**

# Service Bus Queues API

Service Bus Queues API 包含在 Azure SDK for Python 當中，相關的原始碼存放在 [azure-sdk-for-python / azure / servicebus](https://github.com/Azure/azure-sdk-for-python/tree/master/azure/servicebus) 底下。在 `servicebusservice.py` 中定義了 ServiceBusService 這個類別，您可以建立一個實體來存取相關的服務，其中包括了在下一篇文章才會接紹到的 pub/sub 模型。

# 取得命名空間連線資訊

若您還未曾建立過 Service Bus 服務，請透過 Management Portal 建立一個命名空間：

![Create](https://raw.githubusercontent.com/hungys/azure-blog/master/media/23-azure-service-bus-queues-using-python/create.png)

建立完成後點選欲使用的 namespcae，並點選下方的**「Connection Information」**，即可獲得一組連線字串：

![Key](https://raw.githubusercontent.com/hungys/azure-blog/master/media/23-azure-service-bus-queues-using-python/key.png)

該連線字串應該為以下形式，其中的 `SharedAccessKeyName` 及 `SharedAccessKey` 在待會會用來認證服務：

```
Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=vLKwIy3mdU8BuxxxxxxqkXilslpUxxxxxxqMR0wMDEk=
```

# 建立 ServiceBusService 實體

在 Python SDK 中關於 Service Bus 的操作皆是透過 ServiceBusService 這個類別，在建立服務實體之前您需要準備好命名空間的名稱以及剛剛取得的 Access Key，接下來便可以傳入必要的參數來建立一個 ServiceBusService 實體：

```
from azure.servicebus import *
bus_service = ServiceBusService(service_namespace='<YOUR NAMESPACE>', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='<YOUR ACCESS KEY>')
```

完成之後您就可以使用 bus_service 這個物件來進行相關操作。

# 建立 Queue

若要建立一個佇列，可以呼叫 ServiceBusService 底下的 `create_queue()` 方法：

```
bus_service.create_queue('myqueue')
```

根據原始碼的定義，若沒有傳入 `fail_on_exist` 這個參數的話，預設為 False，若設為 True 則當佇列已經存在時會引發例外。除此之外，您也可以透過傳入一個 Queue object 參數 `queue` 來對佇列進行基本的設定，例如佇列大小：

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '1024' # 1 GB

bus_service.create_queue('myqueue', queue_options)
```

關於 Queue 類別的相關參數設定，可以參考與 `servicebusservice.py` 同目錄底下的 `__init__.py` 中對於各資料結構、型別的定義。

# 傳送訊息至 Queue

若要新增一條訊息至佇列中，可以使用 `send_queue_message()` 方法，與之前介紹的 Azure Queues 不同的是，您必須要傳入一個 Message object：

```
msg = Message('Hello World')
bus_service.send_queue_message('myqueue', msg)
```

此外，相較於 Azure Queues 限制單條訊息 64 KB 的限制，在 Service Bus Queues 中您可以有彈性更大的 256 KB 限制，而佇列訊息數目則受限於一開始您所定義的佇列大小。

# 取出 Queue 中的訊息

呼叫 `receive_queue_message()` 可以從佇列中取出一筆訊息，值得注意的是，**這則訊息在取出後便會從佇列中刪除**，這點也與 Azure Queues 的行為有所不同。

```
msg = bus_service.receive_queue_message('myqueue')
print(msg.body)
```

若您想要將這個動作分為兩階段，或是要 peek 下一則訊息，可以透過設定 `peek_Lock=Ture` 達到：

```
msg = bus_service.receive_queue_message('myqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

使用這種方式來讀取 Queue 能夠擁有更高的容錯率，能夠避免因為處理過程中發生例外而造成訊息遺漏無法再被處理的問題。在訊息被取出時會將其 lock 住直到成功刪除，而您可以在例外發生的 catch 區段呼叫 Message 的 `unlock()` 方法來解除鎖定以讓其他取用者接收，或是等到逾時候由系統自動解鎖。

# 後記

本文針對 Service Bus Queues 的使用做了簡單的介紹，您可能無法從這些基本操作的示範中了解到它與 Azure Queues 之間的差異。然而，Service Bus 中本身還提供了另一種更強大、支援訂閱主題的佇列，筆者將在下一篇文章中帶領讀者來認識其更進階的使用。

# 參考資料

- 如何使用服務匯流排佇列, [http://azure.microsoft.com/zh-tw/documentation/articles/service-bus-python-how-to-use-queues/](http://azure.microsoft.com/zh-tw/documentation/articles/service-bus-python-how-to-use-queues/)
- Microsoft Azure SDK for Python, [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python)

