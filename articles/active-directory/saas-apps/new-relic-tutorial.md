---
title: 'Tutorial: Azure Active Directory integration with New Relic | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: dfa5fbcf5df8ee314aab3e4a8228d81e7e14565b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233522"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Azure Active Directory integration with New Relic

In this tutorial, you learn how to integrate New Relic with Azure Active Directory (Azure AD).
Integrating New Relic with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to New Relic.
* You can enable your users to be automatically signed-in to New Relic (Single Sign-On) with their Azure AD accounts.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

To configure Azure AD integration with New Relic, you need the following items:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* New Relic single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* New Relic supports **SP** initiated SSO

## <a name="adding-new-relic-from-the-gallery"></a>Adding New Relic from the gallery

To configure the integration of New Relic into Azure AD, you need to add New Relic from the gallery to your list of managed SaaS apps.

**To add New Relic from the gallery, perform the following steps:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. In the search box, type **New Relic**, select **New Relic** from result panel then click **Add** button to add the application.

     ![New Relic in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

In this section, you configure and test Azure AD single sign-on with New Relic based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in New Relic needs to be established.

To configure and test Azure AD single sign-on with New Relic, you need to complete the following building blocks:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Configure New Relic Single Sign-On](#configure-new-relic-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Create New Relic test user](#create-new-relic-test-user)** - to have a counterpart of Britta Simon in New Relic that is linked to the Azure AD representation of user.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

To configure Azure AD single sign-on with New Relic, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **New Relic** application integration page, select **Single sign-on**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![New Relic Domain and URLs single sign-on information](common/sp-identifier.png)

    a. In the **Sign on URL** text box, type a URL using the following pattern: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` - Be sure to substitute your own New Relic Account ID.

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL: `rpm.newrelic.com`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. On the **Set up New Relic** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-new-relic-single-sign-on"></a>Configure New Relic Single Sign-On

1. In a different web browser window, sign on to your **New Relic** company site as administrator.

2. In the menu on the top, click **Account Settings**.
   
    ![Account Settings](./media/new-relic-tutorial/ic797036.png "Account Settings")

3. Click the **Security and authentication** tab, and then click the **Single sign on** tab.
   
    ![Single Sign-On](./media/new-relic-tutorial/ic797037.png "för Aha!")

4. On the SAML dialog page, perform the following steps:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Click **Choose File** to upload your downloaded Azure Active Directory certificate.

    b. In the **Remote login URL** textbox,  paste the value of **Login URL**, which you have copied from Azure portal.
   
    c. In the **Logout landing URL** textbox, paste the value of **Logout URL**, which you have copied from Azure portal.

    d. Click **Save my changes**.

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to New Relic.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **New Relic**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. In the applications list, select **New Relic**.

    ![The New Relic link in the Applications list](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-new-relic-test-user"></a>Create New Relic test user

In order to enable Azure Active Directory users to log in to New Relic, they must be provisioned into New Relic. In the case of New Relic, provisioning is a manual task.

**To provision a user account to New Relic, perform the following steps:**

1. Log in to your **New Relic** company site as administrator.

2. In the menu on the top, click **Account Settings**.
   
    ![Account Settings](./media/new-relic-tutorial/ic797040.png "Account Settings")

3. In the **Account** pane on the left side, click **Summary**, and then click **Add user**.
   
    ![Account Settings](./media/new-relic-tutorial/ic797041.png "Account Settings")

4. On the **Active users** dialog, perform the following steps:
   
    ![Active Users](./media/new-relic-tutorial/ic797042.png "Aktiva användare")
   
    a. In the **Email** textbox, type the email address of a valid Azure Active Directory user you want to provision.

    b. As **Role** select **User**.

    c. Click **Add this user**.

>[!NOTE]
>You can use any other New Relic user account creation tools or APIs provided by New Relic to provision Azure AD user accounts.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

When you click the New Relic tile in the Access Panel, you should be automatically signed in to the New Relic for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

