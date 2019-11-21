---
title: 'Tutorial: Azure Active Directory integration with SpringCM | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bd9ea5565ea64415c00a458236456bed04b6c38
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233390"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutorial: Azure Active Directory integration with SpringCM

In this tutorial, you learn how to integrate SpringCM with Azure Active Directory (Azure AD).
Integrating SpringCM with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to SpringCM.
* You can enable your users to be automatically signed-in to SpringCM (Single Sign-On) with their Azure AD accounts.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

To configure Azure AD integration with SpringCM, you need the following items:

* En Azure AD-prenumeration. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* SpringCM single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SpringCM supports **SP** initiated SSO

## <a name="adding-springcm-from-the-gallery"></a>Adding SpringCM from the gallery

To configure the integration of SpringCM into Azure AD, you need to add SpringCM from the gallery to your list of managed SaaS apps.

**To add SpringCM from the gallery, perform the following steps:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. To add a new application, click the **New application** button at the top of the dialog.

    ![Knappen Nytt program](common/add-new-app.png)

4. In the search box, type **SpringCM**, select **SpringCM** from the result panel then click the **Add** button to add the application.

    ![SpringCM in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

In this section, you configure and test Azure AD single sign-on with SpringCM based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in SpringCM needs to be established.

To configure and test Azure AD single sign-on with SpringCM, you need to complete the following building blocks:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Configure SpringCM Single Sign-On](#configure-springcm-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Create SpringCM test user](#create-springcm-test-user)** - to have a counterpart of Britta Simon in SpringCM that is linked to the Azure AD representation of user.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

To configure Azure AD single sign-on with SpringCM, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **SpringCM** application integration page, select **Single sign-on**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SpringCM Domain and URLs single sign-on information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Contact [SpringCM Client support team](https://knowledge.springcm.com/support) to get the value. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. On the **Set up SpringCM** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-springcm-single-sign-on"></a>Configure SpringCM Single Sign-On

1. In a different web browser window, sign on to your **SpringCM** company site as administrator.

1. In the menu on the top, click **GO TO**, click **Preferences**, and then, in the **Account Preferences** section, click **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. In the Identity Provider Configuration section, perform the following steps:
   
    ![Identity Provider Configuration](./media/spring-cm-tutorial/ic797052.png "Identity Provider Configuration")
    
    a. To upload your downloaded Azure Active Directory certificate, click **Select Issuer Certificate** or **Change Issuer Certificate**.
    
    b. In the **Issuer** textbox, paste **Azure AD Identifier** value, which you have copied from Azure portal.
    
    c. In the **Service Provider (SP) Initiated Endpoint** textbox, paste **Login URL** value, which you have copied from the Azure portal.
            
    d. Select **SAML Enabled** as **Enable**.

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to SpringCM.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **SpringCM**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. In the applications list, select **SpringCM**.

    ![The SpringCM link in the Applications list](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-springcm-test-user"></a>Create SpringCM test user

To enable Azure Active Directory users to sign in to SpringCM, they must be provisioned into SpringCM. In the case of SpringCM, provisioning is a manual task.

> [!NOTE]
> For more information, see [Create and Edit a SpringCM User](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**To provision a user account to SpringCM, perform the following steps:**

1. Sign in to your **SpringCM** company site as administrator.

1. Click **GOTO**, and then click **ADDRESS BOOK**.
   
    ![Create User](./media/spring-cm-tutorial/ic797054.png "Create User")

1. Klicka på **Skapa användare**.

1. Select a **User Role**.

1. Select **Send Activation Email**.

1. Type the first name, last name, and email address of a valid Azure Active Directory user account you want to provision into the related textboxes.

1. Add the user to a **Security group**.

1. Klicka på **Save** (Spara).

   > [!NOTE]
   > You can use any other SpringCM user account creation tools or APIs provided by SpringCM to provision Azure AD user accounts.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

When you click the SpringCM tile in the Access Panel, you should be automatically signed in to the SpringCM for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

