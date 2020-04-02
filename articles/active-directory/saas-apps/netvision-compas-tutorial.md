---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Netvision Compas | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netvision Compas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a56f44c5-dc08-4c7c-ad20-b6e7127deb2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3015ea26d81505c4f058846dbcb3b7858f79267
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520128"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Netvision Compas

I den här självstudien får du lära dig hur du integrerar Netvision Compas med Azure Active Directory (Azure AD). När du integrerar Netvision Compas med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Netvision Compas.
* Gör att användarna automatiskt loggas in på Netvision Compas med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Netvision Compas enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Netvision Compas stöder **SP och IDP** initierade SSO
* När du har konfigurerat Netvision Compas kan du framtvinga Sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Lägga till Netvision Compas från galleriet

Om du vill konfigurera integreringen av Netvision Compas i Azure AD måste du lägga till Netvision Compas från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Netvision Compas** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Netvision Compas** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Konfigurera och testa en azure AD-inloggning för Netvision Compas

Konfigurera och testa Azure AD SSO med Netvision Compas med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Netvision Compas.

Så här konfigurerar och testar du Azure AD SSO med Netvision Compas:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Netvision Compas SSO](#configure-netvision-compas-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Konfigurera Netvision Compas-testanvändare](#configure-netvision-compas-test-user)** – om du vill ha en motsvarighet till B.Simon i Netvision Compas som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Netvision Compas-program** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<TENANT>.compas.cloud/Identity/Saml20`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Netvision Compas Client supportteam](mailto:contact@net.vision) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta metadatafilen och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)



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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Netvision Compas.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Netvision Compas**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-netvision-compas-sso"></a>Konfigurera Netvision Compas SSO

I det här avsnittet aktiverar du SAML SSO i **Netvision Compas**.
1. Logga in på **Netvision Compas** med ett administrativt konto och få tillgång till administrationsområdet.

    ![Admin-området](media/netvision-compas-tutorial/admin.png)

1. Leta reda på **området System** och välj **Identitetsleverantörer**.

    ![Administratörs-ID:er](media/netvision-compas-tutorial/admin-idps.png)

1. Välj åtgärden **Lägg till** för att registrera Azure AD som en ny IDP.

    ![Lägg till IDP](media/netvision-compas-tutorial/idps-add.png)

1. Välj **SAML** för **providertypen**.
1. Ange meningsfulla värden för fälten **Visningsnamn** och **Beskrivning.**
1. Tilldela **Netvision Compas-användare** till IDP genom att välja i listan **Tillgängliga användare** och sedan välja knappen Lägg till **markerad.** Användare kan också tilldelas IDP när de följer etableringsproceduren.
1. För alternativet **Metadata** SAML klickar du på knappen **Välj fil** och väljer den metadatafil som tidigare sparats på datorn.
1. Klicka på **Spara**.

    ![Redigera IDP](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Konfigurera Netvision Compas-testanvändare

I det här avsnittet konfigurerar du en befintlig användare i **Netvision Compas** för att använda Azure AD för SSO.
1. Följ **netvision Compas-proceduren** för användaretablering, enligt företagets definition eller redigera ett befintligt användarkonto.
1. När du definierar användarens profil kontrollerar du att användarens **e-postadress (Personlig)** matchar Azure AD-användarnamnet: username@companydomain.extension. Till exempel `B.Simon@contoso.com`.

    ![Redigera användare](media/netvision-compas-tutorial/user-config.png)

Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du din Azure AD-konfiguration för enkel inloggning.

### <a name="using-the-access-panel-idp-initiated"></a>Använda åtkomstpanelen (IDP initierad).

När du klickar på panelen Netvision Compas på åtkomstpanelen ska du automatiskt loggas in på den Netvision Compas som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Direkt åtkomst till Netvision Compas (SP initierad).

1. Gå till **Netvision Compas-URL:en.** Till exempel `https://tenant.compas.cloud`.
1. Ange **Netvision Compas** användarnamn och välj **Nästa**.

    ![Inloggad användare](media/netvision-compas-tutorial/login-user.png)

1. **(valfritt)** Om användaren tilldelas flera IDPs inom **Netvision Compas**visas en lista över tillgängliga IDPs. Välj Azure AD IDP som konfigurerats tidigare i **Netvision Compas**.

    ![Logga in välj](media/netvision-compas-tutorial/login-choose.png)

1. Du omdirigeras till Azure AD för att utföra autentiseringen. När du har autentiserats ska du automatiskt loggas in på **Netvision Compas** som du ställer in SSO för.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Netvision Compas med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
