---
title: 'Tutorial: Azure Active Directory integration with Thoughtworks Mingle | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a12d4dca61734275ef0e56dfe2a800c64dc52540
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233295"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: Azure Active Directory integration with Thoughtworks Mingle

In this tutorial, you learn how to integrate Thoughtworks Mingle with Azure Active Directory (Azure AD).
Integrating Thoughtworks Mingle with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Thoughtworks Mingle.
* You can enable your users to be automatically signed-in to Thoughtworks Mingle (Single Sign-On) with their Azure AD accounts.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

To configure Azure AD integration with Thoughtworks Mingle, you need the following items:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Thoughtworks Mingle single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Thoughtworks Mingle supports **SP** initiated SSO

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Adding Thoughtworks Mingle from the gallery

To configure the integration of Thoughtworks Mingle into Azure AD, you need to add Thoughtworks Mingle from the gallery to your list of managed SaaS apps.

**To add Thoughtworks Mingle from the gallery, perform the following steps:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. In the search box, type **Thoughtworks Mingle**, select **Thoughtworks Mingle** from result panel then click **Add** button to add the application.

     ![Thoughtworks Mingle in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

In this section, you configure and test Azure AD single sign-on with Thoughtworks Mingle based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Thoughtworks Mingle needs to be established.

To configure and test Azure AD single sign-on with Thoughtworks Mingle, you need to complete the following building blocks:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Configure Thoughtworks Mingle Single Sign-On](#configure-thoughtworks-mingle-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Create Thoughtworks Mingle test user](#create-thoughtworks-mingle-test-user)** - to have a counterpart of Britta Simon in Thoughtworks Mingle that is linked to the Azure AD representation of user.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

To configure Azure AD single sign-on with Thoughtworks Mingle, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Thoughtworks Mingle** application integration page, select **Single sign-on**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Thoughtworks Mingle Domain and URLs single sign-on information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Contact [Thoughtworks Mingle Client support team](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) to get the value. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. On the **Set up Thoughtworks Mingle** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Configure Thoughtworks Mingle Single Sign-On

1. Sign in to your **Thoughtworks Mingle** company site as administrator.

2. Click the **Admin** tab, and then, click **SSO Config**.
   
    ![Admin tab](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO Config")

3. In the **SSO Config** section, perform the following steps:
   
    ![SSO Config](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO Config")
    
    a. To upload the metadata file, click **Choose file**. 

    b. Klicka på **Spara ändringar**.

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Thoughtworks Mingle.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Thoughtworks Mingle**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. In the applications list, select **Thoughtworks Mingle**.

    ![The Thoughtworks Mingle link in the Applications list](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-thoughtworks-mingle-test-user"></a>Create Thoughtworks Mingle test user

For Azure AD users to be able to sign in, they must be provisioned to the Thoughtworks Mingle application using their Azure Active Directory user names. In the case of Thoughtworks Mingle, provisioning is a manual task.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Sign in to your Thoughtworks Mingle company site as administrator.

2. Click **Profile**.
   
    ![Your First Project](./media/thoughtworks-mingle-tutorial/ic785160.png "Your First Project")

3. Click the **Admin** tab, and then click **Users**.
   
    ![Användare](./media/thoughtworks-mingle-tutorial/ic785161.png "Användare")

4. Klicka på **Ny användare**.
   
    ![New User](./media/thoughtworks-mingle-tutorial/ic785162.png "New User")

5. I dialogrutan **Ny användare** utför du följande steg:
   
    ![New User dialog](./media/thoughtworks-mingle-tutorial/ic785163.png "New User")  
 
    a. Type the **Sign-in name**, **Display name**, **Choose password**, **Confirm password** of a valid Azure AD account you want to provision into the related textboxes. 

    b. As **User type**, select **Full user**.

    c. Click **Create This Profile**.

>[!NOTE]
>You can use any other Thoughtworks Mingle user account creation tools or APIs provided by Thoughtworks Mingle to provision Azure AD user accounts.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

When you click the Thoughtworks Mingle tile in the Access Panel, you should be automatically signed in to the Thoughtworks Mingle for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

