---
title: 管理 Azure Analysis Services | Microsoft Docs
description: 瞭解如何以 Azure 管理 Analysis Services 伺服器。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0bae06d46c2c96ba9dd058e9c2d380379523811c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065154"
---
# <a name="manage-analysis-services"></a>Azure Analysis Services
在 Azure 中建立 Analysis Services 伺服器之後，會有一些您必須立即或稍後執行的管理工作。 例如：執行資料重新整理處理作業、控制誰能夠存取您伺服器上的模型，或監視伺服器的健康狀態。 有些管理工作只能在 Azure 入口網站中執行，有些只能在 SQL Server Management Studio (SSMS) 中執行，也有些工作可以在這兩個位置中執行。

## <a name="azure-portal"></a>Azure 入口網站
[Azure 入口網站](https://portal.azure.com/)可讓您建立與刪除伺服器、監視伺服器資源、變更大小，以及管理誰能夠存取您的伺服器。  如果遇到问题，可提交支持请求。

![在 Azure 中取得伺服器名稱](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
在 Azure 中連線到您的伺服器，就如同連線到您自己組織中的伺服器執行個體。 您可從 SSMS 執行許多相同的工作，例如處理資料或建立處理指令碼、管理角色，以及使用 PowerShell。
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>下载并安装 SSMS
若要取得所有最新功能，並在連接到 Azure Analysis Services 伺服器時獲得最順暢的體驗，請確定您使用的是最新版的 SSMS。 

[下载 SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。


### <a name="to-connect-with-ssms"></a>连接 SSMS
 使用 SSMS 時，請在第一次連線到您伺服器之前，先確定您的使用者名稱包含在「Analysis Services 管理員」群組中。 若要深入了解，請參閱本文稍後的[伺服器管理員與資料庫使用者](#server-administrators-and-database-users)。

1. 連線之前，您必須先取得伺服器名稱。 在 **Azure 门户**中，单击“服务器”>“概述” > “服务器名称”，并复制服务器名称。
   
    ![在 Azure 中获取服务器名称](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. 在 SSMS >“对象资源管理器”中，单击“连接” > “Analysis Services”。
3. 在 [連線至伺服器] 對話方塊中，貼上伺服器名稱，然後在 [驗證] 中，選擇下列其中一種驗證類型：   
    > [!NOTE]
    > [驗證] 類型建議使用 [具 MFA 支援的 Active Directory - 通用]。

    > [!NOTE]
    > 如果您使用 Microsoft 帳戶、Live ID、Yahoo、Gmail 等來登入，請將密碼欄位保留空白。 您按 [連線] 之後系統會提示您輸入密碼。

    [Windows 驗證] 以使用您的 Windows 網域\使用者名稱和密碼認證。

    [Active Directory 密碼驗證] 以使用組織帳戶。 例如，當從未加入網域的電腦連線時。

    **具 MFA 支援的 Active Directory - 通用** 請使用 [非互動式或多重要素驗證](../sql-database/sql-database-ssms-mfa-authentication.md)。 
   
    ![在 SSMS 中連線](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>伺服器管理員和資料庫使用者
Azure Analysis Services 中存在两种类型的用户，即服务器管理员和数据库用户。 這兩種類型的使用者都必須位於 Azure Active Directory 中，而且必須由組織的電子郵件地址或 UPN 指定。 若要了解详细信息，请参阅[身份验证和用户权限](analysis-services-manage-users.md)。


## <a name="troubleshooting-connection-problems"></a>排查连接问题
使用 SSMS 進行連線時，如果遇到問題，您可能需要清除登入快取。 系統不會將任何資料快取到磁碟。若要清除快取，請將連線程序關閉並重新啟動。 

## <a name="next-steps"></a>後續步驟
如果您尚未將表格式模型部署到新伺服器，現在正是時候。 若要深入了解，請參閱 [Deploy to Azure Analysis Services](analysis-services-deploy.md) (部署至 Azure Analysis Services)。

如果您已將模型部署至您的伺服器，您便可以透過用戶端或瀏覽器與伺服器連線。 若要深入了解，請參閱 [Get data from Azure Analysis Services server](analysis-services-connect.md) (從 Azure Analysis Services 伺服器取得資料)。

