---
title: Azure AD Connect：必要條件和硬體 | Microsoft Docs
description: 本主題描述 Azure AD Connect 的必要條件和硬體需求。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f7219578932a259f48b0109d433dcba9ff28d1f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508058"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect 的必要條件
本主題描述 Azure AD Connect 的必要條件和硬體需求。

## <a name="before-you-install-azure-ad-connect"></a>安裝 Azure AD Connect 之前
安裝 Azure AD Connect 之前，您需要注意一些事項。

### <a name="azure-ad"></a>Azure AD
* Azure AD 租用戶。 您可以透過 [Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)取得一個租用戶。 您可以使用下列其中一個入口網站來管理 Azure AD Connect：
  * [Azure 入口網站](https://portal.azure.com)。
  * [Office 入口網站](https://portal.office.com)。  
* [新增並驗證](../active-directory-domains-add-azure-portal.md) 您計畫使用於 Azure AD 中的網域。 例如，如果您計畫讓使用者使用 contoso.com，請確定此網域已經過驗證，而且您不是只使用 contoso.onmicrosoft.com 預設網域。
* Azure AD 租用戶預設允許 5 萬個物件。 在验证域后，该限制增加到 30 万个对象。 如果在 Azure AD 中需要更多的对象，则需要开具支持案例来请求增大此限制。 如果您需要 50 萬個以上的物件，您需要如 Office 365、Azure AD Basic、Azure AD Premium 或 Enterprise Mobility + Security 等授權。

### <a name="prepare-your-on-premises-data"></a>準備您的內部部署資料
* 在同步至 Azure AD 和 Office 365 之前，使用 [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) 來識別目錄中如重複項目和格式問題等的錯誤。
* 檢閱 [您可在 Azure AD 中啟用的選用同步處理功能](how-to-connect-syncservice-features.md) ，並評估您應該啟用哪些功能。

### <a name="on-premises-active-directory"></a>內部部署 Active Directory
* AD 結構描述版本與樹系功能等級必須是 Windows Server 2003 或更新版本。 只要符合結構描述和樹系層級需求，網域控制站就能執行任何版本。
* 如果您打算使用**密碼回寫**功能，網域控制站必須是 Windows Server 2008 R2 或更新版本。
* Azure AD 使用的網域控制站必須為可寫入。 **不**支援使用 RODC (唯讀網域控制站)，且 Azure AD Connect 不會追蹤任何寫入重新導向。
* **不**支援使用內部部署樹系/帶點 (名稱包含句點 ".") 的 NetBios 名稱的網域。
* 建議您[啟用 Active Directory 資源回收筒](how-to-connect-sync-recycle-bin.md)。

### <a name="azure-ad-connect-server"></a>Azure AD Connect 伺服器
* Azure AD Connect 無法安裝至 2019 以前的 Small Business Server 或 Windows Server Essentials (支援 Windows Server Essentials 2019)。 伺服器必須使用 Windows Server Standard 或以上版本。
* 因為安全性作法與更嚴格的設定可以防止 Azure AD Connect 安裝正確，不建議在網域控制站上安裝 Azure AD Connect
* Azure AD Connect 伺服器必須已安裝完整的 GUI。 **不**支援在伺服器核心上安裝。
>[!IMPORTANT]
>不支援在小型企業伺服器、 server essentials 中或 server core 上安裝 Azure AD Connect。

* Azure AD Connect 必須安裝於 Windows Server 2008 R2 或更新版本上。 此服务器必须加入域，并且可以是域控制器或成员服务器。
* 如果您要在 Windows Server 2008 R2 上安裝 Azure AD Connect，請務必套用來自 Windows Update 的最新 Hotfix。 無法在未修補的伺服器上開始進行安裝。
* 如果打算使用 **密码同步**功能，则必须在 Windows Server 2008 R2 SP1 或更高版本上安装 Azure AD Connect 服务器。
* 如果您計畫使用「群組受控服務帳戶」，則 Azure AD Connect 伺服器必須位於 Windows Server 2012 或更新版本上。
* Azure AD Connect 伺服器必須已安裝 [.NET Framework 4.5.1](#component-prerequisites) 或更新的版本及 [Microsoft PowerShell 3.0](#component-prerequisites) 或更新的版本。
* Azure AD Connect 伺服器不得啟用 PowerShell 轉譯群組原則。
* 如果部署的是 Active Directory Federation Services，則安裝 AD FS 或 Web 應用程式 Proxy 的伺服器必須是 Windows Server 2012 R2 或更新版本。 [Windows 遠端管理](#windows-remote-management) ，才能執行遠端安裝。
* 如果部署的是 Active Directory 同盟服務，則您需要 [SSL 憑證](#ssl-certificate-requirements)。
* 如果部署的是 Active Directory 同盟服務，您就需要設定 [名稱解析](#name-resolution-for-federation-servers)。
* 如果全域管理員已啟用 MFA，URL **https://secure.aadcdn.microsoftonline-p.com** 就必須在信任的網站清單中。 在顯示 MFA 挑戰提示時，如果您尚未將此 URL 新增到信任的網站清單，系統會先提示您將它新增到清單。 您可以使用 Internet Explorer 將它新增到信任的網站。
* Microsoft 建議強化您的 Azure AD Connect 伺服器，可減少安全性攻擊面，您的 IT 環境的這個重要元件。  遵循下列建議將會降低您的組織的安全性風險。

* 部署在加入網域的伺服器上的 Azure AD Connect 和網域系統管理員或其他具有受到嚴格控制的安全性群組來限制系統管理存取權。

若要深入了解，請參閱： 

* [保護系統管理員群組](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [保護內建的 administrator 帳戶](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [改進安全性和 sustainment 藉由降低受攻擊面](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [減少 Active Directory 攻擊面](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect 使用的 SQL Server
* Azure AD Connect 要求使用 SQL Server 数据库来存储标识数据。 預設會安裝 SQL Server 2012 Express LocalDB (SQL Server Express 的精簡版)。 SQL Server Express 有 10GB 的大小限制，允许管理大约 100,000 个对象。 如果您需要管理更多數量的目錄物件，則必須將安裝精靈指向不同的 SQL Server 安裝。 安裝 SQL Server 型別可能會影響[效能的 Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)。
* 如果您使用不同的 SQL Server 安裝時，這些需求適用於：
  * Azure AD Connect 支持从 2008 R2（包含最新的 Service Pack）到 SQL Server 2019 的所有 Microsoft SQL Server 版本。 **不支援** 使用 Microsoft Azure SQL Database 作為資料庫。
  * 您必須使用不區分大小寫的 SQL 定序。 這些定序是在其名稱中使用 \_CI_ 來識別。 **不支援**使用區分大小寫的定序 (在其名稱中以 \_CS_ 來識別)。
  * 您在每個 SQL 執行個體中只能有一個同步引擎。 **不支援** 使用 FIM/MIM Sync、DirSync 或 Azure AD Sync 來共用 SQL 執行個體。

### <a name="accounts"></a>帳戶
* 想要與其整合之 Azure AD 租用戶的 Azure AD 全域管理員帳戶。 此帳戶必須是**學校或組織帳戶**，不能是 **Microsoft 帳戶**。
* 如果您使用快速設定或從 DirSync 升級，則必須具有內部部署 Active Directory 的企業系統管理員帳戶。
* [Active Directory 中的帳戶](reference-connect-accounts-permissions.md) (如果您使用自訂設定安裝路徑，或適用於內部部署 Active Directory 的企業系統管理員帳戶)。

### <a name="connectivity"></a>連線能力
* Azure AD Connect 伺服器需要內部網路和網際網路的 DNS 解析。 DNS 伺服器必須能夠將名稱解析成您的內部部署 Active Directory 和 Azure AD 端點。
* 如果您的內部網路有防火牆，而您需要開放 Azure AD Connect 伺服器與網域控制站之間的連接埠，請參閱 [Azure AD Connect 連接埠](reference-connect-ports.md)以了解詳細資訊。
* 如果您的 Proxy 或防火牆會限制可以存取的 URL，則必須開啟 [Office 365 URL 和 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中記載的 URL。
  * 如果您是使用 Microsoft Cloud Germany，或是使用 Microsoft Azure Government 雲端，則請參閱 [Azure AD Connect：執行個體的特殊考量](reference-connect-instances.md) 中的 URL。
* Azure AD Connect (1.1.614.0 和更高版本) 預設會使用 TLS 1.2 來加密同步引擎與 Azure AD 之間的通訊。 若 TLS 1.2 無法在基礎作業系統上使用，Azure AD Connect 會逐步回到較舊的通訊協定 (TLS 1.1 和 TLS 1.0)。
* 在 1.1.614.0 版之前的版本中，Azure AD Connect 預設會使用 TLS 1.0 來加密同步引擎與 Azure AD 之間的通訊。 若要變更為 TLS 1.2，請依照[啟用 Azure AD Connect 的 TLS 1.2](#enable-tls-12-for-azure-ad-connect) 中的步驟。
* 如果您使用連出 Proxy 來連線到網際網路，就必須在 **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** 檔案中新增下列設定，安裝精靈和 Azure AD Connect 同步處理才能夠連線到網際網路和 Azure AD。 必須在檔案底部輸入此文字。 在此程式碼中，&lt;PROXYADDRESS&gt; 代表實際的 Proxy IP 位址或主機名稱。

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* 如果您的 Proxy 伺服器需要驗證，則[服務帳戶](reference-connect-accounts-permissions.md#adsync-service-account)必須位於網域中，且您必須使用自訂的設定安裝路徑來指定[自訂服務帳戶](how-to-connect-install-custom.md#install-required-components)。 您也需要對 machine.config 進行不同的變更。在 machine.config 中進行這項變更之後，安裝精靈和同步處理引擎就會回應來自 Proxy 伺服器的驗證要求。 在所有安装向导页中（“配置”页除外）都使用已登录用户的凭据。 在安裝精靈結尾的 [設定] 頁面上，內容會切換到您建立的[服務帳戶](reference-connect-accounts-permissions.md#adsync-service-account)。 machine.config 节应如下所示。

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* 當 Azure AD Connect 在同步處理目錄的過程中對 Azure AD 傳送了 Web 要求，Azure AD 最多可能需要 5 分鐘的時間才會回應。 Proxy 伺服器常會有連線閒置逾時設定。 請確定此設定至少設為 6 分鐘以上。

如需詳細資訊，請參閱關於[預設 Proxy 元素 (英文)](https://msdn.microsoft.com/library/kd3cf2ex.aspx) 的 MSDN。  
如需連線問題的詳細資訊，請參閱[針對連線問題進行疑難排解](tshoot-connect-connectivity.md)。

### <a name="other"></a>其他
* 選用：測試使用者帳戶來驗證同步處理。

## <a name="component-prerequisites"></a>元件的必要條件
### <a name="powershell-and-net-framework"></a>PowerShell 和 .NET Framework
Azure AD Connect 需要 Microsoft PowerShell 和 .NET Framework 4.5.1。 您需要在伺服器上安裝此版本或更新版本。 依您的 Windows Server 版本來執行下列作業：

* Windows Server 2012R2
  * 預設會安裝 Microsoft PowerShell。 不需採取任何動作。
  * .NET Framework 4.5.1 和更新版本會透過 Windows Update 提供。 請確定您已在控制台安裝 Windows Server 的最新更新。
* Windows Server 2008 R2 和 Windows Server 2012
  * **Windows Management Framework 4.0**中包含最新的 Microsoft PowerShell 版本，可從 [Microsoft 下載中心](https://www.microsoft.com/downloads)取得。
  * .NET Framework 4.5.1 和更新版本可從 [Microsoft 下載中心](https://www.microsoft.com/downloads)取得。


### <a name="enable-tls-12-for-azure-ad-connect"></a>啟用 Azure AD Connect 的 TLS 1.2
在 1.1.614.0 版之前的版本中，Azure AD Connect 預設會使用 TLS 1.0 來加密同步引擎伺服器與 Azure AD 之間的通訊。 可以通过配置 .NET 应用程序在服务器上默认使用 TLS 1.2 来更改此项。 您可以在 [Microsoft 資訊安全摘要報告 2960358](https://technet.microsoft.com/security/advisory/2960358) 中找到 TLS 1.2 的相關詳細資訊。

1. Windows Server 2008 R2 或更新版本之前無法啟用 TLS 1.2。 请确保已为操作系统安装了 .NET 4.5.1 修补程序，请参阅 [Microsoft 安全通报 2960358](https://technet.microsoft.com/security/advisory/2960358)。 您的伺服器上可能已經安裝此 Hotfix 或更新版本。
2. 如果您使用 Windows Server 2008 R2，請確定已啟用 TLS 1.2。 在 Windows Server 2012 伺服器和更新版本上，TLS 1.2 應該已經啟用。
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. 对于所有操作系统，设置此注册表项并重新启动服务器。
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. 如果您也想要在同步引擎伺服器和遠端 SQL Server 之間啟用 TLS 1.2，請確定您已經安裝必要版本以獲得 [TLS 1.2 support for Microsoft SQL Server (Microsoft SQL Server 的 TLS 1.2 支援)](https://support.microsoft.com/kb/3135244)。

## <a name="prerequisites-for-federation-installation-and-configuration"></a>同盟安裝和組態的必要條件
### <a name="windows-remote-management"></a>Windows 遠端管理
當您使用 Azure AD Connect 部署 Active Directory 同盟服務或 Web 應用程式 Proxy 時，請檢查這些需求：

* 如果目標伺服器已加入網域，請確定已啟用 Windows 遠端管理
  * 在权限提升的 PSH 命令窗口中，使用命令 `Enable-PSRemoting –force`
* 如果目標伺服器是未加入網域的 WAP 電腦，則需要遵循一些額外需求
  * 在目標電腦 (WAP 電腦) 上：
    * 确保 winrm（Windows 远程管理/WS-Management）服务正在通过“服务”管理单元运行
    * 在提高權限的 PSH 命令視窗中，使用 `Enable-PSRemoting –force`
  * 在运行向导的计算机上（如果目标计算机未加入域或者是不受信任的域）：
    * 在提高權限的 PSH 命令視窗中，使用 `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * 在服务器管理器中：
      * 將 DMZ WAP 主機加入至電腦集區 ([伺服器管理員] -> [管理] -> [加入伺服器]...使用 DNS 索引標籤)
      * 伺服器管理員的 [所有伺服器] 索引標籤：以滑鼠右鍵按一下 WAP 伺服器並選擇 [管理]，然後輸入 WAP 電腦的本機 (非網域) 認證
      * 若要驗證遠端 PSH 的連線能力，請在伺服器管理員的 [所有伺服器] 索引標籤中：以滑鼠右鍵按一下 WAP 伺服器，然後選擇 [Windows PowerShell]。 遠端 PSH 工作階段應隨即開啟，以確保可建立遠端 PowerShell 工作階段。

### <a name="ssl-certificate-requirements"></a>SSL 憑證需求
* 強烈建議您在 AD FS 伺服器陣列的所有節點以及所有 Web 應用程式 Proxy 伺服器上使用相同的 SSL 憑證。
* 此憑證必須是 X509 憑證。
* 您可以在測試實驗室環境中的同盟伺服器上使用自我簽署的憑證。 不過，在生產環境中，我們建議您從公用 CA 取得憑證。
  * 如果使用非公開信任的憑證，請確定每個 Web 應用程式 Proxy 伺服器上安裝的憑證已取得本機伺服器和所有同盟伺服器的信任
* 憑證的身分識別必須與同盟服務名稱相符 (例如 sts.contoso.com)。
  * 身分識別可以是 dNSName 類型的主體別名 (SAN) 副檔名；或如果沒有 SAN 項目，則會將主體名稱指定為通用名稱。  
  * 憑證中可顯示多個 SAN 項目，前提是其中一個項目與 Federation Service 名稱相符。
  * 如果您打算使用「新增工作場所」，則需要一個值為 **enterpriseregistration** 的額外 SAN。 後面接著組織的「使用者主體名稱」(UPN) 尾碼 (例如 **enterpriseregistration.contoso.com**)。
* 不支持基于 CryptoAPI 下一代 (CNG) 密钥和密钥存储提供者的证书。 這表示您必須使用以 CSP (密碼編譯服務提供者) 為基礎的憑證，而不是 KSP (金鑰儲存體提供者)。
* 支援萬用字元憑證。

### <a name="name-resolution-for-federation-servers"></a>同盟伺服器的名稱解析
* 針對內部網路 (內部 DNS 伺服器) 和外部網路 (透過網域註冊機構註冊的公用 DNS)，設定 AD FS 同盟服務名稱 (例如 sts.contoso.com) 的 DNS 記錄。 請確認內部網路 DNS 記錄是使用 A 記錄而非 CNAME 記錄。 只有这样，才能从加入域的计算机正常执行 Windows 身份验证。
* 如果要部署多部 AD FS 伺服器或 Web 應用程式 Proxy 伺服器，則確定您已設定負載平衡器，並且 AD FS 同盟服務名稱 (例如 sts.contoso.com) 的 DNS 記錄指向負載平衡器。
* 若要讓 Windows 整合式驗證能夠對您內部網路中使用 Internet Explorer 的瀏覽器應用程式發揮作用，請確定 AD FS 同盟服務名稱 (例如 sts.contoso.com) 已新增到 IE 中的內部網路區域。 這項作業可以透過群組原則加以控制，並部署到您所有加入網域的電腦中。

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect 支援元件
下列是 Azure AD Connect 會在要安裝 Azure AD Connect 的伺服器上安裝的元件清單。 此列表针对基本快速安装。 如果您在 [安裝同步處理服務] 頁面上選擇使用不同的 SQL Server，則不會在本機安裝 SQL Express LocalDB。

* Azure AD Connect Health
* Microsoft SQL Server 2012 命令行实用工具
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect 的硬體需求
下表顯示 Azure AD Connect 同步處理電腦的基本需求。

| Active Directory 中的物件數目 | CPU | 記憶體 | 硬碟大小 |
| --- | --- | --- | --- |
| 少於 10,000 個 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 個 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 個 |1.6 GHz |16 GB |100 GB |
| 若有 100,000 個以上的物件，則需要完整版本的 SQL Server | | | |
| 100,000–300,000 個 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 個 |1.6 GHz |32 GB |450 GB |
| 超過 600,000 個 |1.6 GHz |32 GB |500 GB |

執行 AD FS 或 Web 應用程式伺服器的電腦的最低需求如下：

* CPU：雙核心 1.6 GHz 以上
* 記憶體：2 GB 以上
* Azure VM：A2 組態以上

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
