---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Boomi | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2469745edb5b8b3696478603cfe874bcabc8c1ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231958"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: Azure Active Directory single sign-on (SSO) integration with Boomi

In this tutorial, you'll learn how to integrate Boomi with Azure Active Directory (Azure AD). When you integrate Boomi with Azure AD, you can:

* Control in Azure AD who has access to Boomi.
* Enable your users to be automatically signed-in to Boomi with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

To get started, you need the following items:

* En Azure AD-prenumeration. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Boomi single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeskrivning

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Boomi stöder **IDP**-initierad enkel inloggning

## <a name="adding-boomi-from-the-gallery"></a>Lägga till Boomi från galleriet

För att konfigurera integreringen av Boomi till Azure AD behöver du lägga till Boomi från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Boomi** in the search box.
1. Select **Boomi** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Configure and test Azure AD single sign-on for Boomi

Configure and test Azure AD SSO with Boomi using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Boomi.

To configure and test Azure AD SSO with Boomi, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Boomi SSO](#configure-boomi-sso)** - to configure the single sign-on settings on application side.
    * **[Create Boomi test user](#create-boomi-test-user)** - to have a counterpart of B.Simon in Boomi that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Boomi** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. On the **Set up single sign-on with SAML** page, enter the values for the following fields:

    a. I textrutan **Identifierare** skriver du in en URL: `https://platform.boomi.com/`

    b. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > Värdet för svars-URL är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Hämta det här värdet genom att kontakta [supportteamet för Boomi-klienten](https://boomi.com/company/contact/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Boomi application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. I följande skärmbild visas listan över standardattribut.

    ![mallar](common/default-attributes.png)

1. In addition to above, Boomi application expects few more attributes to be passed back in SAML response which are shown below. These attributes are also pre populated but you can review them as per your requirements.

    | Namn |  Källattribut|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. On the **Set up Boomi** section, copy the appropriate URL(s) based on your requirement.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Boomi.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. I programlistan väljer du **Boomi**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-boomi-sso"></a>Configure Boomi SSO

1. In a different web browser window, sign in to your Boomi company site as an administrator.

1. Gå till **Företagsnamn** och sedan till **Konfigurera**.

1. Klicka på fliken **SSO Options** (SSO-alternativ) och utför stegen nedan.

    ![Konfigurera enkel inloggning på appsidan](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Markera kryssrutan **Enable SAML Single Sign-On** (Aktivera enkel inloggning med SAML).

    b. Klicka på **Importera** för att ladda upp det nedladdade certifikatet från Azure AD till **Certifikat för identitetsprovider**.

    c. I textrutan **Inloggnings-URL för identitetsprovider** anger du värdet för **inloggnings-URL** från konfigurationsfönstret för Azure AD-programmet.

    d. För **Federation Id Location** (Plats för federations-ID) markerar du alternativknappen **Federation Id is in FEDERATION_ID Attribute element** (Federations-ID finns i attributelementet FEDERATION_ID).

    e. Klicka på knappen **Spara**.

### <a name="create-boomi-test-user"></a>Skapa Boomi-testanvändare

In order to enable Azure AD users to sign in to Boomi, they must be provisioned into Boomi. När det gäller Boomi är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Sign in to your Boomi company site as an administrator.

1. När du loggat in går du till **Användarhantering** och sedan till **Användare**.

    ![Användare](./media/boomi-tutorial/tutorial_boomi_001.png "Användare")

1. Klicka på ikonen **+** så öppnas dialogrutan **Add/Maintain User Roles** (Lägg till/underhåll användarroller).

    ![Användare](./media/boomi-tutorial/tutorial_boomi_002.png "Användare")

    ![Användare](./media/boomi-tutorial/tutorial_boomi_003.png "Användare")

    a. I textrutan **User e-mail address** (E-post för användare) skriver du användarens e-postadress som B.Simon@contoso.com.

    b. In the **First name** textbox, type the First name of user like B.

    c. I textrutan **Efternamn** skriver du efternamnet: Simon.

    d. Ange användarens **Federations-ID**. Varje användare ha ett Federation-ID som unikt identifierar användare i kontot.

    e. Tilldela rollen **Standardanvändare** till användaren. Do not assign the Administrator role because that would give them normal Atmosphere access as well as single sign-on access.

    f. Klicka på **OK**

    > [!NOTE]
    > The user will not receive a welcome notification email containing a password that can be used to log in to the AtomSphere account because their password is managed through the identity provider. You may use any other Boomi user account creation tools or APIs provided by Boomi to provision Azure AD user accounts.

## <a name="test-sso"></a>Test SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Boomi-panelen i åtkomstpanelen bör du automatiskt loggas in på Boomi som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Boomi with Azure AD](https://aad.portal.azure.com/)