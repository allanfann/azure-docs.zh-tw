---
title: 包含檔案
description: 包含檔案
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/23/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: d75dbf8f2f3de6101358274ef41f1b229950633d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713521"
---
下表列出的觸發程序和繫結屬性可在 Azure Functions 類別庫專案中使用。 所有屬性都在 `Microsoft.Azure.WebJobs` 命名空間中。

| 觸發程序 | 輸入 | 輸出|
|------   | ------    | ------  |
| [BlobTrigger](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---attributes)| [Blob](../articles/azure-functions/functions-bindings-storage-blob.md#input---attributes)| [Blob](../articles/azure-functions/functions-bindings-storage-blob.md#output---attributes)|
| [CosmosDBTrigger](../articles/azure-functions/functions-bindings-cosmosdb.md#trigger---attributes)| [DocumentDB](../articles/azure-functions/functions-bindings-cosmosdb.md#input---attributes)| [DocumentDB](../articles/azure-functions/functions-bindings-cosmosdb.md#output---attributes) |
| [EventHubTrigger](../articles/azure-functions/functions-bindings-event-hubs.md#trigger---attributes)|| [EventHub](../articles/azure-functions/functions-bindings-event-hubs.md#output---attributes) |
| [HTTPTrigger](../articles/azure-functions/functions-bindings-http-webhook.md#trigger---attributes)|||
| [QueueTrigger](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---attributes)|| [佇列](../articles/azure-functions/functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](../articles/azure-functions/functions-bindings-service-bus.md#trigger---attributes)|| [ServiceBus](../articles/azure-functions/functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](../articles/azure-functions/functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](../articles/azure-functions/functions-bindings-external-file.md)| [ApiHubFile](../articles/azure-functions/functions-bindings-external-file.md)|
| |[MobileTable](../articles/azure-functions/functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](../articles/azure-functions/functions-bindings-mobile-apps.md#output---attributes) | 
| |[資料表](../articles/azure-functions/functions-bindings-storage-table.md#input---attributes)| [資料表](../articles/azure-functions/functions-bindings-storage-table.md#output---attributes)  | 
| ||[NotificationHub](../articles/azure-functions/functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](../articles/azure-functions/functions-bindings-twilio.md#attributes)| 