---
title: 教學課程：Azure Active Directory 與 Evernote 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Evernote 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 2ce05e904484a6d773a0132734208b87e161f960
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59499915"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>教學課程：Azure Active Directory 與 Evernote 整合

在本教學課程中，您將了解如何整合 Evernote 與 Azure Active Directory (Azure AD)。
Evernote 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Evernote 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Evernote (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Evernote 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Evernote 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Evernote 支援由 **SP** 和 **IDP** 起始的 SSO

## <a name="adding-evernote-from-the-gallery"></a>從資源庫新增 Evernote

若要設定 Evernote 與 Azure AD 的整合作業，您必須從資源庫將 Evernote 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 Evernote，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽面板中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Evernote**，從結果面板中選取 **Evernote**，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 Evernote](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Evernote 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Evernote 中相關使用者之間的連結關聯性。

若要設定並測試透過 Evernote 使用 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Evernote 單一登入](#configure-evernote-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Evernote 測試使用者](#create-evernote-test-user)** - 在 Evernote 中建立一個與 Azure AD 中代表使用者之項目連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 Evernote 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Evernote] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上按一下 [編輯] 圖示，以開啟 [基本 SAML 組態] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定] 區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![Evernote 網域和 URL 單一登入資訊](common/idp-identifier.png)

    在 [識別碼] 文字方塊中，鍵入 URL：`https://www.evernote.com/saml2`

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]，然後執行下列步驟：

    ![映像](common/both-preintegrated-signon.png)

    在 [登入 URL] 文字方塊中，輸入 URL：`https://www.evernote.com/Login.action`

6. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中，按一下 [下載]，以依據您的需求從指定選項下載 [憑證 (Base64)]，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

7. 若要修改**簽署**選項，請按一下 [編輯] 按鈕以開啟 [SAML 簽署憑證] 對話方塊。

    ![映像](common/edit-certificate.png) 

    ![映像](./media/evernote-tutorial/samlassertion.png)

    a. 選取 [簽署 SAML 回應及判斷提示] 選項作為 [簽署選項]。

    b. 按一下 [儲存] 

8. 在 [設定 Evernote] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-evernote-single-sign-on"></a>設定 Evernote 單一登入

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Evernote 公司網站。

2. 移至 [管理主控台]

    ![管理主控台](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. 從 [管理主控台]，移至 [安全性]，然後選取 [單一登入]

    ![SSO 設定](./media/evernote-tutorial/tutorial_evernote_sso.png)

4. 設定下列值：

    ![憑證設定](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **啟用 SSO：** 預設會啟用 SSO (按一下 [Disable Single Sign-on] \(停用單一登入\) 即可移除 SSO 需求)

    b. 將您從 Azure 入口網站複製的 [登入 URL] 值，貼到 [SAML HTTP 要求 URL] 文字方塊中。

    c. 在記事本中開啟已從 Azure AD 下載的憑證並複製內容，包括 "BEGIN CERTIFICATE" 和 "END CERTIFICATE"，並將它貼入 [X.509 憑證] 文字方塊。 

    d. 按一下 [儲存變更]

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Evernote 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Evernote]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Evernote]。

    ![應用程式清單中的 Evernote 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-evernote-test-user"></a>建立 Evernote 測試使用者

若要讓 Azure AD 使用者可以登入 Evernote，則必須將他們佈建到 Evernote。  
Evernote 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Evernote 公司網站。

2. 按一下 [管理主控台]。

    ![管理主控台](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. 從 [管理主控台]，移至 [新增使用者]。

    ![新增測試使用者](./media/evernote-tutorial/create_aaduser_0001.png)

4. 在 [新增小組成員]的 [電子郵件] 文字方塊中，輸入使用者帳戶的電子郵件地址，然後按一下 [邀請]。

    ![新增測試使用者](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. 傳送邀請之後，Azure Active Directory 帳戶持有者會收到一封接受邀請的電子郵件。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Evernote] 圖格時，應該會自動登入您設定 SSO 的 Evernote。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

