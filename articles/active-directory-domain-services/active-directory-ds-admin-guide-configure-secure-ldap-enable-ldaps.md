---
title: 在 Azure AD Domain Services 中啟用安全 LDAP (LDAPS) | Microsoft Docs
description: 為 Azure AD Domain Services 受控網域啟用安全 LDAP (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: df189e405dcd5277c1ccbd94e9d5d302660be79b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359782"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>為 Azure AD Domain Services 受控網域啟用安全 LDAP (LDAPS)

## <a name="before-you-begin"></a>開始之前
完成[工作 2 - 將安全 LDAP 憑證匯出到 .PFX 檔案](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)。


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>工作 3：使用 Azure 入口網站為受控網域啟用安全 LDAP
若要啟用安全的 LDAP，請執行下列設定步驟：

1. 瀏覽至 **[Azure 入口網站](https://portal.azure.com)**。

2. 在 **搜尋資源** 搜尋方塊中搜尋「網域服務」。 從搜尋結果選取 **Azure AD Domain Services**。 **Azure AD Domain Services** 頁面會列出受控網域。

    ![找出正在佈建的受控網域](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. 按一下受控網域名稱 (例如，'contoso100.com')，以查看網域的詳細資料。

    ![Domain Services - 佈建狀態](./media/getting-started/domain-services-provisioning-state.png)

3. 按一下導覽窗格中的 [安全 LDAP]。

    ![Domain Services - 安全 LDAP 頁面](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. 根據預設，已停用受控網域的安全 LDAP 存取。 將 [安全 LDAP] 切換至 [啟用]。

    ![啟用安全 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. 根據預設，已停用透過網際網路之受控網域的安全 LDAP 存取。 視需要將 [在網際網路上允許安全 LDAP 存取] 切換為 [啟用]。

    > [!WARNING]
    > 當您啟用透過網際網路的安全 LDAP 存取時，您的網域將容易遭受網際網路上的暴力密碼破解攻擊。 因此，建議您設定 NSG，以將存取權鎖定在必要的來源 IP 位址範圍。 請參閱指示，以[鎖定透過網際網路對於受控網域的安全 LDAP 存取](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet)。
    >

6. 按一下 [.PFX 檔案]\(具備安全 LDAP 的憑證\) 後面的資料夾圖示。 指定具備憑證之 PFX 檔案的路徑，對受控網域進行安全 LDAP 存取。

7. 指定 [用以解密 PFX 檔案的密碼]。 提供將憑證匯出到 PFX 檔案時所使用的相同密碼。

8. 完成時，請按一下 [儲存] 按鈕。

9. 您會看到通知，通知您安全 LDAP 已針對受控網域進行設定。 此作業完成之前，您無法修改網域的其他設定。

    ![為受控網域設定安全 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> 為受控網域啟用安全 LDAP 需要約 10 到 15 分鐘的時間。 如果提供的安全 LDAP 憑證不符合所需的準則，則不會為您的目錄啟用安全 LDAP，而且您會看到失敗。 例如，網域名稱不正確、憑證已到期或即將到期等。 在此情況下，使用有效憑證重試。
>
>

## <a name="next-step"></a>後續步驟
[工作 4：設定 DNS 以從網際網路存取受控網域](active-directory-ds-ldaps-configure-dns.md)
