---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med uniFLOW Online | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och uniFLOW Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f26af813fcd4032aabce2305ac8845307d1fca65
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76262137"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med uniFLOW Online

I den här självstudien får du lära dig hur du integrerar uniFLOW Online med Azure Active Directory (Azure AD). När du integrerar uniFLOW Online med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till uniFLOW Online.
* Gör det möjligt för användarna att logga in på uniFLOW Online med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* uniFLOW Online-klient.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* uniFLOW Online stöder **SP** initierade SSO

## <a name="adding-uniflow-online-from-the-gallery"></a>Lägga till uniFLOW Online från galleriet

Om du vill konfigurera integreringen av uniFLOW Online i Azure AD måste du lägga till uniFLOW Online från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **uniFLOW Online** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **uniFLOW Online** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Konfigurera och testa en azure AD-inloggning för uniFLOW Online

Konfigurera och testa Azure AD SSO med uniFLOW Online med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i uniFLOW Online.

Så här konfigurerar och testar du Azure AD SSO med uniFLOW Online:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
   1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
   1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera uniFLOW Online SSO](#configure-uniflow-online-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Logga in på uniFLOW Online med den skapade testanvändaren](#sign-in-to-uniflow-online-using-the-created-test-user)** - för att testa användarinloggning på programsidan.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **uniFLOW Online** Hantera på sidan Hantera på sidan **Manage** [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [uniFLOW Online Client support team](mailto:support@nt-ware.com) för att få dessa värden. Du kan också referera till de mönster som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen eller referera till svars-URL:en som visas i uniFLOW Online-klienten.

1. uniFLOW Online-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig uniFLOW Online-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | -----------| --------------- |
    | displayname (visningsnamn) | user.displayname |
    | Smeknamn | user.onpremisessamaccountname |

   > [!NOTE]
   > Attributet `user.onpremisessamaccountname` innehåller endast ett värde om dina Azure AD-användare synkroniseras från en lokal Active Directory i Windows.

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera url till App **Federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till uniFLOW Online.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **uniFLOW Online**i programlistan .
1. På appens översiktssida går du till avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

   ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

> [!NOTE]
> Om du vill att alla användare ska kunna komma åt programmet utan manuell tilldelning går du till avsnittet **Hantera** och väljer **Egenskaper**. Ändra sedan parametern **Användartilldelning krävs** till **NO**.

## <a name="configure-uniflow-online-sso"></a>Konfigurera uniFLOW Online SSO

1. Logga in på uniFLOW Online-webbplatsen som administratör i ett annat webbläsarfönster.

1. Välj **Fliken Användare** på den vänstra navigeringspanelen.

    ![uniFLOW-onlinekonfiguration](./media/uniflow-online-tutorial/configure1.png)

1. Klicka på **Identitetsleverantörer**.

    ![uniFLOW-onlinekonfiguration](./media/uniflow-online-tutorial/configure2.png)

1. Klicka på **Lägg till identitetsprovider**.

    ![uniFLOW-onlinekonfiguration](./media/uniflow-online-tutorial/configure3.png)

1. Gör följande i avsnittet **LÄGG TILL IDENTITETSPROVIDER:**


    ![uniFLOW-onlinekonfiguration](./media/uniflow-online-tutorial/configure4.png)

    a. Ange visningsnamnet Ex: *AzureAD SSO*.

    b. För **providertyp**väljer du alternativet **WS-Fed** i listrutan.

    c. För **WS-Fed-typ**väljer du **Alternativet Azure Active Directory** i listrutan.

    d. Klicka på **Spara**.

1. Gör följande på fliken **Allmänt:**

    ![uniFLOW-onlinekonfiguration](./media/uniflow-online-tutorial/configure5.png)

    a. Ange visningsnamnet Ex: *AzureAD SSO*.

    b. Välj alternativet **Från URL** för **ADFS Federation Metadata**.

    c. I textrutan **Federation Metadata URl** klistrar du in **url-värdet för App Federation Metadata,** som du har kopierat från Azure-portalen.

    d. Välj **Identitetsprovider** som **aktiverad**.

    e. Välj **Automatisk användarregistrering** som **aktiverad**.

    f. Klicka på **Spara**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Logga in på uniFLOW Online med den skapade testanvändaren

1. I ett annat webbläsarfönster går du till uniFLOW Online-URL:en för din klientorganisation.

1. Välj den tidigare skapade identitetsprovidern för att logga in via din Azure AD-instans.

1. Logga in med testanvändaren.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova uniFLOW Online med Azure AD](https://aad.portal.azure.com/)
