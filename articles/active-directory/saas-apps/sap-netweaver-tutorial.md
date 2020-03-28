---
title: 'Självstudiekurs: Azure Active Directory Single sign-on (SSO) integration med SAP NetWeaver | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 700f2ca4d46b3483531fa0784cb78699befb20ca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897740"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Självstudiekurs: Azure Active Directory Single sign-on (SSO) integration med SAP NetWeaver

I den här självstudien får du lära dig hur du integrerar SAP NetWeaver med Azure Active Directory (Azure AD). När du integrerar SAP NetWeaver med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP NetWeaver.
* Gör att användarna automatiskt loggas in på SAP NetWeaver med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SAP NetWeaver enkel inloggning (SSO) aktiverad prenumeration.
* SAP NetWeaver V7.20 krävs minst

## <a name="scenario-description"></a>Scenariobeskrivning

SAP NetWeaver stöder både **SAML** **(SP initierade SSO)** och **OAuth**. I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. 

> [!NOTE]
> Konfigurera programmet antingen i SAML eller i OAuth enligt organisationens krav. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Lägga till SAP NetWeaver från galleriet

Om du vill konfigurera integreringen av SAP NetWeaver i Azure Active Directory måste du lägga till SAP NetWeaver från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SAP NetWeaver** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SAP NetWeaver** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Konfigurera och testa en azure AD-inloggning för SAP NetWeaver

Konfigurera och testa Azure AD SSO med SAP NetWeaver med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAP NetWeaver.

