---
title: 'Tutorial: Azure Active Directory integration with Kintone | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 6786b44aca9ceed3cec5daf0f858a51e2dd12833
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227587"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: Azure Active Directory integration with Kintone

In this tutorial, you learn how to integrate Kintone with Azure Active Directory (Azure AD).
Integrating Kintone with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Kintone.
* You can enable your users to be automatically signed-in to Kintone (Single Sign-On) with their Azure AD accounts.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

To configure Azure AD integration with Kintone, you need the following items:

* En Azure AD-prenumeration. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* Kintone single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kintone supports **SP** initiated SSO

## <a name="adding-kintone-from-the-gallery"></a>Adding Kintone from the gallery

To configure the integration of Kintone into Azure AD, you need to add Kintone from the gallery to your list of managed SaaS apps.

**To add Kintone from the gallery, perform the following steps:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. In the search box, type **Kintone**, select **Kintone** from result panel then click **Add** button to add the application.

     ![Kintone in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

In this section, you configure and test Azure AD single sign-on with Kintone based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Kintone needs to be established.

To configure and test Azure AD single sign-on with Kintone, you need to complete the following building blocks:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Configure Kintone Single Sign-On](#configure-kintone-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Create Kintone test user](#create-kintone-test-user)** - to have a counterpart of Britta Simon in Kintone that is linked to the Azure AD representation of user.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

To configure Azure AD single sign-on with Kintone, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Kintone** application integration page, select **Single sign-on**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Kintone Domain and URLs single sign-on information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.kintone.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster:
    
    | |
    |--|
    | `https://<companyname>.cybozu.com` |
    | `https://<companyname>.kintone.com` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Contact [Kintone Client support team](https://www.kintone.com/contact/) to get these values. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. On the **Set up Kintone** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kintone-single-sign-on"></a>Configure Kintone Single Sign-On

1. In a different web browser window, sign into your **Kintone** company site as an administrator.

1. Click **Settings icon**.

    ![Inställningar](./media/kintone-tutorial/ic785879.png "Inställningar")

1. Click **Users & System Administration**.

    ![Users & System Administration](./media/kintone-tutorial/ic785880.png "Users & System Administration")

1. Under **System Administration \> Security** click **Login**.

    ![Inloggning](./media/kintone-tutorial/ic785881.png "Logga in")

1. Click **Enable SAML authentication**.

    ![SAML Authentication](./media/kintone-tutorial/ic785882.png "SAML Authentication")

1. In the SAML Authentication section, perform the following steps:

    ![SAML Authentication](./media/kintone-tutorial/ic785883.png "SAML Authentication")

    a. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan **Logout URL** (Utloggnings-URL) klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    c. Click **Browse** to upload your downloaded certificate file from Azure portal.

    d. Klicka på **Save** (Spara).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. In the **User name** field type `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Kintone.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Kintone**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. In the applications list, select **Kintone**.

    ![The Kintone link in the Applications list](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kintone-test-user"></a>Create Kintone test user

To enable Azure AD users to sign in to Kintone, they must be provisioned into Kintone. In the case of Kintone, provisioning is a manual task.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Sign in to your **Kintone** company site as an administrator.

1. Click **Settings icon**.

    ![Inställningar](./media/kintone-tutorial/ic785879.png "Inställningar")

1. Click **Users & System Administration**.

    ![User & System Administration](./media/kintone-tutorial/ic785880.png "User & System Administration")

1. Under **User Administration**, click **Departments & Users**.

    ![Department & Users](./media/kintone-tutorial/ic785888.png "Department & Users")

1. Klicka på **Ny användare**.

    ![New Users](./media/kintone-tutorial/ic785889.png "New Users")

1. In the **New User** section, perform the following steps:

    ![New Users](./media/kintone-tutorial/ic785890.png "New Users")

    a. Type a **Display Name**, **Login Name**, **New Password**, **Confirm Password**, **E-mail Address**, and other details of a valid Azure AD account you want to provision into the related textboxes.

    b. Klicka på **Save** (Spara).

> [!NOTE]
> You can use any other Kintone user account creation tools or APIs provided by Kintone to provision Azure AD user accounts.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

When you click the Kintone tile in the Access Panel, you should be automatically signed in to the Kintone for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
