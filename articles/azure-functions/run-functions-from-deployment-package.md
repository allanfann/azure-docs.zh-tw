---
title: 從套件執行 Azure Functions | Microsoft Docs
description: 讓 Azure Functions 執行階段藉由掛接部署套件檔案 (內含函式應用程式的專案檔) 來執行函式。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: glenga
ms.openlocfilehash: 88e5f1ac7834caa32302a3817e1779d0d733a7b3
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787552"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>從套件檔案執行 Azure Functions

> [!NOTE]
> 本文所述的功能不适用于[应用服务计划](functions-scale.md#app-service-plan)中在 Linux 上运行的函数应用。

在 Azure 中，您可以直接從函式應用程式中的部署套件檔案執行函式。 另一個選項是將您的檔案部署在函式應用程式的 `d:\home\site\wwwroot` 目錄中。

本文說明從套件執行函式的優點。 文中也會說明如何在函式應用程式中啟用這項功能。

## <a name="benefits-of-running-from-a-package-file"></a>從套件檔案執行的優點
  
從套件檔案執行有幾項優點：

+ 減少檔案複製鎖定問題的風險。
+ 可以部署到生產應用程式 (透過重新啟動)。
+ 您可以確定在應用程式中執行的檔案。
+ 提升 [Azure Resource Manager 部署](functions-infrastructure-as-code.md)的效能。
+ 可縮短冷啟動時間，特別是針對具有大型 npm 套件樹狀結構的 JavaScript 函式。

如需詳細資訊，請參閱[此公告](https://github.com/Azure/app-service-announcements/issues/84)。

## <a name="enabling-functions-to-run-from-a-package"></a>讓函式能夠從套件執行

若要讓函式應用程式能夠從套件執行，您只需要將 `WEBSITE_RUN_FROM_PACKAGE` 設定新增至函式應用程式的設定。 `WEBSITE_RUN_FROM_PACKAGE` 設定可具有下列其中一個值：

| Value  | 說明  |
|---------|---------|
| **`1`**  | 建议用于在 Windows 上运行的函数应用。 從函式應用程式中 `d:\home\data\SitePackages` 資料夾內的套件檔案執行。 如果不[使用 zip deploy 进行部署](#integration-with-zip-deployment)，则此选项要求该文件夹同时包含名为 `packagename.txt` 的文件。 此檔案只會包含資料夾內套件檔案的名稱，且不含任何空白字元。 |
|**`<url>`**  | 所要執行的特定套件檔案所在的位置。 使用 Blob 儲存體時，請搭配使用私用容器與[共用存取簽章 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) 來讓 Functions 執行階段能夠存取套件。 您可以使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)將套件檔案上傳至 Blob 儲存體帳戶。         |

> [!CAUTION]
> 在 Windows 上运行函数应用时，外部 URL 选项会导致糟糕的冷启动性能。 将函数应用部署到 Windows 时，应将 `WEBSITE_RUN_FROM_PACKAGE` 设置为 `1` 并通过 zip 部署进行发布。

下圖顯示的函式應用程式已設定為要從裝載於 Azure Blob 儲存體的 .zip 檔案來執行：

![WEBSITE_RUN_FROM_ZIP 應用程式設定](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 目前只支援 .zip 套件檔案。

## <a name="integration-with-zip-deployment"></a>與 zip 部署的整合

[zip 部署][Zip deployment for Azure Functions]是 Azure App Service 的一項功能，可讓您將函式應用程式專案部署至 `wwwroot` 目錄。 專案會封裝為 .zip 部署檔案。 您可以使用相同的 API 將套件部署至 `d:\home\data\SitePackages` 資料夾。 `WEBSITE_RUN_FROM_PACKAGE` 應用程式設定值若為 `1`，zip 部署 API 就會將套件複製到 `d:\home\data\SitePackages` 資料夾，而不會將檔案解壓縮到 `d:\home\site\wwwroot`。 它也會建立 `packagename.txt` 檔案。 然後，函式應用程式就會在重新啟動後從套件執行，且 `wwwroot` 會變成唯讀。 如需 zip 部署的詳細資訊，請參閱 [Azure Functions 的 zip 部署](deployment-zip-push.md)。

## <a name="adding-the-websiterunfrompackage-setting"></a>新增 WEBSITE_RUN_FROM_PACKAGE 設定

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>疑難排解

- 執行從套件可讓`wwwroot`唯讀，因此這個目錄中寫入檔案時，您會收到錯誤。
- 不支援 tar 和 gzip 格式。
- 這項功能無法使用本機快取組成。
- 為了改善冷啟動效能，請使用本機的 Zip 選項 (`WEBSITE_RUN_FROM_PACKAGE`= 1)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 的持續部署](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
