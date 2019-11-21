---
title: 'Tutorial: Azure Active Directory integration with Bonusly | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9c88f8eb8ac7b72f11ff286d2294df8cb70860
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232063"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Azure Active Directory integration with Bonusly

In this tutorial, you learn how to integrate Bonusly with Azure Active Directory (Azure AD).
Integrating Bonusly with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Bonusly.
* You can enable your users to be automatically signed-in to Bonusly (Single Sign-On) with their Azure AD accounts.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

To configure Azure AD integration with Bonusly, you need the following items:

* En Azure AD-prenumeration. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* Bonusly single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Bonusly supports **IDP** initiated SSO

## <a name="adding-bonusly-from-the-gallery"></a>Adding Bonusly from the gallery

To configure the integration of Bonusly into Azure AD, you need to add Bonusly from the gallery to your list of managed SaaS apps.

**To add Bonusly from the gallery, perform the following steps:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. In the search box, type **Bonusly**, select **Bonusly** from result panel then click **Add** button to add the application.

    ![Bonusly in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

In this section, you configure and test Azure AD single sign-on with Bonusly based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Bonusly needs to be established.

To configure and test Azure AD single sign-on with Bonusly, you need to complete the following building blocks:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Configure Bonusly Single Sign-On](#configure-bonusly-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Create Bonusly test user](#create-bonusly-test-user)** - to have a counterpart of Britta Simon in Bonusly that is linked to the Azure AD representation of user.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

To configure Azure AD single sign-on with Bonusly, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Bonusly** application integration page, select **Single sign-on**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Bonusly Domain and URLs single sign-on information](common/idp-reply.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Contact [Bonusly Client support team](https://bonus.ly/contact) to get the value. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera värdet för Tumavtryck](common/copy-thumbprint.png)

7. On the **Set up Bonusly** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-bonusly-single-sign-on"></a>Configure Bonusly Single Sign-On

1. In a different browser window, sign in to your **Bonusly** tenant.

1. In the toolbar on the top, click **Settings** and then select **Integrations and apps**.

    ![Bonusly Social Section](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Under **Single Sign-On**, select **SAML**.

1. On the **SAML** dialog page, perform the following steps:

    ![Bonusly Saml Dialog page](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. In the **IdP SSO target URL** textbox, paste the value of **Login URL**, which you have copied from Azure portal.

    b. In the **IdP Login URL** textbox, paste the value of **Login URL**, which you have copied from Azure portal.

    c. In the **IdP Issuer** textbox, paste the value of **Azure AD Identifier**, which you have copied from Azure portal.
    
    d. Paste the **Thumbprint** value copied from Azure portal into the **Cert Fingerprint** textbox.
    
    e. Klicka på **Save** (Spara).

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Bonusly.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Bonusly**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. In the applications list, select **Bonusly**.

    ![The Bonusly link in the Applications list](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bonusly-test-user"></a>Create Bonusly test user

In order to enable Azure AD users to sign in to Bonusly, they must be provisioned into Bonusly. In the case of Bonusly, provisioning is a manual task.

> [!NOTE]
> You can use any other Bonusly user account creation tools or APIs provided by Bonusly to provision Azure AD user accounts. 

**Utför följande steg för att konfigurera användarförsörjning:**

1. In a web browser window, sign in to your Bonusly tenant.

1. Klicka på **Inställningar**.

    ![Inställningar](./media/bonus-tutorial/ic781041.png "Inställningar")

1. Click the **Users and bonuses** tab.

    ![Users and bonuses](./media/bonus-tutorial/ic781042.png "Users and bonuses")

1. Click **Manage Users**.

    ![Manage Users](./media/bonus-tutorial/ic781043.png "Manage Users")

1. Klicka på **Lägg till användare**.

    ![Add User](./media/bonus-tutorial/ic781044.png "Lägg till användare")

1. I dialogrutan **Lägg till användare** utför du följande steg:

    ![Add User](./media/bonus-tutorial/ic781045.png "Lägg till användare")  

    a. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

    c. In the **Email** textbox, enter the email of user like `brittasimon\@contoso.com`.

    d. Klicka på **Save** (Spara).

    > [!NOTE]
    > The Azure AD account holder receives an email that includes a link to confirm the account before it becomes active.  

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

When you click the Bonusly tile in the Access Panel, you should be automatically signed in to the Bonusly for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