Så här konfigurerar och testar du Azure AD SSO med SAP NetWeaver:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. **[Konfigurera SAP NetWeaver med SAML](#configure-sap-netweaver-using-saml)** för att konfigurera SSO-inställningarna på programsidan.
    1. **[Skapa SAP NetWeaver-testanvändare](#create-sap-netweaver-test-user)** för att ha en motsvarighet till B.Simon i SAP NetWeaver som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.
1. **[Konfigurera SAP NetWeaver för OAuth](#configure-sap-netweaver-for-oauth)** för att konfigurera OAuth-inställningarna på programsidan.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning i Azure Active Directory med SAP NetWeaver genom att göra följande:

1. Öppna ett nytt webbläsarfönster och logga in på din SAP NetWeaver-företagswebbplats som administratör

1. Se till att tjänsterna **http** och **https** är aktiva och att lämpliga portar tilldelas i transaktionskoden **SMICM**.

1. Logga in på affärsklienten för SAP System (T01), där SSO krävs och aktivera HTTP Security session Management.

    a. Gå till transaktionskod **SICF_SESSIONS**. Den visar alla relevanta profilparametrar med aktuella värden. De ser ut så här:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Justera parametrarna ovan enligt organisationens krav. Parametrarna ovan anges här endast som indikation.

    b. Om det behövs justera parametrar, i instans / standard profil SAP-system och starta om SAP-systemet.

    c. Dubbelklicka på relevant klient för att aktivera HTTP-säkerhetssession.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktivera nedan SICF-tjänsterna nedan:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Gå till transaktionskod **SAML2** i SAP-systemets företagsklient [T01/122]. Ett användargränssnitt öppnas i en webbläsare. I det här exemplet antog vi att SAP-företagsklienten är 122.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Ange ditt användarnamn och lösenord som ska anges i användargränssnittet och klicka på **Redigera**.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Ersätt **providernamn** från T01122 till `http://T01122` och klicka på **Spara**.

    > [!NOTE]
    > Som standard leverantörsnamn kommer som `<sid><client>` format men `<protocol>://<name>`Azure AD förväntar namn `https://<sid><client>` i formatet , rekommendera att underhålla providernamn så att flera SAP NetWeaver ABAP motorer att konfigurera i Azure AD.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Generera metadata för tjänstleverantör**: – När vi är klara med att konfigurera inställningarna för den **lokala leverantören** och **betrodda leverantörer** i användargränssnittet för SAML 2.0 är nästa steg att generera tjänstleverantörens metadatafil (som innehåller alla inställningar, när det gäller autentisering och andra konfigurationer i SAP). När den här filen har genererats måste vi ladda upp den i Azure AD.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Gå till **fliken Local Provider** (Lokal provider).

    b. Klicka på **Metadata**.

    c. Spara den genererade **METADATA XML-filen** på datorn och ladda upp den i avsnittet **Grundläggande SAML-konfiguration** för att automatiskt fylla i **URL-värdena identifierare** och **Svara** i Azure-portalen.

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan **SAP NetWeaver-programintegration** hittar du avsnittet **Hantera** och väljer **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande steg:

    a. Klicka på **Ladda upp metadatafil** om du vill ladda upp **metadatafilen för Tjänsteleverantören**, som du har fått tidigare.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i textrutan i avsnittet **Grundläggande SAML-konfiguration** enligt följande:

    d. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Vi har fått rapporter från några kunder om ett fel med felaktig svars-URL som har konfigurerats för deras instans. Om du får ett sådant fel kan du använda följande PowerShell-skript som en tillfällig åtgärd för att ange rätt svars-URL för din instans:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal Object ID ska anges av dig först, eller så kan du skicka det också här.

1. SAP NetWeaver-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    a. Öppna dialogrutan **Hantera användaranspråk** genom att klicka på **redigeringsikonen**.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Välj **ExtractMailPrefix()** i listan **Transformering**.

    c. Välj **user.userprincipalname**i listan **Parameter 1** .

    d. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera SAP NetWeaver.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP NetWeaver.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I listan över program väljer du **SAP NetWeaver**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sap-netweaver-using-saml"></a>Konfigurera SAP NetWeaver med SAML

1. Logga in på SAP-systemet och gå till transaktionskoden SAML2. Ett nytt webbläsarfönster öppnas med en skärm för konfiguration av SAML.

2. För att konfigurera slutpunkter för betrodda identitetsproviders (Azure AD) går du till fliken **Trusted Providers** (Betrodda providers).

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Tryck på **Lägg till** och välj **Ladda upp metadatafil** på snabbmenyn.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Ladda upp metadatafilen som du har laddat ned från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Ange aliasnamnet på nästa skärm, Till exempel aadsts och tryck på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Se till att din **sammandragsalgoritm** ska vara **SHA-256** och att den inte kräver några ändringar och tryck på **Nästa**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. På **Single Sign-On Endpoints** (Slutpunkter för enkel inloggning) använder du **HTTP POST** och klickar på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. På **Single Logout Endpoints** (Slutpunkter för enkel utloggning) använder du **HTTP POST** och klickar på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. På **Artifact Endpoints** (Slutpunkter för artefakter) trycker du på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Vid **Autentiseringskrav** klickar du på **Slutför**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Gå till fliken **Trusted Provider** > **Identity Federation** (längst ned på skärmen). Klicka på **Redigera**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Klicka på **Lägg till** under fliken **Identitetsfederation** (det nedre fönstret).

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Välj **Ospecificerat** i **namnID-format som stöds** i popup-fönstret och klicka på OK.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Observera att värden för **användar-ID-ursprungs-** och **användar-ID-mappningsläge** bestämmer länken mellan SAP-användare och Azure AD-anspråk.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenario: SAP-användare till Azure AD-användarmappning.

    a. Skärmbild med NameID-information från SAP.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Skärmbild som nämner nödvändiga anspråk från Azure AD.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenario: Välj SAP-användar-ID baserat på konfigurerad e-postadress i SU01. I det här fallet ska e-post-ID konfigureras i su01 för varje användare som behöver SSO.

    a.  Skärmbild med NameID-information från SAP.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. skärmbild som nämner nödvändiga anspråk från Azure AD.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Klicka på **Spara** och klicka sedan på **Aktivera** för att aktivera identitetsprovider.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/configuration1.png)

16. Klicka på **OK** när du uppmanas till detta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Skapa SAP NetWeaver-testanvändare

    I det här avsnittet skapar du en användare som heter B.simon i SAP NetWeaver. Arbeta med ditt interna SAP-expertteam eller med din organisations SAP-partner för att lägga till användare i SAP NetWeaver-plattformen.

## <a name="test-sso"></a>Testa SSO

1. När identitetsprovidern Azure AD aktiverades försöker du öppna nedanstående URL för att kontrollera SSO (det kommer ingen uppmaning för användarnamn och lösenord)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (eller) använd webbadressen nedan

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Ersätt sapurl med själva SAP-värdnamnet.

2. Webbadressen ovan bör ta dig under den nämnda skärmen. Om du kan komma till sidan nedan är Azure AD SSO-installationen klar.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/testingsso.png)

3. Om du ser en uppmaning för användarnamn och lösenord ska du diagnostisera problemet genom att aktivera spårning med hjälp av nedanstående URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Konfigurera SAP NetWeaver för OAuth

1. SAP-dokumenterad process är tillgänglig på platsen: [NetWeaver Gateway Service Aktivera och OAuth 2.0 Scope Creation](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Gå till SPRO och hitta **Aktivera och underhålla tjänster**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth01.png)

3. I det här exemplet vill vi `DAAG_MNGGRP` ansluta OData-tjänsten: med OAuth till Azure AD SSO. Använd den tekniska tjänstnamnsökningen för tjänsten `DAAG_MNGGRP` och aktivera om `green` den ännu inte är aktiv, redan (leta efter status under fliken ICF-noder). Kontrollera om systemalias (det anslutna backend-systemet, där tjänsten faktiskt körs) är korrekt.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth02.png)

    * Klicka sedan på tryckknapp **OAuth** i `scope` det övre knappfältet och tilldela (behåll standardnamnet som erbjuds).

