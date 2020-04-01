---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Tableau Server | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d19a7cc8d81f9e6e913f147b24c5cce03ff82027
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76986741"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Tableau Server

I den här självstudien får du lära dig hur du integrerar Tableau Server med Azure Active Directory (Azure AD). När du integrerar Tableau Server med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Tableau Server.
* Gör att användarna automatiskt loggas in på Tableau Server med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Tableau Server enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Tableau Server stöder **SP** initierad SSO
* När du har konfigurerat Tableau Server kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Lägga till tablåserver från galleriet

Om du vill konfigurera integreringen av Tableau Server i Azure AD måste du lägga till Tableau Server från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Ta** **till från galleriet** i avsnittet Lägg till i sökrutan.
1. Välj **Tableau Server** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Konfigurera och testa en azure AD-inloggning för Tableau Server

Konfigurera och testa Azure AD SSO med Tableau Server med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Tableau Server.

Så här konfigurerar och testar du Azure AD SSO med Tableau Server:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Tableau Server SSO](#configure-tableau-server-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Tableau Server-testanvändare](#create-tableau-server-test-user)** – om du vill ha en motsvarighet till B.Simon i Tableau Server som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Tableau Server** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://azure.<domain name>.link`

    b. Skriv en URL med följande mönster i rutan **Identifierare:**`https://azure.<domain name>.link`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > De föregående värdena är inte verkliga värden. Uppdatera värdena med den faktiska URL:en och identifieraren från konfigurationssidan för Tableau Server, som förklaras senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera tabellserver** baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Tableau Server.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Tablåserver**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-tableau-server-sso"></a>Konfigurera SSO för tablåserver

1. Om du vill konfigurera SSO för ditt program måste du logga in på tableauserverklienten som administratör.

2. Välj **Användaridentitet & Access på**fliken **KONFIGURATION** och välj sedan fliken **Autentiseringsmetod.**

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Gör följande på sidan **KONFIGURATION:**

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. För **autentiseringsmetod**väljer du SAML.

    b. Markera kryssrutan **Aktivera SAML-autentisering för servern**.

    c. Tableau Server return URL – URL:en som Tableau <http://tableau_server>Server-användare kommer åt, till exempel . Användning `http://localhost` rekommenderas inte. Det går inte att använda en `http://tableau_server/`URL med ett avslutande snedstreck (till exempel) . Returnera **URL:en för att returnera tabellauserver** och klistra in den i textrutan **Logga in på URL** i avsnittet Grundläggande **SAML-konfiguration** i Azure-portalen

    d. SAML-enhets-ID – Entitets-ID:et identifierar din Tableau Server-installation unikt för IdP. Du kan ange din Tableau Server URL igen här, om du vill, men det behöver inte vara din Tableau Server URL. Kopiera **SAML-entitets-ID** och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen

    e. Klicka på **filen Hämta XML-metadata** och öppna den i textredigeringsprogrammet. Leta reda på url för kontrolltjänster för konsumenttjänst med Http Post och Index 0 och kopiera webbadressen. Klistra nu in den i **textrutan Svara på URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen

    f. Leta reda på filen Federation Metadata som hämtats från Azure-portalen och ladda sedan upp den i **METADATAfilen för SAML Idp**.

    g. Ange namnen på de attribut som IdP använder för att innehålla användarnamn, visningsnamn och e-postadresser.

    h. Klicka på **Spara**

    > [!NOTE]
    > Kunden måste ladda upp alla certifikat i Tableau Server SAML SSO-konfigurationen och det kommer att ignoreras i SSO-flödet. Om du behöver hjälp med att konfigurera SAML på Tableau Server kan du läsa den här artikeln [Konfigurera SAML](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm).

### <a name="create-tableau-server-test-user"></a>Skapa testanvändare för Tablåserver

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i Tableau Server. Du måste etablera alla användare på Tableau-servern.

Användarens användarnamn ska matcha det värde som du har konfigurerat i det anpassade Azure AD-attributet **för användarnamn**. Med rätt mappning bör integreringen fungera Konfigurera Azure AD Single Sign-On.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta Tableau Server-administratören i organisationen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Tablåserver på åtkomstpanelen bör du automatiskt loggas in på den Tabellserver som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Tableau Server med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)