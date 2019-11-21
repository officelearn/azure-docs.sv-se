---
title: 'Tutorial: Azure Active Directory integration with AirWatch | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 772b37816b83c275bae927d825434dc3ca76a35c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231996"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integrate AirWatch with Azure Active Directory

In this tutorial, you'll learn how to integrate AirWatch with Azure Active Directory (Azure AD). When you integrate AirWatch with Azure AD, you can:

* Control in Azure AD who has access to AirWatch.
* Enable your users to be automatically signed-in to AirWatch with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

To get started, you need the following items:

* En Azure AD-prenumeration. If you don't have a subscription, you can get one-month free trial [here](https://azure.microsoft.com/pricing/free-trial/).
* AirWatch single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeskrivning

In this tutorial, you configure and test Azure AD SSO in a test environment. AirWatch supports **SP** initiated SSO.

## <a name="adding-airwatch-from-the-gallery"></a>Lägga till AirWatch från galleriet

För att konfigurera integrering av AirWatch i Azure AD behöver du lägga till AirWatch från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **AirWatch** in the search box.
1. Select **AirWatch** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Configure and test Azure AD SSO with AirWatch using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in AirWatch.

To configure and test Azure AD SSO with AirWatch, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
2. **[Configure AirWatch SSO](#configure-airwatch-sso)** - to configure the Single Sign-On settings on application side.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Skapa AirWatch-testanvändare](#create-airwatch-test-user)** – för att ha en motsvarighet till Britta Simon i AirWatch som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **AirWatch** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. On the **Basic SAML Configuration** page, enter the values for the following fields:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. I textrutan **Identifierare (entitets-ID)** anger du värdet som: `AirWatch`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [supportteamet för AirWatch-klienten](https://www.air-watch.com/company/contact-us/) för att hämta det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. AirWatch-programmet förväntar sig att SAML-försäkran har ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![mallar](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn |  Källattribut|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Save** (Spara).

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, find **Federation Metadata XML** and select **Download** to download the Metadata XML and save it on your computer.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. On the **Set up AirWatch** section, copy the appropriate URL(s) based on your requirement.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Configure AirWatch SSO

1. In a different web browser window, sign in to your AirWatch company site as an administrator.

1. On the settings page. Select **Settings > Enterprise Integration > Directory Services**.

   ![Inställningar](./media/airwatch-tutorial/ic791921.png "Inställningar")

1. Klicka på fliken **Användare**. I textrutan **Grundläggande unikt namn** skriver du ditt domännamn och klickar sedan på **Spara**.

   ![User](./media/airwatch-tutorial/ic791922.png "Användare")

1. Klicka på fliken **Server**.

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

1. Perform the following steps on the **LDAP** section:

    ![Upload](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. För **Katalogtyp** väljer du **Ingen**.

    b. Välj **Use SAML For Authentication** (Använd SAML för autentisering).

1. On the **SAML 2.0** section, to upload the downloaded certificate, click **Upload**.

    ![Upload](./media/airwatch-tutorial/ic791932.png "Ladda upp")

1. I avsnittet **Begäran** utför du följande steg:

    ![Förfrågan](./media/airwatch-tutorial/ic791925.png "Förfrågan")  

    a. För **Request Binding Type** (Begär bindningstyp) väljer du **POST**.

    b. In the Azure portal, on the **Configure single sign-on at AirWatch** dialog page, copy the **Login URL** value, and then paste it into the **Identity Provider Single Sign On URL** textbox.

    c. För **NameID-format** väljer du **E-postadress**.

    d. As **Authentication Request Security**, select **None**.

    e. Klicka på **Save** (Spara).

1. Klicka på fliken **Användare** igen.

    ![User](./media/airwatch-tutorial/ic791926.png "Användare")

1. I avsnittet **Attribut** utför du följande steg:

    ![Attribute](./media/airwatch-tutorial/ic791927.png "Attribut")

    a. I textrutan **Object Identifier** (Objektidentifierare) skriver du `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. I textrutan **Användarnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. I textrutan **Visningsnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. I textrutan **Förnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. I textrutan **Efternamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. I textrutan **E-post** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Klicka på **Save** (Spara).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Välj **Ny användare** överst på skärmen.
1. In the **User** properties, follow these steps:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to AirWatch.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. I programlistan väljer du **AirWatch**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-airwatch-test-user"></a>Skapa AirWatch-testanvändare

To enable Azure AD users to sign in to AirWatch, they must be provisioned in to AirWatch. För AirWatch är etablering en manuell uppgift.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Sign in to your **AirWatch** company site as administrator.

2. I navigeringsfönstret på den vänstra sidan klickar du på **Konton** och sedan på **Användare**.
  
   ![Användare](./media/airwatch-tutorial/ic791929.png "Användare")

3. In the **Users** menu, click **List View**, and then click **Add > Add User**.
  
   ![Add User](./media/airwatch-tutorial/ic791930.png "Lägg till användare")

4. I dialogrutan **Add / Edit User** (Lägg till/redigera användare) utför du följande steg:

   ![Add User](./media/airwatch-tutorial/ic791931.png "Lägg till användare")

   a. Skriv **Användarnamn**, **Lösenord**, **Bekräfta lösenord**, **Förnamn**, **Efternamn** samt **E-postadress** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

   b. Klicka på **Save** (Spara).

> [!NOTE]
> You can use any other AirWatch user account creation tools or APIs provided by AirWatch to provision Azure AD user accounts.

### <a name="test-sso"></a>Test SSO

When you select the AirWatch tile in the Access Panel, you should be automatically signed in to the AirWatch for which you set up SSO. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
