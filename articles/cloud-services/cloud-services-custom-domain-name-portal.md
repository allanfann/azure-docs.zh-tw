---
title: 在雲端服務中設定自訂網域名稱 | Microsoft Docs
description: 了解如何藉由 DNS 設定，公開您的 Azure 應用程式或資料到自訂網域的網際網路上。  這些範例使用 Azure 入口網站。
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 5783a246-a151-4fb1-b488-441bfb29ee44
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 2255004ae8cd92473b5fe71b44cccb79021a8bf7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337443"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>設定 Azure 雲端服務的自訂網域名稱
當您建立雲端服務時，Azure 會將它指派給 **cloudapp.net**的子網域。 例如，如果您的雲端服務的名稱為 "contoso"，您的使用者可以透過 URL (如 http://contoso.cloudapp.net) 存取應用程式。 Azure 也會指派虛擬 IP 位址。

不過，您也可以在自己的網域名稱 (例如 **contoso.com**) 上公開您的應用程式。 本文說明如何保留或設定雲端服務 Web 角色的自訂網域名稱。

您已經了解什麼是 CNAME 和 A 記錄嗎？ [跳過說明](#add-a-cname-record-for-your-custom-domain)。

> [!NOTE]
> 此工作的程序適用於 Azure 雲端服務。 對於應用程式服務，請參閱[將現有的自訂 DNS 名稱對應至 Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md)。 對於儲存體帳戶，請參閱[針對 Azure Blob 儲存體端點設定自訂網域名稱](../storage/blobs/storage-custom-domain-name.md)。
> 
> 

<p/>

> [!TIP]
> 快速完成啟用 -- 使用全新的 Azure [引導式逐步解說](https://support.microsoft.com/kb/2990804)！  在彈指之間完成自訂網域名稱的關聯，以及與 Azure 雲端服務或 Azure 網站之間的通訊 (SSL) 保護。
> 
> 

## <a name="understand-cname-and-a-records"></a>了解 CNAME 和 A 記錄
CNAME (或別名記錄) 和 A 記錄都可讓您將網域名稱和特定的伺服器 (在此案例中為服務) 產生關聯，但運作方式不同。 針對 Azure 雲端服務來使用 A 記錄時，在決定使用何者之前，還有一些事項應該考慮。

### <a name="cname-or-alias-record"></a>CNAME 或別名記錄
CNAME 記錄對應*特定*網域，例如**contoso.com**或是**www\.contoso.com**，正式網域名稱。 在此案例中，Canonical 網域名稱為 Azure 主控應用程式的 **[myapp].cloudapp.net** 網域名稱。 CNAME 建立之後還會建立 **[myapp].cloudapp.net**的別名。 CNAME 項目會自動解析成 **[myapp].cloudapp.net** 服務的 IP 位址，就算雲端服務的 IP 位址變更，您也不需要採取任何動作。

> [!NOTE]
> 某些網域註冊機構只允許您對應子網域，當使用 CNAME 記錄，例如 www\.contoso.com，並不是根名稱，例如 contoso.com。 如需 CNAME 記錄的詳細資訊，請參閱註冊機構提供的文件、[維基百科 CNAME 記錄條目](https://en.wikipedia.org/wiki/CNAME_record)，或 [IETF 網域名稱 - 實作與規格](https://tools.ietf.org/html/rfc1035)文件。

### <a name="a-record"></a>A 記錄
*A*記錄將網域對應，例如**contoso.com**或是**www\.contoso.com**，*或萬用字元網域*例如**\*。 contoso.com**，為 IP 位址。 以 Azure 雲端服務而言，就是指服務的虛擬 IP。 因此於 CNAME 記錄，A 記錄的主要優點是，您可以有一個項目使用萬用字元，例如\* **。 contoso.com**，例如處理多個子網域的要求**mail.contoso.com**， **login.contoso.com**，或**www\.contso.com**。

> [!NOTE]
> 因為 A 記錄會對應至靜態 IP 位址，所以無法自動解析雲端服務 IP 位址的變更。 第一次將雲端服務部署到空的位置時 (生產或預備)，將會配置雲端服務所使用的 IP 位址。如果刪除此位置的部署，則 Azure 會釋放此 IP 位址，而未來再部署到此位置時，可能會給予新的 IP 位址。
> 
> 在預備和生產部署之間切換時，或就地升級現有的部署時，指定部署位置 (生產或預備) 的 IP 位址會保留下來，相當方便。 如需關於執行這些動作的詳細資訊，請參閱 [如何管理雲端服務](cloud-services-how-to-manage-portal.md)。
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>新增自訂網域的 CNAME 記錄
若要建立 CNAME 記錄，您必須使用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。 各註冊機構指定 CNAME 記錄的方法都很類似，只是稍微不同，但概念都一樣。

1. 使用其中一種方法來尋找指派給雲端服務的 **.cloudapp.net** 網域名稱。

   * 登录到 [Azure 门户]，选择云服务，查看“概述”部分，然后找到“站点 URL”条目。

       ![快速瀏覽區段，其中顯示網站 URL][csurl]

       **或者**
   * 安裝並設定 [Azure Powershell](/powershell/azure/overview)，然後使用下列命令：

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     請將任一方法傳回的 URL 中所使用的網域名稱儲存下來，建立 CNAME 記錄時需要用到。
2. 登入 DNS 註冊機構的網站，並移至 DNS 管理頁面。 在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。
3. 現在找出可選取或輸入 CNAME 的地方。 您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。 請尋找 **CNAME**、**Alias** 或 **Subdomains** 之類的字。
4. 您也必須提供網域或子網域別名的 CNAME，例如**www**如果您想要建立別名**www\.customdomain.com**。 如果要建立根網域的別名，註冊機構的 DNS 工具中可能會以 '**\@**' 符號列出此別名。
5. 接著，您必須提供正式主機名稱，在此案例中為應用程式的 **cloudapp.net** 網域。

例如，下列 CNAME 記錄會轉送所有的流量，從**www\.contoso.com**要**contoso.cloudapp.net**，您已部署的應用程式的自訂網域名稱：

| 別名/主機名稱/子網域 | 正式網域 |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> 訪客**www\.contoso.com**絕對看真正的主機 (contoso.cloudapp.net)，所以不會看不到使用者轉送過程。
> 
> 上述範例僅適用於 **www** 子網域的流量。 因為 CNAME 記錄不能使用萬用字元，所以您必須為每一個網域/子網域建立一個 CNAME。 如果要將來自子網域 (例如 *.contoso.com) 的流量導向您的 cloudapp.net 位址，您可以在 DNS 設定中設定 [URL 重新導向] 或 [URL 轉送] 項目，或建立 A 記錄。

## <a name="add-an-a-record-for-your-custom-domain"></a>新增自訂網域的 A 記錄
若要建立 A 記錄，您必須先找出雲端服務的虛擬 IP 位址。 然後，利用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。 每个注册机构指定 A 记录的方法类似但略有不同，但概念是相同的。

1. 使用下列其中一種方法取得雲端服務的 IP 位址。

   * 登录到 [Azure 门户]，选择云服务，查看“概述”部分，然后找到“公共 IP 地址”条目。

       ![快速瀏覽區段，其中顯示 VIP][vip]

       **或**
   * 安裝並設定 [Azure Powershell](/powershell/azure/overview)，然後使用下列命令：

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     建立 A 記錄時需要用到此 IP 位址，請儲存下來。
2. 登录到 DNS 注册机构的网站，并转到用于管理 DNS 的页面。 在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。
3. 現在找出可選取或輸入 A 記錄的地方。 您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。
4. 選取或輸入將使用此 A 記錄的網域或子網域。 例如，選取**www**如果您想要建立別名**www\.customdomain.com**。 如果要為所有子網域建立萬用字元項目，請輸入 '*****'。 這會涵蓋所有子網域，例如**mail.customdomain.com**， **login.customdomain.com**，並**www\.customdomain.com**。

    如果要建立根網域的 A 記錄，註冊機構的 DNS 工具中可能會以 '**\@**' 符號列出此別名。
5. 在提供的欄位中，輸入雲端服務的 IP 位址。 這樣會將 A 記錄中使用的網域項目與雲端服務部署的 IP 位址產生關聯。

例如，下列 A 記錄會將來自 **contoso.com** 的所有流量轉送至 **137.135.70.239** (已部署之應用程式的 IP 位址)：

| 主機名稱/子網域 | IP 位址 |
| --- | --- |
| \@ |137.135.70.239 |

此範例示範建立根網域的 A 記錄。 如果想要建立萬用字元項目來涵蓋所有子網域，請輸入 '*****' 做為子網域。

> [!WARNING]
> 依預設，Azure 中的 IP 位址是動態的。 您可能想要使用 [保留的 IP 位址](../virtual-network/virtual-networks-reserved-public-ip.md) ，以確保您的 IP 位址不會變更。
> 
> 

## <a name="next-steps"></a>後續步驟
* [如何管理云服务](cloud-services-how-to-manage-portal.md)
* [如何將 CDN 內容對應至自訂網域](../cdn/cdn-map-content-to-custom-domain.md)
* [雲端服務的一般設定](cloud-services-how-to-configure-portal.md)。
* 了解如何 [部署雲端服務](cloud-services-how-to-create-deploy-portal.md)。
* 配置 [SSL 证书](cloud-services-configure-ssl-certificate-portal.md)。

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure 门户]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
