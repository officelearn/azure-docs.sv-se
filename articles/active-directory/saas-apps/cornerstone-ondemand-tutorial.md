---
title: 'Tutorial: Azure Active Directory Single sign-on (SSO) integration with Cornerstone OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CornerStone OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 363b5b2dc2891166f779c98c6de1487bc45047a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227638"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>Tutorial: Azure Active Directory Single sign-on (SSO) integration with Cornerstone OnDemand

In this tutorial, you'll learn how to integrate Cornerstone OnDemand with Azure Active Directory (Azure AD). When you integrate Cornerstone OnDemand with Azure AD, you can:

* Control in Azure AD who has access to Cornerstone OnDemand.
* Enable your users to be automatically signed-in to Cornerstone OnDemand with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

To get started, you need the following items:

* En Azure AD-prenumeration. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Cornerstone OnDemand single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeskrivning

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Cornerstone OnDemand har stöd för **SP**-initierad enkel inloggning
* Cornerstone OnDemand har stöd för [automatisk användaretablering](cornerstone-ondemand-provisioning-tutorial.md)

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Lägga till Cornerstone OnDemand från galleriet

För att konfigurera integrering av Cornerstone OnDemand i Azure AD behöver du lägga till Cornerstone OnDemand från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Cornerstone OnDemand** in the search box.
1. Select **Cornerstone OnDemand** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cornerstone-ondemand"></a>Configure and test Azure AD single sign-on for Cornerstone OnDemand

Configure and test Azure AD SSO with Cornerstone OnDemand using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Cornerstone OnDemand.

To configure and test Azure AD SSO with Cornerstone OnDemand, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
2. **[Configure Cornerstone OnDemand SSO](#configure-cornerstone-ondemand-sso)** - to configure the Single Sign-On settings on application side.
    1. **[Create Cornerstone OnDemand test user](#create-cornerstone-ondemand-test-user)** - to have a counterpart of B.Simon in Cornerstone OnDemand that is linked to the Azure AD representation of user.
3. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Cornerstone OnDemand** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company>.csod.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company>.csod.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Cornerstone OnDemand-klientens supportteam](mailto:moreinfo@csod.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. On the **Set up Cornerstone OnDemand** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Cornerstone OnDemand.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Cornerstone OnDemand**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-cornerstone-ondemand-sso"></a>Configure Cornerstone OnDemand SSO

För att kunna konfigurera enkel inloggning på **Cornerstone OnDemand**-sidan måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [Cornerstone OnDemand-supporten](mailto:moreinfo@csod.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cornerstone-ondemand-test-user"></a>Skapa testanvändare för Cornerstone OnDemand

The objective of this section is to create a user called B.Simon in Cornerstone OnDemand. Cornerstone OnDemand stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](https://docs.microsoft.com/azure/active-directory/saas-apps/cornerstone-ondemand-provisioning-tutorial) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

To configure user provisioning, send the information (e.g.: Name, Email) about the Azure AD user you want to provision to the [Cornerstone OnDemand support team](mailto:moreinfo@csod.com).

>[!NOTE]
>You can use any other Cornerstone OnDemand user account creation tools or APIs provided by Cornerstone OnDemand to provision Azure AD user accounts.

## <a name="test-sso"></a>Test SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Cornerstone OnDemand-panelen i åtkomstpanelen bör du automatiskt loggas in på Cornerstone OnDemand som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Cornerstone OnDemand with Azure AD](https://aad.portal.azure.com)
