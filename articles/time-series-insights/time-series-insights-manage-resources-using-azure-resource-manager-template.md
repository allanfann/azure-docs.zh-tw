---
title: 如何使用 Azure Resource Manager 範本管理 Azure 時間序列深入解析環境 | Microsoft Docs
description: 本文說明如何使用 Azure Resource Manager 以程式設計方式管理 Azure 時間序列深入解析環境。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: f5e350e8a9093936f1e747afda7c3192b4d8368d
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471718"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本建立時間序列深入解析資源

本文說明如何使用 Azure Resource Manager 範本、PowerShell 和「時間序列深入解析」資源提供者來建立和部署「時間序列深入解析」資源。

「時間序列深入解析」支援下列資源：

   | 資源 | 說明 |
   | --- | --- |
   | 環境 | Time Series Insights 環境是事件從事件訊息代理程式讀取、 儲存，而且可供查詢的邏輯群組。 如需詳細資訊，請參閱[規劃您的 Azure Time Series Insights 環境](time-series-insights-environment-planning.md) |
   | 事件來源 | 事件來源是與事件訊息代理程式的連線，「時間序列深入解析」會從該訊息代理程式讀取事件，並將事件內嵌到環境中。 目前支援的事件來源為 IoT 中樞和事件中樞。 |
   | 參考資料集 | 參考資料集可提供和環境中事件有關的中繼資料。 參考資料集中的中繼資料將會在輸入過程中與事件結合。 參考資料集會由它們的事件索引鍵屬性定義為資源。 構成參考資料集的實際中繼資料會透過資料層 API 上傳或修改。 |
   | 存取原則 | 存取原則可授與下列權限：發出資料查詢、在環境中操作參考資料，以及共用與環境相關聯的已儲存查詢和檢視方塊。 如需詳細資訊，請參閱[授與至 Time Series Insights 環境使用 Azure 入口網站的資料存取](time-series-insights-data-access.md) |

Resource Manager 範本是一個 JSON 檔案，定義了資源群組中資源的基礎結構和設定。 下列文件描述更詳細的範本檔案：

- [Azure Resource Manager 概觀 - 範本部署](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/resource-group-template-deploy.md)
- [Microsoft.TimeSeriesInsights 資源類型](/azure/templates/microsoft.timeseriesinsights/allversions)

[201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) 快速入門範本已在 GitHub 上發行。 此範本會建立一個「時間序列深入解析」環境、一個設定為從事件中樞取用事件的子系事件來源，以及授與環境資料存取權的存取原則。 如果未指定現有事件中樞，就會隨著部署建立一個事件中樞。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>指定部署範本和參數

以下程序說明如何使用 PowerShell 來部署 Azure Resource Manager 範本，此範本會建立一個「時間序列深入解析」環境、一個設定為從事件中樞取用事件的子系事件來源，以及授與環境資料存取權的存取原則。 如果未指定現有事件中樞，就會隨著部署建立一個事件中樞。

