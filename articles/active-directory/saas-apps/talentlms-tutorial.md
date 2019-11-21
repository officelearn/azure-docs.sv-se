---
title: 'Tutorial: Azure Active Directory integration with TalentLMS | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970ab9de270b1227884a13ac578d4c439043b20c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233368"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Azure Active Directory integration with TalentLMS

In this tutorial, you learn how to integrate TalentLMS with Azure Active Directory (Azure AD).
Integrating TalentLMS with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to TalentLMS.
* You can enable your users to be automatically signed-in to TalentLMS (Single Sign-On) with their Azure AD accounts.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

To configure Azure AD integration with TalentLMS, you need the following items:

* En Azure AD-prenumeration. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* TalentLMS single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TalentLMS supports **SP** initiated SSO

## <a name="adding-talentlms-from-the-gallery"></a>Adding TalentLMS from the gallery

To configure the integration of TalentLMS into Azure AD, you need to add TalentLMS from the gallery to your list of managed SaaS apps.

**To add TalentLMS from the gallery, perform the following steps:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. In the search box, type **TalentLMS**, select **TalentLMS** from result panel then click **Add** button to add the application.

    ![TalentLMS in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

In this section, you configure and test Azure AD single sign-on with TalentLMS based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in TalentLMS needs to be established.

To configure and test Azure AD single sign-on with TalentLMS, you need to complete the following building blocks:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Configure TalentLMS Single Sign-On](#configure-talentlms-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Create TalentLMS test user](#create-talentlms-test-user)** - to have a counterpart of Britta Simon in TalentLMS that is linked to the Azure AD representation of user.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

To configure Azure AD single sign-on with TalentLMS, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **TalentLMS** application integration page, select **Single sign-on**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![TalentLMS Domain and URLs single sign-on information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.TalentLMSapp.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Contact [TalentLMS Client support team](https://www.talentlms.com/contact) to get these values. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera värdet för Tumavtryck](common/copy-thumbprint.png)

7. On the **Set up TalentLMS** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-talentlms-single-sign-on"></a>Configure TalentLMS Single Sign-On

1. In a different web browser window, sign in to your TalentLMS company site as an administrator.

1. In the **Account & Settings** section, click the **Users** tab.

    ![Account & Settings](./media/talentlms-tutorial/IC777296.png "Account & Settings")

1. Click **Single Sign-On (SSO)** ,

1. Gör följande i avsnittet Enkel inloggning:

    ![Single Sign-On](./media/talentlms-tutorial/IC777297.png "för Aha!")

    a. From the **SSO integration type** list, select **SAML 2.0**.

    b. In the **Identity provider (IDP)** textbox, paste the value of **Azure AD Identifier**, which you have copied from Azure portal.

    c. Paste the **Thumbprint** value from Azure portal into the **Certificate fingerprint** textbox.

    d.  In the **Remote sign-in URL** textbox, paste the value of **Login URL**, which you have copied from Azure portal.

    e. In the **Remote sign-out URL** textbox, paste the value of **Logout URL**, which you have copied from Azure portal.

    f. Fill in the following:

    * In the **TargetedID** textbox, type `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * In the **First name** textbox, type `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * In the **Last name** textbox, type `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * In the **Email** textbox, type `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Klicka på **Save** (Spara).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. In the **User name** field type `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

In this section, you enable Britta Simon to use Azure single sign-on by granting access to TalentLMS.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **TalentLMS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. In the applications list, select **TalentLMS**.

    ![The TalentLMS link in the Applications list](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-talentlms-test-user"></a>Create TalentLMS test user

To enable Azure AD users to sign in to TalentLMS, they must be provisioned into TalentLMS. In the case of TalentLMS, provisioning is a manual task.

**Utför följande steg för att etablera ett användarkonto:**

1. Sign in to your **TalentLMS** tenant.

1. Click **Users**, and then click **Add User**.

1. On the **Add user** dialog page, perform the following steps:

    ![Add User](./media/talentlms-tutorial/IC777299.png "Lägg till användare")  

    a. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.
 
    c. In the **Email address** textbox, enter the email of user like `brittasimon\@contoso.com`.

    d. Klicka på **Lägg till användare**.

> [!NOTE]
> You can use any other TalentLMS user account creation tools or APIs provided by TalentLMS to provision Azure AD user accounts.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

When you click the TalentLMS tile in the Access Panel, you should be automatically signed in to the TalentLMS for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

