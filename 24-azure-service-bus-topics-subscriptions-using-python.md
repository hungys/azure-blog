Day 24: 使用 Python 操作 Service Bus Topics/Subscriptions
==============================

# 前言

在前一篇文章中，筆者介紹了如何操作 Service Bus Queues，但讀者可能還難以感受到 Service Bus 的威力，而今天要介紹的是 Service Bus 中提供的另一種佇列模型，它是一種以「主題」及「訂閱」為基礎的佇列服務，提供了一對多的訊息傳遞模式。

# 什麼是 Service Bus Topics/Subscriptions?

![Concept](https://raw.githubusercontent.com/hungys/azure-blog/master/media/24-azure-service-bus-topics-subscriptions-using-python/concept.png)

Service Bus 服務匯流排所提供的主題、訂閱機制與服務匯流排佇列最大的差別在於支援了「**一對多**」的通訊模式，對於單一一個主題，可以有多個訂閱註冊，而這個訂閱可以設定好篩選的條件，當訊息傳送至主題時，每個訂閱都可以取得符合條件的訊息來處理，在這個機制中，每個訂閱就如同一個「虛擬佇列」。

# Service Bus Topics/Subscriptions API

Service Bus Topics/Subscriptions API 包含在 Azure SDK for Python 當中，相關的原始碼存放在 [azure-sdk-for-python / azure / servicebus](https://github.com/Azure/azure-sdk-for-python/tree/master/azure/servicebus) 底下。與昨天介紹的 Service Bus Queues 相同，是使用定義在在 `servicebusservice.py` 中的 ServiceBusService 這個類別來存取相關服務。

# 建立 ServiceBusService 實體

在 Python SDK 中關於 Service Bus 的操作皆是透過 ServiceBusService 這個類別，在建立服務實體之前您需要準備好 Service Bus 的命名空間名稱以及 Access Key，若不曉得如何取得可以參考上一篇介紹 Service Bus Queues 的文章，接下來便可以傳入必要的參數來建立一個 ServiceBusService 實體：

```
from azure.servicebus import *
bus_service = ServiceBusService(service_namespace='<YOUR NAMESPACE>', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='<YOUR ACCESS KEY>')
```

完成之後您就可以使用 bus_service 這個物件來進行相關操作。

# 建立 Topic

若要建立一個主題，可以呼叫 ServiceBusService 底下的 `create_topic()` 方法：

```
bus_service.create_topic('mytopic')
```

根據原始碼的定義，若沒有傳入 `fail_on_exist` 這個參數的話，預設為 False，若設為 True 則當佇列已經存在時會引發例外。除此之外，您也可以透過傳入一個 Topic object 參數 `topic` 來對佇列進行基本的設定，例如佇列大小：

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '1024' # 1 GB

bus_service.create_topic('mytopic', topic_options)
```

關於 Topic 類別的相關參數設定，可以參考與 `servicebusservice.py` 同目錄底下的 `__init__.py` 中對於各資料結構、型別的定義。

# 建立 Subscription

在前文有提到，本文所介紹的佇列服務最大的特色就是可以建立許多訂閱，並給予不同的篩選條件，若要建立一個訂閱，可以使用 `create_subscription()` 方法，預設會使用 `MatchAll` 這個規則，也就是所有訊息都符合篩選條件：

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

在 Subscription 中，篩選條件是使用 [SQL92](http://www.contrib.andrew.cmu.edu/~shadow/sql/sql1992.txt) 標準中所定義的 SqlFilter，可以參考以下的 Grammar 定義，或是前往 [MSDN 文件](http://msdn.microsoft.com/zh-tw/library/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx)瀏覽更細節的說明，但基本上與熟悉的 SQL 查詢語法相同。

```
<predicate ::=
{ NOT <predicate> }
| <predicate> AND <predicate>
| <predicate> OR <predicate>
| <expression> { = | <> | != | > | >= | < | <= } <expression>
| <property> IS [NOT] NULL
| <expression> [NOT] IN ( <expression> [, ...n] )
| <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]
| EXISTS ( <property> )
| ( <predicate> )
 
<expression> ::=
<constant> 
| <property>
| <expression> { + | - | * | / } <expression>
| { + | - } <expression>
| ( <expression> )
```

若要設定篩選條件，必須呼叫 `create_rule()` 方法並傳入訂閱名稱及一個 Rule object：

```
bus_service.create_subscription('mytopic', 'ImportantMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'star > 4'

bus_service.create_rule('mytopic', 'ImportantMessages', '<RULE NAME>', rule)
```

上述的程式碼即建立一個當訊息中的 `custom_properties` 屬性中 star > 4 的條件成立作為 ImportantMessages 的篩選標準，`custom_properties` 的設定可以參考下一節的介紹。相對的，若要刪除篩選條件則可以使用 `delete_rule()` 方法：

```
bus_service.delete_rule('mytopic', 'ImportantMessages', '<RULE NAME>')
```

如此一來，當一則訊息中的 star 欄位值大於 4 時，即會被送進 ImportantMessages 這個訂閱裡，有點像虛擬佇列的概念。

# 傳送訊息至 Topic

若要傳送訊息至 Topic，需要傳入的是一個 Message 物件，關於 Message 類別的定義可以參考 `__init__.py`。Message 的建構子第一個參數為訊息的內容 (body)，而您也可以傳入一個字典物件到 `custom_properties` 欄位，篩選條件的 SqlFilter 即是透過這個欄位來篩選的。

```
msg = Message('I am very important', custom_properties={'star': 5})
bus_service.send_topic_message('mytopic', msg)
```

由於這個訊息的屬性符合 star > 4 的條件，所以會傳入先前所定義的「ImportantMessages」訂閱中。

# 取出 Subscription 中的訊息

呼叫 `receive_subscription_message()` 可以從訂閱中取出一筆訊息，值得注意的是，**這則訊息在取出後便會從佇列中刪除**，這點與昨天介紹的 Service Bus Queues 相同。

```
msg = bus_service.receive_subscription_message('mytopic', 'ImportantMessages')
print(msg.body)
```

若您想要將這個動作分為兩階段，或是要 peek 下一則訊息，可以透過設定 `peek_Lock=Ture` 達到：

```
msg = receive_subscription_message('mytopic', 'ImportantMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

使用這種方式來讀取 Subscription 能夠擁有更高的容錯率，能夠避免因為處理過程中發生例外而造成訊息遺漏無法再被處理的問題。在訊息被取出時會將其 lock 住直到成功刪除，而您可以在例外發生的 catch 區段呼叫 Message 的 `unlock()` 方法來解除鎖定以讓其他取用者接收，或是等到逾時候由系統自動解鎖。

# 刪除 Topic 與 Subscription

若您需要刪除訂閱或是主題，可以分別透過 `delete_subscription()` 與 `delete_topic()` 方法來達成：

```
bus_service.delete_subscription('mytopic', 'ImportantMessages')
bus_service.delete_topic('mytopic')
```

# 後記

在這幾天的系列文章中，我們已經針對了 Azure SDK for Python 所提供的 API 做了大致的介紹，也可以滿足大部份的開發需求，而在下一篇文章中，將要持續向讀者介紹如何使用 Python 來操作 Azure 的其他服務，例如 DocumentDB。

# 參考資料

- 如何使用服務匯流排主題/訂閱, [http://azure.microsoft.com/zh-tw/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/](http://azure.microsoft.com/zh-tw/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/)
- Microsoft Azure SDK for Python, [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python)