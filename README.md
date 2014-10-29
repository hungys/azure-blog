【鐵人賽 30 天】從開源角度認識 Microsoft Azure
================================

筆者在「[2014 第七屆 iT邦幫忙鐵人賽](http://ithelp.ithome.com.tw/ironman7)」中發表了一系列關於 Microsoft Azure 的技術文章，由於筆者一直以來非常熱衷於微軟的 solution，但同時也非常關注 open source 的相關專案，所以正巧利用這次的機會，在一系列文章中著重在從 open source 角度及非微軟自家技術的角度來介紹 Microsoft Azure。前半段主要著重在 IaaS 與 PaaS 服務的架設及使用，後半段則會著重在使用 open source SDK 來操作 Azure 所提供的 PaaS 服務，這部分將會以筆者較熟悉的 Python 為例，當然您也可以在 Azure 官網找到其他語言如 node.js 對應的學習資源。以下是本次系列文章的目錄。

# 目錄

- Day 1: [認識 Microsoft Azure](https://github.com/hungys/azure-blog/blob/master/01-introduction-to-microsoft-azure.md)
- Day 2: [當 Azure 擁抱開放](https://github.com/hungys/azure-blog/blob/master/02-when-azure-embrace-open.md)
- Day 3: [在 Azure 上快速架設開源 Web App 專案](https://github.com/hungys/azure-blog/blob/master/03-install-open-source-cms-on-azure.md)
- Day 4: [虛擬機器、雲端服務、網站服務比較](https://github.com/hungys/azure-blog/blob/master/04-compare-vm-cloudservices-websites.md)
- Day 5: [使用跨平台 CLI 管理 Azure (1)](https://github.com/hungys/azure-blog/blob/master/05-using-azure-xplat-cli-1.md)
- Day 6: [使用跨平台 CLI 管理 Azure (2)](https://github.com/hungys/azure-blog/blob/master/06-using-azure-xplat-cli-2.md)
- Day 7: [在 Azure 上使用 Git 分散式版本控制](https://github.com/hungys/azure-blog/blob/master/07-using-git-on-azure.md)
- Day 8: [使用 Dropbox 發佈專案至 Azure Websites](https://github.com/hungys/azure-blog/blob/master/08-using-dropbox-deploy-to-azure-websites.md)
- Day 9: [在 Azure 上架設 Linux VM 及 LAMP 服務](https://github.com/hungys/azure-blog/blob/master/09-install-lamp-on-azure-vm.md)
- Day 10: [在 Azure VM 上架設 nginx、MongoDB 服務](https://github.com/hungys/azure-blog/blob/master/10-install-nginx-mongodb-on-azure.md)
- Day 11: [在 Azure VM 上使用 gunicorn 部署 Flask 網站](https://github.com/hungys/azure-blog/blob/master/11-deploy-flask-by-gunicorn-on-azure.md)
- Day 12: [在 Azure 上快速建置 MongoDB 託管服務](https://github.com/hungys/azure-blog/blob/master/12-mongodb-managed-service-on-azure.md)
- Day 13: [在 Azure 上快速建置 Redis Cache 服務](https://github.com/hungys/azure-blog/blob/master/13-managed-redis-cache-on-azure.md)
- Day 14: [在 Azure 上使用內容傳遞網路 (CDN)](https://github.com/hungys/azure-blog/blob/master/14-using-azure-cdn.md)
- Day 15: [Azure API Management 服務簡介](https://github.com/hungys/azure-blog/blob/master/15-azure-api-management-overview.md)
- Day 16: [在 Azure VM 上安裝 IPython Notebook](https://github.com/hungys/azure-blog/blob/master/16-install-ipython-notebook-on-azure.md)
- Day 17: [在 Azure Websites 上設定 Python 執行環境](https://github.com/hungys/azure-blog/blob/master/17-configure-python-on-azure-websites.md)
- Day 18: [部署 Django 網站 - 以 VM 及 Azure Websites 為例](https://github.com/hungys/azure-blog/blob/master/18-deploy-django-on-azure-vm-and-websites.md)
- Day 19: [使用 Azure SDK for Python 管理 Azure 服務](https://github.com/hungys/azure-blog/blob/master/19-azure-service-management-using-python.md)
- Day 20: [使用 Python 操作 Azure Blob Service](https://github.com/hungys/azure-blog/blob/master/20-azure-blob-service-using-python.md)
- Day 21: [使用 Python 操作 Azure Table Service](https://github.com/hungys/azure-blog/blob/master/21-azure-table-service-using-python.md)
- Day 22: [使用 Python 操作 Azure Queue Storage Service](https://github.com/hungys/azure-blog/blob/master/22-azure-queue-storage-service-using-python.md)
- Day 23: [使用 Python 操作 Azure Service Bus Queues](https://github.com/hungys/azure-blog/blob/master/23-azure-service-bus-queues-using-python.md)
- Day 24: [使用 Python 操作 Service Bus Topics/Subscriptions](https://github.com/hungys/azure-blog/blob/master/24-azure-service-bus-topics-subscriptions-using-python.md)
- Day 25: [使用 Python 操作 Azure DocumentDB](https://github.com/hungys/azure-blog/blob/master/25-azure-documentdb-using-python.md)
- Day 26: [使用 Azure Mobile Services 快速建立 App 後端服務](https://github.com/hungys/azure-blog/blob/master/26-azure-mobile-services-overview.md)
- Day 27: [使用 Azure Notification Hubs 建立面向數百萬裝置的推播服務](https://github.com/hungys/azure-blog/blob/master/27-azure-notification-hubs-overview.md)
- Day 28: [在 Azure 上使用 Docker](https://github.com/hungys/azure-blog/blob/master/28-using-docker-on-azure.md)
- Day 29: [探討以開源方案在 Azure 上建置完整服務的可行性](https://github.com/hungys/azure-blog/blob/master/29-possibility-to-build-open-source-service-on-azure.md)
- Day 30: [Azure 學習資源](https://github.com/hungys/azure-blog/blob/master/30-azure-study-resouces.md)
