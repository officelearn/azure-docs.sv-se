---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med WEDO | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och WEDO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 73adc94e-7656-4a92-99e3-a401c67be477
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ce7ffb389a585511883c3b35de3773ae37342b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76992375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med WEDO

I den här självstudien får du lära dig hur du integrerar WEDO med Azure Active Directory (Azure AD). När du integrerar WEDO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till WEDO.
* Gör att användarna automatiskt loggas in på WEDO med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* WEDO enkel inloggning (SSO) aktiverad prenumeration. Kontakta [WEDO Client supportteam](mailto:info@wedo.swiss) för att få en SSO-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* WEDO stöder **SP och IDP** initierade SSO

* [När du har konfigurerat WEDO kan du framtvinga sessionskontroller som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wedo-from-the-gallery"></a>Lägga till WEDO från galleriet

Om du vill konfigurera integreringen av WEDO i Azure AD måste du lägga till WEDO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **WEDO** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **WEDO** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-wedo"></a>Konfigurera och testa en azure AD-inloggning för WEDO

Konfigurera och testa Azure AD SSO med WEDO med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i WEDO.

Så här konfigurerar och testar du Azure AD SSO med WEDO:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera WEDO SSO](#configure-wedo-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa WEDO-testanvändare](#create-wedo-test-user)** – om du vill ha en motsvarighet till B.Simon i WEDO som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **WEDO** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [WEDO Client support team](mailto:info@wedo.swiss) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. WEDO-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    | Namn | Källattribut|
    | ------------ | --------- |
    | e-post | användare.e-post |
    | firstName | user.firstName |
    | lastName | användare.efternamnNamn |
    | userName | user.userName |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera WEDO.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till WEDO.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **WEDO**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-wedo-sso"></a>Konfigurera WEDO SSO

Följ dessa steg för att aktivera Azure AD SSO i WEDO.

1. Logga in [WEDO](https://login.wedo.swiss/). Du måste ha **administratörsroll**.
1. I profilinställningarna väljer du menyn **Autentisering** i avsnittet **Nätverksinställningar**.
1. Gör följande på sidan **SAML-autentisering:**

   ![Länk för SAML-autentisering](media/wedo-tutorial/network-security-authentification.png)

   a. Aktivera **SAML-autentisering**.

   b. Välj **fliken XML-metadata (Identity Provider metadata).**

   c. Öppna den nedladdade **FEDERATIONSMETADATA-XML:n** från Azure-portalen till Anteckningar och kopiera innehållet i metadata-XML och klistra in den i textrutan **X.509-certifikat.**

   d. Klicka på **Spara**

### <a name="create-wedo-test-user"></a>Skapa WEDO-testanvändare

I det här avsnittet ska du skapa en testanvändare i WEDO som heter Bob Simon. Informationen måste matchas från *Skapa en Azure AD-testanvändare*.

1. Välj **Användare** från avsnittet *Nätverksinställningar* i inställningen Profil i WEDO.
1. Klicka på **Lägg till användare**.
1. I popup-fönstret Lägg till användare fyller du i användarens information

    a. Förnamn `B`.

    b. Efternamn `Simon`.

    c. Ange e-postmeddelandet `username@companydomain.extension`. Till exempel `B.Simon@contoso.com`. Det är obligatoriskt att ha e-post med samma domän som ditt företags kortnamn.

    d. Användartyp `User`.

    e. Klicka på **Skapa användare**.

    f. Klicka på **Spara**på sidan *Välj lag.*

    g.  Klicka på **Ja**på sidan *Bjud in användare* .

1. Validera användaren med hjälp av länken du fick via e-post

> [!NOTE]
> Om du vill skapa en falsk användare (e-post ovan finns inte i ditt nätverk), kontakta [vår support](mailto:info@wedo.swiss) för att validera användaren*.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på WEDO-panelen på åtkomstpanelen ska du automatiskt loggas in på den WEDO som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova WEDO med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hur man skyddar WEDO med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
