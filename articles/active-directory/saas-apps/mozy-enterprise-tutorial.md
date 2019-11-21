---
title: 'Tutorial: Azure Active Directory integration with Mozy Enterprise | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7d3b7f8982a24856818cbb6ce86060beac969fc2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233497"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: Azure Active Directory integration with Mozy Enterprise

In this tutorial, you learn how to integrate Mozy Enterprise with Azure Active Directory (Azure AD).
Integrating Mozy Enterprise with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Mozy Enterprise.
* You can enable your users to be automatically signed-in to Mozy Enterprise (Single Sign-On) with their Azure AD accounts.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

To configure Azure AD integration with Mozy Enterprise, you need the following items:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Mozy Enterprise single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mozy Enterprise supports **SP** initiated SSO

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Adding Mozy Enterprise from the gallery

To configure the integration of Mozy Enterprise into Azure AD, you need to add Mozy Enterprise from the gallery to your list of managed SaaS apps.

**To add Mozy Enterprise from the gallery, perform the following steps:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. In the search box, type **Mozy Enterprise**, select **Mozy Enterprise** from result panel then click **Add** button to add the application.

     ![Mozy Enterprise in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

In this section, you configure and test Azure AD single sign-on with Mozy Enterprise based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Mozy Enterprise needs to be established.

To configure and test Azure AD single sign-on with Mozy Enterprise, you need to complete the following building blocks:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Configure Mozy Enterprise Single Sign-On](#configure-mozy-enterprise-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Create Mozy Enterprise test user](#create-mozy-enterprise-test-user)** - to have a counterpart of Britta Simon in Mozy Enterprise that is linked to the Azure AD representation of user.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

To configure Azure AD single sign-on with Mozy Enterprise, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Mozy Enterprise** application integration page, select **Single sign-on**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Mozy Enterprise Domain and URLs single sign-on information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Contact [Mozy Enterprise Client support team](https://support.mozy.com/) to get the value. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. On the **Set up Mozy Enterprise** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Configure Mozy Enterprise Single Sign-On

1. In a different web browser window, log into your Mozy Enterprise company site as an administrator.

2. In the **Configuration** section, click **Authentication Policy**.
   
    ![Authentication policy](./media/mozy-enterprise-tutorial/ic777314.png "Authentication policy")

3. On the **Authentication Policy** section, perform the following steps:
   
    ![Authentication policy](./media/mozy-enterprise-tutorial/ic777315.png "Authentication policy")
   
    a. Select **Directory Service** as **Provider**.
   
    b. Select **Use LDAP Push**.
   
    c. Klicka på fliken **SAML-autentisering**.
   
    d. Paste **Login URL**, which you have copied from the Azure portal into the **Authentication URL** textbox.
   
    e. Paste **Azure AD Identifier**, which you have copied from the Azure portal into the **SAML Endpoint** textbox.
   
    f. Open your downloaded base-64 encoded certificate in notepad, copy the content of it into your clipboard, and then paste the entire Certificate into **SAML Certificate** textbox.
   
    g. Select **Enable SSO for Admins to log in with their network credentials**.
   
    h. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. In the **User name** field type **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Mozy Enterprise.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Mozy Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. In the applications list, select **Mozy Enterprise**.

    ![The Mozy Enterprise link in the Applications list](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mozy-enterprise-test-user"></a>Create Mozy Enterprise test user

In order to enable Azure AD users to log into Mozy Enterprise, they must be provisioned into Mozy Enterprise. In the case of Mozy Enterprise, provisioning is a manual task.

>[!NOTE]
>You can use any other Mozy Enterprise user account creation tools or APIs provided by Mozy Enterprise to provision Azure AD user accounts.

**Utför följande steg för att etablera ett användarkonto:**

1. Log in to your **Mozy Enterprise** tenant.

2. Click **Users**, and then click **Add New User**.
   
    ![Användare](./media/mozy-enterprise-tutorial/ic777317.png "Användare")
   
    >[!NOTE]
    >The **Add New User** option is only displayed only if **Mozy** is selected as the provider under **Authentication policy**. If SAML Authentication is configured, then the users are added automatically on their first login through Single sign on.
    
3. On the new user dialog, perform the following steps:
   
    ![Add Users](./media/mozy-enterprise-tutorial/ic777318.png "Add Users")
   
    a. From the **Choose a Group** list, select a group.
   
    b. From the **What type of user** list, select a type.
   
    c. In the **Username** textbox, type the name of the Azure AD user.
   
    d. In the **Email** textbox, type the email address of the Azure AD user.
   
    e. Select **Send user instruction email**.
   
    f. Click **Add User(s)** .

     >[!NOTE]
     > After creating the user, an email will be sent to the Azure AD user that includes a link to confirm the account before it becomes active.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

When you click the Mozy Enterprise tile in the Access Panel, you should be automatically signed in to the Mozy Enterprise for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