1. 依照下列指示安裝 Azure PowerShell：[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。

1. 從 GitHub 再製或複製 [201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) 範本。

   * 建立參數檔案

     若要建立參數檔案，請複製 [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) 檔案。

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * 必要參數

     | 參數 | 說明 |
     | --- | --- |
     | eventHubNamespaceName | 來源事件中樞的命名空間。 |
     | 事件中樞名稱 | 來源事件中樞的名稱。 |
     | consumerGroupName | 「時間序列深入解析」服務將用來從事件中樞讀取資料的取用者群組名稱。 **注意：** 為避免資源爭用，此取用者群組必須專用於「時間序列深入解析」服務，而不和其他讀取者共用。 |
     | environmentName | 環境的名稱。 名稱不得包含： `<`， `>`， `%`， `&`， `:`， `\\`， `?`， `/`，和任何控制字元。 允許所有其他字元。|
     | eventSourceName | 事件來源子系資源的名稱。 名稱不得包含： `<`， `>`， `%`， `&`， `:`， `\\`， `?`， `/`，和任何控制字元。 允許所有其他字元。 |

    <div id="optional-parameters"></div>

   * 選擇性參數

     | 參數 | 說明 |
     | --- | --- |
     | existingEventHubResourceId | 現有事件中樞的選擇性資源識別碼，將透過事件來源連線至「時間序列深入解析」環境。 **注意：** 部署範本的使用者必須擁有在事件中樞中執行 listkey 作業的權限。 如果沒有傳遞任何值，將會由範本建立新的事件中樞。 |
     | environmentDisplayName | 要在工具或使用者介面中顯示的選擇性易記名稱，而不是環境名稱。 |
     | environmentSkuName | SKU 的名稱。 如需詳細資訊，請參閱[時間序列深入解析定價頁面](https://azure.microsoft.com/pricing/details/time-series-insights/)。  |
     | environmentSkuCapacity | SKU 的單位容量。 如需詳細資訊，請參閱[時間序列深入解析定價頁面](https://azure.microsoft.com/pricing/details/time-series-insights/)。|
     | environmentDataRetentionTime | 查詢時將能使用環境事件的最小時間範圍。 指定的值必須是 ISO 8601 格式，例如`P30D`30 天的保留原則。 |
     | eventSourceDisplayName | 要在工具或使用者介面中顯示的選擇性易記名稱，而不是事件來源名稱。 |
     | eventSourceTimestampPropertyName | 將用來作為事件來源時間戳記的事件屬性。 如果沒有為 timestampPropertyName 指定值，或如果指定的是空值或空字串，將會使用事件建立時間。 |
     | eventSourceKeyName | 「時間序列深入解析」服務將用來連線至事件中樞的共用存取金鑰名稱。 |
     | accessPolicyReaderObjectIds | Azure AD 中使用者或應用程式的物件識別碼清單，這些使用者和應用程式應擁有環境讀取者存取權。 可取得服務主體 objectId，請呼叫**Get AzADUser**或**Get AzADServicePrincipal** cmdlet。 尚未支援為 Azure AD 群組建立存取原則。 |
     | accessPolicyContributorObjectIds | Azure AD 中使用者或應用程式的物件識別碼清單，這些使用者和應用程式應擁有環境參與者存取權。 可取得服務主體 objectId，請呼叫**Get AzADUser**或**Get AzADServicePrincipal** cmdlet。 尚未支援為 Azure AD 群組建立存取原則。 |

   * 例如，以下參數檔案會用來建立環境和從現有事件中樞讀取事件的事件來源。 它也會建立兩個存取原則，將環境存取權授與參與者。

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }    
         }
     }
     ```
  
    * 如需詳細資訊，請參閱[參數](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)一文。

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>使用 PowerShell 在本機部署快速入門範本

> [!IMPORTANT]
> 下面顯示的命令列作業描述[Az PowerShell 模組](https://docs.microsoft.com/powershell/azure/overview)。

1. 在 PowerShell 中登入您的 Azure 帳戶。

    * 從 PowerShell 提示字元中執行下列命令：

      ```powershell
      Connect-AzAccount
      ```

    * 系統會提示您登入您的 Azure 帳戶。 登入之後，執行下列命令以檢視可用的訂用帳戶：

      ```powershell
      Get-AzSubscription
      ```

    * 這個命令會傳回可用的 Azure 訂用帳戶清單。 執行下列命令為目前的工作階段選擇訂用帳戶。 以您要使用的 Azure 訂用帳戶 GUID 取代 `<YourSubscriptionId>`：

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. 如果沒有資源群組，請建立一個新的。

   * 如果您沒有現有的資源群組中，建立新的資源群組**新增 AzResourceGroup**命令。 提供您要使用的資源群組名稱和位置。 例如：

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * 如果成功，就會顯示新資源群組的摘要。

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. 測試部署。

   * 執行 `Test-AzResourceGroupDeployment` Cmdlet 驗證部署。 測試部署時，請提供與執行部署時完全一致的參數。

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. 建立部署

    * 若要建立新的部署，請執行 `New-AzResourceGroupDeployment` Cmdlet，並於提示出現時提供必要的參數。 參數會包含部署的名稱、資源群組的名稱，以及範本檔案的路徑或 URL。 如未指定 **Mode** 參數，即會使用預設值 **Incremental**。 如需詳細資訊，請參閱[累加部署與完整部署](../azure-resource-manager/deployment-modes.md)。

    * 以下命令會提示您在 PowerShell 視窗中輸入五個必要參數︰

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
      ```

    * 若要改為指定參數檔案，使用下列命令：

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * 執行部署 Cmdlet 時，您也可以使用內嵌參數。 命令如下所示︰

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * 若要執行[完整](../azure-resource-manager/deployment-modes.md)部署，請將 **Mode** 參數設為 **Complete**：

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. 驗證部署

    * 如果資源成功部署，PowerShell 視窗中就會顯示部署的摘要︰

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 5/8/2019 10:28:34 PM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. 透過 Azure 入口網站部署快速入門範本

   * GitHub 上快速入門範本的首頁也包括一個 [部署至 Azure] 按鈕。 按一下它即可在 Azure 入口網站中開啟一個 [自訂部署] 頁面。 您可以從這個頁面的[必要參數](#required-parameters)或[選擇性參數](#optional-parameters)表格輸入或選擇 每個參數的值。 填好設定值之後，按一下 [購買] 按鈕就會起始範本部署。
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>後續步驟

- 如需使用 REST API 以程式設計方式管理「時間序列深入解析」資源的相關資訊，請參閱[時間序列深入解析管理](https://docs.microsoft.com/rest/api/time-series-insights-management/) \(英文\)。
