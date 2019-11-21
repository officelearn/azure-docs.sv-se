---
title: 'Tutorial: Azure Active Directory integration with UserVoice | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 7a3302f1ca615fe5005be9ed1f09995ebf432eb7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232004"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Azure Active Directory integration with UserVoice

In this tutorial, you learn how to integrate UserVoice with Azure Active Directory (Azure AD).
Integrating UserVoice with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to UserVoice.
* You can enable your users to be automatically signed-in to UserVoice (Single Sign-On) with their Azure AD accounts.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

To configure Azure AD integration with UserVoice, you need the following items:

* En Azure AD-prenumeration. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* UserVoice single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* UserVoice supports **SP** initiated SSO

## <a name="adding-uservoice-from-the-gallery"></a>Adding UserVoice from the gallery

To configure the integration of UserVoice into Azure AD, you need to add UserVoice from the gallery to your list of managed SaaS apps.

**To add UserVoice from the gallery, perform the following steps:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. In the search box, type **UserVoice**, select **UserVoice** from result panel then click **Add** button to add the application.

     ![UserVoice in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

In this section, you configure and test Azure AD single sign-on with UserVoice based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in UserVoice needs to be established.

To configure and test Azure AD single sign-on with UserVoice, you need to complete the following building blocks:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Configure UserVoice Single Sign-On](#configure-uservoice-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Create UserVoice test user](#create-uservoice-test-user)** - to have a counterpart of Britta Simon in UserVoice that is linked to the Azure AD representation of user.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

To configure Azure AD single sign-on with UserVoice, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **UserVoice** application integration page, select **Single sign-on**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![UserVoice Domain and URLs single sign-on information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenantname>.UserVoice.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Contact [UserVoice Client support team](https://www.uservoice.com/) to get these values. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. I avsnittet **SAML-signeringscertifikat** kopierar du **Tumavtryck** och sparar det på datorn.

    ![Kopiera värdet för Tumavtryck](common/copy-thumbprint.png)

7. On the **Set up UserVoice** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-uservoice-single-sign-on"></a>Configure UserVoice Single Sign-On

1. In a different web browser window, sign in to your UserVoice company site as an administrator.

2. In the toolbar on the top, click **Settings**, and then select **Web portal** from the menu.
   
    ![Settings Section On App Side](./media/uservoice-tutorial/ic777519.png "Inställningar")

3. On the **Web portal** tab, in the **User authentication** section, click **Edit** to open the **Edit User Authentication** dialog page.
   
    ![Web portal Tab](./media/uservoice-tutorial/ic777520.png "Web portal")

4. On the **Edit User Authentication** dialog page, perform the following steps:
   
    ![Edit user authentication](./media/uservoice-tutorial/ic777521.png "Edit user authentication")
   
    a. Click **Single Sign-On (SSO)** .
 
    b. Paste the **Login URL** value, which you have copied from the Azure portal into the **SSO Remote Sign-In** textbox.

    c. Paste the **Logout URL** value, which you have copied from the Azure portal into the **SSO Remote Sign-Out textbox**.
 
    d. Paste the **Thumbprint** value , which you have copied from Azure portal  into the **Current certificate SHA1 fingerprint** textbox.
    
    e. Click **Save authentication settings**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. In the **User name** field type brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

In this section, you enable Britta Simon to use Azure single sign-on by granting access to UserVoice.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **UserVoice**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. In the applications list, select **UserVoice**.

    ![The UserVoice link in the Applications list](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-uservoice-test-user"></a>Create UserVoice test user

To enable Azure AD users to sign in to UserVoice, they must be provisioned into UserVoice. In the case of UserVoice, provisioning is a manual task.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Sign in to your **UserVoice** tenant.

2. Gå till **Inställningar**.
   
    ![Inställningar](./media/uservoice-tutorial/ic777811.png "Inställningar")

3. Click **General**.

4. Click **Agents and permissions**.
   
    ![Agents and permissions](./media/uservoice-tutorial/ic777812.png "Agents and permissions")

5. Click **Add admins**.
   
    ![Add admins](./media/uservoice-tutorial/ic777813.png "Add admins")

6. On the **Invite admins** dialog, perform the following steps:
   
    ![Invite admins](./media/uservoice-tutorial/ic777814.png "Invite admins")
   
    a. In the Emails textbox, type the email address of the account you want to provision, and then click **Add**.
   
    b. Klicka på **Bjud in**.

> [!NOTE]
> You can use any other UserVoice user account creation tools or APIs provided by UserVoice to provision Azure AD user accounts.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

When you click the UserVoice tile in the Access Panel, you should be automatically signed in to the UserVoice for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

