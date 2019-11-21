---
title: 'Tutorial: Azure Active Directory integration with ZIVVER | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and ZIVVER.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 64cb7ea0-df6c-4963-84d8-6f435980e2de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 02b292c5db3d20f56d7b8291ea31d8da9863809b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233249"
---
# <a name="tutorial-azure-active-directory-integration-with-zivver"></a>Tutorial: Azure Active Directory integration with ZIVVER

In this tutorial, you learn how to integrate ZIVVER with Azure Active Directory (Azure AD).
Integrating ZIVVER with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to ZIVVER.
* You can enable your users to be automatically signed-in to ZIVVER (Single Sign-On) with their Azure AD accounts.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

To configure Azure AD integration with ZIVVER, you need the following items:

* En Azure AD-prenumeration. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* ZIVVER single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ZIVVER supports **IDP** initiated SSO

## <a name="adding-zivver-from-the-gallery"></a>Adding ZIVVER from the gallery

To configure the integration of ZIVVER into Azure AD, you need to add ZIVVER from the gallery to your list of managed SaaS apps.

**To add ZIVVER from the gallery, perform the following steps:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. In the search box, type **ZIVVER**, select **ZIVVER** from result panel then click **Add** button to add the application.

     ![ZIVVER in the results list](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

In this section, you configure and test Azure AD single sign-on with ZIVVER based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in ZIVVER needs to be established.

To configure and test Azure AD single sign-on with ZIVVER, you need to complete the following building blocks:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Configure ZIVVER Single Sign-On](#configure-zivver-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Create ZIVVER test user](#create-zivver-test-user)** - to have a counterpart of Britta Simon in ZIVVER that is linked to the Azure AD representation of user.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

To configure Azure AD single sign-on with ZIVVER, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **ZIVVER** application integration page, select **Single sign-on**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![ZIVVER Domain and URLs single sign-on information](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://app.zivver.com/SAML/Zivver`

5. ZIVVER application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. ZIVVER application expects **nameidentifier** to be mapped with **user.mail**, so you need to edit the attribute mapping by clicking on **Edit** icon and change the attribute mapping.

    ![mallar](common/edit-attribute.png)

6. In addition to above, ZIVVER application expects few more attributes to be passed back in SAML response. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Namnområde | Källattribut|
    | ---------------| --------------- |
    | ZivverAccountKey | https:\//zivver.com/SAML/Attributes | user.objectid |

    >[!NOTE]
    >If you are using a hybrid setup with Active Directory on-premises and Azure AD Connect Tool, VALUE should be set to `user.objectGUID`

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![mallar](common/new-save-attribute.png)

    ![mallar](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Save** (Spara).

7. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Federation Metadata XML** and click **Copy** icon to copy the **App Federation Metadata url** from the given options as per your requirement and save it on your computer.

    ![The Certificate URL download link](./media/zivver-tutorial/metadataxmlurl.png)

8. On the **Set up ZIVVER** section, copy the appropriate URL(s) as per your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zivver-single-sign-on"></a>Configure ZIVVER Single Sign-On

1. In a different web browser window, sign in to your ZIVVER company [site](https://app.zivver.com/login) as an administrator.

2. Click the **Organization settings** icon at the bottom left of your browser window.

3. Go to **Single sign-on**.

4. Open the Federation Metadata XML file that you downloaded from Azure portal.

5. In the **Identity Provider metadata URL** text box, paste the **App Federation Metadata URL** you have saved previously from the Azure portal.

6. Check the checkbox **Turn on SSO**.

7. Klicka på **SPARA**.

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to ZIVVER.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **ZIVVER**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. In the applications list, select **ZIVVER**.

    ![The ZIVVER link in the Applications list](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zivver-test-user"></a>Create ZIVVER test user

In this section, you create a user called Britta Simon in ZIVVER. Work with [ZIVVER support team](https://support.zivver.com/) to add the users in the ZIVVER platform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

When you click the ZIVVER tile in the Access Panel, you should be automatically signed in to the ZIVVER for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

