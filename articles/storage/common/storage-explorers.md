---
title: 與 Azure 儲存體搭配使用的工具 | Microsoft Docs
description: 可讓您檢視 Azure 儲存體資料並與其互動的工具清單。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: d7debbc760e103046ce9bb1a8bdf25a954d9891c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138562"
---
# <a name="azure-storage-client-tools"></a>Azure 存储客户端工具
Azure 儲存體的使用者經常想要能夠使用 Azure 儲存體用戶端工具，檢視他們資料或與其互動。 我們在下表中列出幾個可讓您這麼做的工具。 如果該區塊可以列舉及/或存取資料抽象，我們便在該區塊中放一個 "X"。 下表也會顯示該工具是否免費提供。 「試用」表示有免費試用版，但完整產品並非免費。 「Y/N」表示有某個版本可供免費使用，而另一個版本則需購買使用。

我們只提供可用的 Azure 儲存體用戶端工具的快照。 這些工具在功能上可能會繼續發展及成長。 如果有修正或更新，請留言讓我們知道。 如果您知道有應該在這裡的工具，也請留言讓我們知道，我們很樂意將它們加入。

**Microsoft Azure 存储客户端工具**

<table>
  <tr>
    <th rowspan="2">Azure 儲存體用戶端工具</th>
    <th rowspan="2">區塊 Blob</th>
    <th rowspan="2">分頁 Blob</th>
    <th rowspan="2">附加 Blob</th>
    <th rowspan="2">資料表</th>
    <th rowspan="2">队列</th>
    <th rowspan="2">檔案</th>
    <th rowspan="2">免費</th>
    <th colspan="4">平台</th>
  </tr>
  <tr>
    <td>Web</td>
    <td> Windows</td>
    <td>OSX</td>
    <td> Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure 入口網站</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://storageexplorer.com/">Microsoft Azure 存储资源管理器</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio 伺服器總管</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**協力廠商 Azure 儲存體工具**

我們尚未驗證下列協力廠商工具所宣稱的功能或品質，且其清單並不代表 Microsoft 背書。

<table>
  <tr>
    <th rowspan="2">Azure 儲存體用戶端工具</th>
    <th rowspan="2">區塊 Blob</th>
    <th rowspan="2">分頁 Blob</th>
    <th rowspan="2">附加 Blob</th>
    <th rowspan="2">資料表</th>
    <th rowspan="2">队列</th>
    <th rowspan="2">檔案</th>
    <th rowspan="2">免費</th>
    <th colspan="4">平台</th>
  </tr>
  <tr>
    <td>Web</td>
    <td> Windows</td>
    <td>OSX</td>
    <td> Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata:Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>试用</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate:Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web 儲存體總管 (英文)</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry 總管 (英文)</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>Y/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gapotchenko.com/cloudcombine">Cloud Combine (英文)</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>试用</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf:AzureXplorer、CloudXplorer、TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud (英文)</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>试用</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
