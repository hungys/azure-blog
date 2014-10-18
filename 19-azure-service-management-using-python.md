Day 19: 使用 Azure SDK for Python 管理 Azure 服務
==============================

# 前言

過去認為使用 Azure 部署應用程式的通常是 .NET 的開發者，但這幾年微軟為了扭轉這個印象，所以針對許多其他語言，尤其是開源界尤其喜歡使用的 Python、Ruby 或是 Node.js 推出對應的 SDK，而且這些 SDK 全部都開源在 GitHub 上，有助於 SDK 本身的發展。在接下來連續七篇文章中，筆者將示範如何使用 Python SDK 來管理或是使用 Azure 上的服務，希望能幫助到更多非 .NET 的開發者來使用 Azure 的服務。

# 安裝 Python SDK

![Python logo](https://raw.githubusercontent.com/hungys/azure-blog/master/media/19-azure-service-management-using-python/python-logo.png)

目前微軟官方將 Azure SDK for Python 開源在 [GitHub](https://github.com/Azure/azure-sdk-for-python) 上，若您想要安裝的話可以自行 clone 一份到本地：

```
$ git clone https://github.com/Azure/azure-sdk-for-python.git
$ cd ./azure-sdk-for-python
```

不過筆者在此推薦先建立一個 virtualenv，並使用 `pip` 來安裝套件：

```
(venv)$ pip install azure
```

目前 Python SDK 主要提供了以下五大功能：

- Tables
- Blobs
- Storage Queues
- Service Bus
- Service Management

在本系列的第一篇文章中將會介紹如何使用 SDK 來管理 Azure 上的服務，也就是 Service Management 的部分，而在後續幾篇才會開始介紹如何使用 Python 來存取到 Azure 上的其他服務。

# 建立憑證

在使用服務管理的 API 之前，我們必須先設定好相關的憑證，在此我們會以在 Mac 或 Linux 環境下自我簽署為例。您可以使用 OpenSSL 來建立管理憑證，在此需要建立兩個憑證，一個是用於伺服器的 cer 檔案，另一個則是用於用戶端的 pem 檔案。若要建立 pem 檔案，請使用下列指令：

```
$ openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

接著請使用剛剛產生的 mycert.pem 作為輸入，來建立一個 cer 檔案：

```
$ openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer
```

最後請開啟 Management Portal，在**「Settings」**頁面中的**「Management Certificates」**分頁點選**「Upload」**將剛剛產生好的 cer 檔案上傳到 Portal 上即可完成憑證的設定。

![Upload cert](https://raw.githubusercontent.com/hungys/azure-blog/master/media/19-azure-service-management-using-python/upload-cert.png)

# 使用服務管理 API

在開始使用 Service Management 的 API 之前，除了憑證之外您還需要取得您的訂閱 ID，這項資訊可以從 Management Portal 中的**「Settings」**頁面中找到，或是從您的訂閱管理頁面中取得。

![Get Subscription ID](https://raw.githubusercontent.com/hungys/azure-blog/master/media/19-azure-service-management-using-python/subscription-id.png)

準備好必要資訊之後，我們就可以開始使用 Python SDK 來管理 Azure 上的服務，在 SDK 中的 ServiceManagementService 就是用來操作相關管理功能的物件，在建立時請分別傳入訂閱 ID 以及 pem 憑證的路徑：

```
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = '<path_to_.pem_certificate>'

sms = ServiceManagementService(subscription_id, certificate_path)
```

接下來我們就可以開始嘗試第一個操作，首先要示範如何將目前服務所支援的「地區」列出，可以使用 `list_locations()` 方法：

```
result = sms.list_locations()
for location in result:
    print(location.name)
```

將這段程式執行起來應該可以得到類似下面的輸出，也就是目前 Azure 有建置資料中心的地區，可以看到其中包含了最新的日本資料中心：

```
South Central US
Central US
East US 2
East US
West US
North Central US
North Europe
West Europe
East Asia
Southeast Asia
Japan West
Japan East
Brazil South
```

接著筆者將示範如何使用 API 來獲取雲端服務的細節資訊，使用 `get_hosted_service_properties()` 方法可以幫助您取得特定雲端服務的屬性資訊：

```
hosted_service = sms.get_hosted_service_properties('ironman-nginx')

print('Service name:' + hosted_service.service_name)
print('Management URL:' + hosted_service.url)
print('Affinity group:' + hosted_service.hosted_service_properties.affinity_group)
print('Location:' + hosted_service.hosted_service_properties.location)
```

若將程式跑起來，應該可以成功取得該雲端服務的相關資訊：

```
Service name:ironman-nginx
Management URL:https://management.core.windows.net/20f56482-5c7d-424b-a753-8dad1b8bf932/services/hostedservices/ironman-nginx
Affinity group:
Location:East Asia
```

當然，若您要刪除某個雲端服務，同樣可以透過 `delete_hosted_service()` 方法來達成：

```
sms.delete_hosted_service('ironman-nginx')
```

# 學習使用 Python SDK

在前一小節我們簡單示範了使用 Service Management API，而因為篇幅的關係筆者不可能在本文介紹所有 API 及方法的使用，所以在接下來的內容會著重在帶領讀者來學習如何查閱相關文件或原始碼來瞭解 API 的使用。

目前 Azure SDK for Python 除了在 [GitHub](https://github.com/Azure/azure-sdk-for-python) 上有基本的操作教學之外，其他較為完整的應該是官方網站上「[如何從 Python 使用服務管理](http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-python-how-to-use-service-management/)」的這篇文章，不過這些都並非完整的 API 文件，文件的缺乏是目前 Azure SDK 較為可惜的地方。所以，接下來筆者將簡單介紹 Python SDK 的程式碼，好讓讀者可以輕鬆的透過 trace code 來瞭解相關 API 的使用方法。

# 剖析 Python SDK

在 Azure SDK for Python 中，其實本身也是透過 [REST API](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee460799.aspx) 來與 Azure 互動，先前我們曾與讀者介紹過的 ServiceManagementService 的原始碼可以在 [azure-sdk-for-python / azure / servicemanagement](https://github.com/Azure/azure-sdk-for-python/tree/master/azure/servicemanagement) 目錄下找到。

`ServiceManagementService` 這個 class 本身繼承了 `_ServiceManagementClient`，在這個 class 中主要定義了較高階、包裝好的 API 方法，例如我們先前曾用過的 `list_locations()` 以及 `get_hosted_service_properties()` 等等。然而這些 API 本身其實都是透過 REST 方法來向 Azure 請求資料的，以 `list_storage_account()` 的實作為例：

```
def list_storage_accounts(self):
    '''
    Lists the storage accounts available under the current subscription.
    '''
    return self._perform_get(self._get_storage_service_path(), StorageServices)
```

這個 API 本身又去呼叫了 `_perform_get()` 的方法來處理 GET 的請求，並分別傳入了兩個參數，第一個參數是 API 的路由，以 `_get_storage_service_path()` 來說，他是一個被定義在同個檔案的 Helper function:

```
def _get_storage_service_path(self, service_name=None):
    return self._get_path('services/storageservices', service_name)
```

`_get_path()` 則是定義在 `_ServiceManagementClient` 中，將相關參數例如訂閱 ID 填入以產生完整的 API 路由：

```
def _get_path(self, resource, name):
    path = '/' + self.subscription_id + '/' + resource
    if name is not None:
        path += '/' + _str(name)
    return path
```

接著我們再回到 `_perform_get()` 的第二個參數，這是用來表達 response type 的型別，以上面的例子來說，他傳入了 `StorageServices` 這個 class，這個型別的資料結構是被定義在 `__init__.py` 中：

```
class StorageServices(WindowsAzureData):

    def __init__(self):
        self.storage_services = _list_of(StorageService)

    def __iter__(self):
        return iter(self.storage_services)

    def __len__(self):
        return len(self.storage_services)

    def __getitem__(self, index):
        return self.storage_services[index]


class StorageService(WindowsAzureData):

    def __init__(self):
        self.url = ''
        self.service_name = ''
        self.storage_service_properties = StorageAccountProperties()
        self.storage_service_keys = StorageServiceKeys()
        self.extended_properties = _dict_of(
            'ExtendedProperty', 'Name', 'Value')
        self.capabilities = _scalar_list_of(str, 'Capability')
```

總結來說，在掌握了以上的 SDK 設計原則之後，您可以在 `servicemanagementservice.py` 中看到所有與服務管理相關的 API 定義與實作，而對於回傳的資料結構，則可以在 `__init__.py` 中查詢到對應的定義，如此一來即使官方目前還沒有完整的文件整理您依舊能夠輕鬆上手。

# 後記

本文簡單的介紹如何使用 Azure SDK for Python 來管理 Azure 上的服務，而因為官方文件的缺乏，所以我們將重點著重在帶領讀者如何熟悉此 SDK 的原始碼來建立自我學習的能力，雖說如此，還是希望微軟官方能夠提供較為完整的文件，來降低這個 SDK 的入門門檻。在接下來一系列的文章中，將會開始更深入的帶領讀者使用 SDK 來實際操作與運用 Azure 上的相關服務。

# 參考資料

- Microsoft Azure SDK for Python, [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python)
- 如何從 Python 使用服務管理, [http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-python-how-to-use-service-management/](http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-python-how-to-use-service-management/)
- Python Dev Center | Azure, [http://azure.microsoft.com/zh-tw/develop/python/](http://azure.microsoft.com/zh-tw/develop/python/)