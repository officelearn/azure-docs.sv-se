---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Freshservice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227522"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Tutorial: Azure Active Directory single sign-on (SSO) integration with Freshservice

In this tutorial, you'll learn how to integrate Freshservice with Azure Active Directory (Azure AD). When you integrate Freshservice with Azure AD, you can:

* Control in Azure AD who has access to Freshservice.
* Enable your users to be automatically signed-in to Freshservice with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

To get started, you need the following items:

* En Azure AD-prenumeration. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Freshservice single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeskrivning

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Freshservice stöder **IDP**-initierad enkel inloggning

## <a name="adding-freshservice-from-the-gallery"></a>Lägga till Freshservice från galleriet

För att konfigurera integreringen av Freshservice till Azure AD behöver du lägga till Freshservice från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Freshservice** in the search box.
1. Select **Freshservice** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Configure and test Azure AD single sign-on for Freshservice

Configure and test Azure AD SSO with Freshservice using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Freshservice.

To configure and test Azure AD SSO with Freshservice, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Freshservice SSO](#configure-freshservice-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Freshservice test user](#create-freshservice-test-user)** - to have a counterpart of B.Simon in Freshservice that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Freshservice** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<democompany>.freshservice.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Hämta dessa värden genom att kontakta [supportteamet för Freshservice-klienten](https://support.freshservice.com/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Freshservice requires SHA-256 fingerprint to get SSO working. To get SHA-256 fingerprint, perform the following steps :

    ![FingerPrint](./media/freshservice-tutorial/ic790821.png "FingerPrint")

    1. Open the [link](https://www.samltool.com/fingerprint.php) in different web browser.

    1. Open downloaded certificate (Base64) file in the Notepad and paste content in the **X.509 cert** textbox.

    1. For the Algorithm, select **sha256** from the dropdown.

    1. Click **CALCULATE FINGERPRINT**.

    1. Click on the copy icon to copy the generated **FingerPrint** and save it on your computer.

1. On the **Set up Freshservice** section on the **Azure portal**, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Freshservice.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. I programlistan väljer du **Freshservice**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-freshservice-sso"></a>Configure Freshservice SSO

1. To automate the configuration within Freshservice, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

    ![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Setup Freshservice** will direct you to the Freshservice application. From there, provide the admin credentials to sign into Freshservice. The browser extension will automatically configure the application for you and automate steps 3-6.

    ![Setup configuration](common/setup-sso.png)

3. If you want to setup Freshservice manually, open a new web browser window and sign into your Freshservice company site as an administrator and perform the following steps:

4. På menyn längst upp klickar du på **Admin**.

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

5. I **kundportalen** klickar du på **Säkerhet**.

    ![Säkerhet](./media/freshservice-tutorial/ic790815.png "Säkerhet")

6. I avsnittet **Säkerhet** utför du följande steg:

    ![Enkel inloggning](./media/freshservice-tutorial/ic790816.png "Enkel inloggning")

    a. Växla **Enkel inloggning**.

    b. Välj **SAML SSO**.

    c. I textrutan för **inloggnings-URL för SAML** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. I textrutan för **utloggnings-URL:en** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    e. In **Security Certificate Fingerprint** textbox, paste the **FingerPrint** value, which you have generated earlier.

    f. Klicka på **Spara**

### <a name="create-freshservice-test-user"></a>Skapa Freshservice-testanvändare

To enable Azure AD users to sign in to FreshService, they must be provisioned into FreshService. När det gäller FreshService är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Sign in to your **FreshService** company site as an administrator.

2. På menyn längst upp klickar du på **Admin**.

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

3. I avsnittet **Användarhantering** klickar du på **Beställare**.

    ![Requesters](./media/freshservice-tutorial/ic790818.png "Requesters")

4. Klicka på **Ny beställare**.

    ![New Requesters](./media/freshservice-tutorial/ic790819.png "New Requesters")

5. I avsnittet **Ny beställare** utför du följande steg:

    ![New Requester](./media/freshservice-tutorial/ic790820.png "New Requester")  

    a. Ange attributen **Förnamn** och **E-post** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

    b. Klicka på **Save** (Spara).

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt
    >  

> [!NOTE]
> You can use any other FreshService user account creation tools or APIs provided by FreshService to provision Azure AD user accounts.

## <a name="test-sso"></a>Test SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Freshservice-panelen i åtkomstpanelen bör du automatiskt loggas in på Freshservice som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Freshservice with Azure AD](https://aad.portal.azure.com/)