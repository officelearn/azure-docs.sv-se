---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Akamai | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74979597"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Akamai

I den här självstudien får du lära dig hur du integrerar Akamai med Azure Active Directory (Azure AD). När du integrerar Akamai med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Akamai.
* Gör att användarna automatiskt loggas in på Akamai med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Akamai enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

- Slack stöder IDP initierad SSO

## <a name="adding-akamai-from-the-gallery"></a>Lägga till Akamai från galleriet

Om du vill konfigurera integreringen av Akamai i Azure AD måste du lägga till Akamai från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Akamai** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Akamai** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Konfigurera och testa en azure AD-inloggning för Akamai

Konfigurera och testa Azure AD SSO med Akamai med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Akamai.

Om du vill konfigurera och testa Azure AD SSO med Akamai slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Akamai SSO](#configure-akamai-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Akamai-testanvändare](#create-akamai-test-user)** – om du vill ha en motsvarighet till B.Simon i Akamai som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Akamai Client supportteam](https://www.akamai.com/us/en/contact-us/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Akamai** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Akamai.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Akamai**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-akamai-sso"></a>Konfigurera Akamai SSO

### <a name="setting-up-idp"></a>Ställa in IDP

1. Logga in på **Akamai Enterprise Application** Access-konsolen.
1. Välj > **identitetsidentitetsleverantörer**på **Identity** **Akamai EAA-konsolen**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure01.png)

1. Klicka på **Lägg till identitetsprovider**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure02.png)

    a. Ange unikt **namn**.
    
    b. Välj **SAML från tredje part** och klicka på Skapa **identitetsprovider och konfigurera**.

### <a name="general-settings"></a>Allmänna inställningar

1. **Identity Intercept** - Ange namnet på (SP-bas-URL – kommer att användas för Azure AD-konfiguration)

    > [!NOTE]
    > Du kan välja att ha en egen anpassad domän (kräver en DNS-post och ett certifikat). I det här exemplet kommer vi att använda Akamai-domänen.

1. **Akamai Cloud Zone** - Välj lämplig molnzon.
1. **Validering av certifikat** – kontrollera Akamai-dokumentation (valfritt)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Konfiguration av autentisering

1. URL – Ange webbadressen som är samma som din identitetsavlyssning (det är här användarna omdirigeras efter autentisering).
2. Utloggning URL: Uppdatera utloggning URL.
3. Signera SAML-begäran: standard avmarkerad.
4. Lägg till programmet i Azure AD-konsolen för IDP-metadatafilen.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Rubrikbaserad autentisering

Akamai header baserad autentisering

1. Välj **Anpassat HTTP-formulär** guiden Lägg till program.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure05.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure06.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure07.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Autentisering

![Konfigurera Akamai](./media/header-akamai-tutorial/configure09.png)

![Konfigurera Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Tjänster

1. Klicka på Spara och gå till autentisering.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

1. Under **kundens HTTP-huvuden**anger du **Attributet CustomerHeader** och **SAML**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure12.png)

1. Klicka på **Spara och gå till distributionsknappen.**

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Distribuera programmet

1. Klicka på Knappen **Distribuera program.**

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure14.png)

1. Kontrollera att programmet har distribuerats.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Kerberos-autentisering

#### <a name="remote-desktop"></a>Fjärrskrivbord

1. Välj **RDP** i guiden LÄGG TILL program.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure16.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure17.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure18.png)

1. Ange den koppling som ska underhålla detta.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Autentisering

Klicka på **Spara och gå till Tjänster**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Tjänster

Klicka på **Spara och gå till Avancerade inställningar**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

Klicka på **Spara och gå till Distribution**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure22.png)

![Konfigurera Akamai](./media/header-akamai-tutorial/configure23.png)

![Konfigurera Akamai](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Distribution

#### <a name="ssh"></a>SSH

1. Gå till Lägg till program , Välj **SSH**.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure25.png)

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure26.png)

1. Konfigurera programidentitet.

    ![Konfigurera Akamai](./media/header-akamai-tutorial/configure27.png)

    a. Ange namn/beskrivning.

    b. Ange Application Server IP/FQDN och port för SSH.

    c. Ange SSH användarnamn / lösenfras *Kontrollera Akamai EAA.

    d. Ange det externa värdnamnet.

    e. Ange platsen för kopplingen och välj kopplingen.

#### <a name="authentication"></a>Autentisering

Klicka på **Spara och gå till Tjänster**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Tjänster

Klicka på **Spara och gå till Avancerade inställningar**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Avancerade inställningar

Klicka på Spara och gå till distribution

![Konfigurera Akamai](./media/header-akamai-tutorial/configure30.png)

![Konfigurera Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Distribution

Klicka på **Distribuera program**.

![Konfigurera Akamai](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Kerberos-program

#### <a name="adding-directory"></a>Lägga till katalog

![Konfigurera Akamai](./media/header-akamai-tutorial/configure33.png)

![Konfigurera Akamai](./media/header-akamai-tutorial/configure34.png)

![Konfigurera Akamai](./media/header-akamai-tutorial/configure35.png)

![Konfigurera Akamai](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Skapa Akamai-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Akamai. Arbeta med [Akamai Client supportteam](https://www.akamai.com/us/en/contact-us/) för att lägga till användarna i Akamai-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Akamai-panelen på åtkomstpanelen ska du automatiskt loggas in på den Akamai som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Akamai med Azure AD](https://aad.portal.azure.com/)