4. För vårt exempel `DAAG_MNGGRP_001`är omfattningen , det genereras från tjänstnamnet genom att automatiskt lägga till ett nummer. Rapporten `/IWFND/R_OAUTH_SCOPES` kan användas för att ändra namn på scope eller skapa manuellt.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Meddelande `soft state status is not supported` – kan ignoreras, eftersom inga problem. För mer information, se [här](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Skapa en tjänstanvändare för OAuth 2.0-klienten

1. OAuth2 använder `service ID` en för att hämta åtkomsttoken för slutanvändaren för dess räkning. Viktig begränsning av OAuth-design: `OAuth 2.0 Client ID` måste `username` vara identisk med OAuth 2.0-klienten som används för inloggning när du begär en åtkomsttoken. Därför kommer vi till exempel att registrera en OAuth 2.0-klient med namnet CLIENT1, och som en förutsättning måste en användare med samma namn (CLIENT1) finnas i SAP-systemet och den användaren som vi kommer att konfigurera för att användas av det hänvisade programmet. 

2. När du registrerar en OAuth-klient använder vi `SAML Bearer Grant type`.

    >[!NOTE]
    >För mer information, se OAuth 2.0 Kundregistrering för SAML Bearer Grant Type [här](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01 / skapa `System type` användaren CLIENT1 som och tilldela lösenord, spara det som behov av att tillhandahålla autentiseringsuppgifter till API programmerare, som ska bränna den med användarnamnet till den uppringande koden. Ingen profil eller roll ska tilldelas.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Registrera det nya Klient-ID:t OAuth 2.0 med guiden Skapa

1. Så här registrerar du en ny **OAuth 2.0-klientstarttransaktion** **SOAUTH2**. Transaktionen visar en översikt över OAuth 2.0-klienterna som redan har registrerats. Välj **Skapa** om du vill starta guiden för den nya OAuth-klienten som heter CLIENT1i i det här exemplet.

2. Gå till T-Code: **SOAUTH2** och Ange beskrivningen och klicka sedan på **nästa**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth04.png)

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth05.png)

3. Välj det redan tillagda **SAML2 IdP – Azure AD** i listrutan och spara.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth06.png)

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth07.png)

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth08.png)

4. Klicka på **Lägg till** under scopetilldelning för att lägga till det tidigare skapade scopet:`DAAG_MNGGRP_001`

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth09.png)

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth10.png)

5. Klicka på **slutför.**

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SAP NetWeaver med Azure AD](https://aad.portal.azure.com/)