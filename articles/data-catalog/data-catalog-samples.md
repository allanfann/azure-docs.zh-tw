---
title: Azure 資料目錄開發人員範例
description: 本文提供資料目錄 REST API 可用之開發人員範例的概觀。
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 0dc23edd-04d8-49fc-841e-d132fb109ce7
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 1f62a5583b7beef2dc535065a6c0d3bcb34fe7b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582715"
---
# <a name="data-catalog-developer-samples"></a>資料目錄開發人員範例
開始使用資料目錄 REST API 開發資料目錄應用程式。 資料目錄 REST API 是REST 架構 API，能夠以程式設計方式存取資料目錄資源，藉此註冊、加上註解，及以程式設計方式搜尋資料資產。

以下是 GitHub 的範例：

* [開始使用 Azure 資料目錄](https://azure.microsoft.com/resources/samples/data-catalog-dotnet-get-started/)
  
  本範例將示範如何向 Azure AD 驗證，以使用資料目錄 REST API 註冊、搜尋及刪除資料資產。
* [大量註冊以及加上註解](https://azure.microsoft.com/resources/samples/data-catalog-dotnet-excel-register-data-assets/)
  
  本範例將示範如何使用資料目錄 REST API 和 Open XML 從 Excel 活頁簿大量註冊資料資產。
* [匯入/匯出工具](https://azure.microsoft.com/resources/samples/data-catalog-dotnet-import-export/)
  
  一個範例，示範如何使用資料目錄 REST API 以從 Azure 資料目錄擷取資產，並將其序列化成檔案。 它也會示範如何取用一組已序列化為 JSON 的資產，並將其推送至目錄。 它支援使用搜尋查詢匯出目錄的子集。

* [大量匯入詞彙](https://azure.microsoft.com/resources/samples/data-catalog-bulk-import-glossary/)

    這個範例會示範如何將 CSV 檔案中的詞彙匯入 ADC 字彙。

