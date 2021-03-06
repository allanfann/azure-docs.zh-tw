---
title: 部署使用者 VHD 的 Azure VM |Azure Marketplace
description: 說明如何部署使用者 VHD 映像來建立 Azure VM 執行個體。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e4da523fa54a513fe77fda037aea0a5fd530250b
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938250"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>從使用者 VHD 部署 Azure VM

本文說明如何使用提供的 Azure Resource Manager 範本和 Azure PowerShell 指令碼，來部署通用的 VHD 映像以建立新的 Azure VM 資源。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>VHD 部署範本

將適用於 [VHD 部署](cpp-deploy-json-template.md)的 Azure Resource Manager 範本複製到名為 `VHDtoImage.json` 的本機檔案。  編輯此檔案以提供下列參數值。 

|  **參數**             |   **說明**                                                              |
|  -------------             |   ---------------                                                              |
| resourceGroupName          | 現有的 Azure 資源群組名稱。  通常會使用與您金鑰保存庫相關聯的相同 RG  |
| TemplateFile               | 檔案 `VHDtoImage.json` 的完整路徑名稱                                    |
| userStorageAccountName     | 儲存體帳戶的名稱                                                    |
| sNameForPublicIP           | 公用 IP 的 DNS 名稱。 必須是小寫                                  |
| subscriptionId             | Azure 訂用帳戶識別碼                                                  |
| 位置                   | 資源群組的標準 Azure 地理位置                       |
| vmName                     | 虛擬機器的名稱                                                    |
| vaultName                  | 金鑰保存庫的名稱                                                          |
| vaultResourceGroup         | 金鑰保存庫的資源群組
| certificateUrl             | 憑證的 URL，包括儲存於金鑰保存庫的版本，例如：`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | 虛擬硬碟的 URL                                                   |
| vmSize                     | 虛擬機器執行個體的大小                                           |
| publicIPAddressName        | 公用 IP 位址的名稱                                                  |
| virtualNetworkName         | 虛擬網路的名稱                                                    |
| nicName                    | 適用於虛擬網路的網路介面卡名稱                     |
| adminUserName              | 系統管理員帳戶的使用者名稱                                          |
| adminPassword              | 系統管理員密碼                                                          |
|  |  |


## <a name="powershell-script"></a>PowerShell 指令碼

複製和編輯下列指令碼，以提供 `$storageaccount` 和 `$vhdUrl` 變數的值。  執行它，從您現有的通用 VHD 建立 Azure VM 資源。

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>後續步驟

VM 部署完成後，您就準備好來[認證您的 VM 映像](./cpp-certify-vm.md)。
