ㄓㄨDay 20: 使用 Python 操作 Azure Blob Service
===================================

# 前言

在前一篇文章中我們簡單介紹了 Azure SDK for Python，並示範了 Management Service API 的使用以及設計，本文則將開始以 Azure 上的服務為重點，示範如何使用 SDK 來操作這些服務。

# 什麼是 Blob Service?

Azure Blob Service 是一個專門用來儲存非結構化的大型二進位物件的服務，而且可以透過 HTTP 或 HTTPS 來存取這些資料，此外，您亦可設定為非公開的儲存容器僅提供內部應用程式來使用。在 Blob Service 中，主要包含三個主要元件，依據層級區分分別為 Account、Container 及 Blob：

- **儲存體帳戶 (Account)**: 一個儲存體帳戶代表一個儲存體，在使用相關 API 時均需要提供該帳戶的名稱以及 Key。
- **容器 (Container)**: 容器是用來放置 Blob 二進位資料的元件，一個帳戶可以擁有不限數量的容器，而容器中也可以儲存無限制的 Blob。此外，容器又分為 Public Container 及 Private Container，前者可透過公開的 HTTP 端點來存取 Blob。
- **二進位資料 (Blob)**: Blob 可以是任何類型的檔案或資料，大部份的檔案皆屬於區塊 Blob，而單一區塊的 Blob 上限為 200GB。在 Container 中並沒有像一般作業系統有資料夾的概念，若有這樣的需求，可以直接在 Blob 的名稱中使用斜線來區隔，在存取時便有類似資料夾的效果。

上面有提到若 Container 是公開的話，可以透過 HTTP 端點來存取，其中 URL 形式為 `http://<storage account>.blob.core.windows.net/<container>/<blob>`。 

# Blob Service API

Blob Service API 包含在 Azure SDK for Python 當中，相關的原始碼存放在 [azure-sdk-for-python / azure / storage](https://github.com/Azure/azure-sdk-for-python/tree/master/azure/storage) 底下。類似於之前介紹的 Management Service API，在 `storageclient.py` 中的 `StorageClient` 類別實作了基本的 REST 操作方法，而開發者則可以直接透過 `blobservice.py` 中繼承 `StorageClient` 的 `BlobService` 類別來產生一個物件操作儲存體服務。

# 建立 BlobService 實體

在 Python SDK 中關於 Blob Service 的操作皆是透過 BlobService 這個類別，在建立服務實體之前您需要準備好儲存體帳戶的名稱以及 Access Key，這些資訊可以從 Management Portal 取得。

![Get keys](https://raw.githubusercontent.com/hungys/azure-blog/master/media/20-azure-blob-service-using-python/get-keys.png)

接下來便可以傳入必要的參數來建立一個 BlobService 實體：

```
from azure.storage import *
blob_service = BlobService(account_name='myaccount', account_key='mykey')
```

完成之後您就可以使用 blob_service 這個物件來進行相關操作。

# 管理 Container

若要建立一個 Container，可以使用 `create_container()` 方法，預設會建立一個 Private Container：

```
blob_service.create_container('mycontainer')
```

若要指定存取等級 (ACL)，您可以在呼叫時特別傳入 `x_ms_blob_public_access` 欄位：

```
blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 
```

即使一開始沒有指定存取等級，您依舊可以使用 `set_container_acl()` 方法來隨時變更設定：

```
blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')
```

除此之外，在 SDK 中還提供了其他相關的 API 可以用來操作，例如：

- `list_containers()`: 列出所有 Container
- `get_container_acl(container_name)`: 取得 Container 存取等級
- `get_container_properties(container_name)`: 取得 Container 所有屬性
- `get_container_metadata(container_name)`: 取得 Container 的中繼資訊
- `set_container_metadata(container_name)`: 透過將一個 dictionary 物件傳入 `x_ms_meta_name_values` 欄位來設定 Container 的中繼資訊

當然，您也可以透過程式碼來刪除特定 Container：

```
blob_service.delete_container('mycontainer')
```

# 上傳 Blob

若您要上傳的檔案小於 64MB，可以直接使用單一 API 呼叫來達成上傳的目的：

```
myblob = open(r'test.txt', 'r').read()
blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')
```

其中，傳入 `put_blob()` 的第一個參數為目的地的 Container 名稱，第二個參數則為 Blob 的命名，若您要產生類似資料夾分層的效果，請在 Blob name 中自行加入斜線，最後則是傳入一個串流並指定為一個區塊 Blob，本文將不會針對分頁 Blob 做介紹。

註：對於超過 64MB 的檔案，您必須以不大於 4MB 的區塊大小來分批上傳，相關的 block 切割及操作可以參考 Azure 官方網站中「[如何從 Python 使用 Blob 儲存體服務](http://azure.microsoft.com/zh-tw/documentation/articles/storage-python-how-to-use-blob-storage/)」一文對於「上傳及下載大型 Blob」的解說。

# 列出容器中所有 Blob

使用 `list_blobs()` 方法可以回傳一個包含該 Container 底下所有 Blob 的一個清單 (List)。

```
blobs = blob_service.list_blobs('mycontainer')
```

blobs 中的每一個元素都是一個 Blob 物件實體，在 `__init__.py` 中有對其資料結構做了明確的定義，您可以自行存取其中的欄位：

```
class Blob(WindowsAzureData):

    ''' Blob class. '''

    def __init__(self):
        self.name = u''
        self.snapshot = u''
        self.url = u''
        self.properties = BlobProperties()
        self.metadata = {}


class BlobProperties(WindowsAzureData):

    ''' Blob Properties '''

    def __init__(self):
        self.last_modified = u''
        self.etag = u''
        self.content_length = 0
        self.content_type = u''
        self.content_encoding = u''
        self.content_language = u''
        self.content_md5 = u''
        self.xms_blob_sequence_number = 0
        self.blob_type = u''
        self.lease_status = u''
        self.lease_state = u''
        self.lease_duration = u''
        self.copy_id = u''
        self.copy_source = u''
        self.copy_status = u''
        self.copy_progress = u''
        self.copy_completion_time = u''
        self.copy_status_description = u''
```

# 下載 Blob

若要下載 Blob，可以呼叫 `get_blob()` 方法來獲取一個串流物件並寫入檔案：

```
blob = blob_service.get_blob('mycontainer', 'myblob')
with open(r'out.txt', 'w') as f:
    f.write(blob)
````

或者，您也可以使用 BlobService 中的 `make_blob_url(self, container_name, blob_name)` 方法來產生一個 URL 並自行將檔案下載至檔案系統。

# 刪除 Blob

當然，您也可以透過 API 來刪除特定的 Blob 物件：

```
blob_service.delete_blob('mycontainer', 'myblob') 
```

# 後記

本文針對如何使用 Azure SDK for Python 來操作 Blob Service 做了大致的介紹，若您有瞭解更細部的 API 呼叫相關說明，例如 optional 的參數設定，建議您直接前往 GitHub 上 [azure-sdk-for-python / azure / storage](https://github.com/Azure/azure-sdk-for-python/tree/master/azure/storage) 的部分參閱 `blobservice.py` 中對於各個 API 方法的定義。

# 參考資料

- 如何從 Python 使用 Blob 儲存體服務, [http://azure.microsoft.com/zh-tw/documentation/articles/storage-python-how-to-use-blob-storage/](http://azure.microsoft.com/zh-tw/documentation/articles/storage-python-how-to-use-blob-storage/)
- Microsoft Azure SDK for Python, [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python)