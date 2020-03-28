---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med SAP Fiori | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 917ba9274276fec5d01a40bdf7219e8d4fee1395
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897768"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SAP Fiori

I den här självstudien får du lära dig hur du integrerar SAP Fiori med Azure Active Directory (Azure AD). När du integrerar SAP Fiori med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Fiori.
* Aktivera dina användare automatiskt inloggade på SAP Fiori med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SAP Fiori enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SAP Fiori stöder **SP** initierade SSO

> [!NOTE]
> För SAP Fiori initierad iFrame-autentisering rekommenderar vi att du använder parametern **IsPassive** i SAML AuthnRequest för tyst autentisering. Mer information om parametern **IsPassive** finns i [Azure AD SAML-information om enkel inloggning](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)

## <a name="adding-sap-fiori-from-the-gallery"></a>Lägga till SAP Fiori från galleriet

Om du vill konfigurera integreringen av SAP Fiori i Azure AD måste du lägga till SAP Fiori från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SAP Fiori** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SAP Fiori** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Konfigurera och testa en azure AD-inloggning för SAP Fiori

Konfigurera och testa Azure AD SSO med SAP Fiori med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAP Fiori.

Så här konfigurerar och testar du Azure AD SSO med SAP Fiori:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SAP Fiori SSO](#configure-sap-fiori-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa SAP Fiori-testanvändare](#create-sap-fiori-test-user)** – om du vill ha en motsvarighet till B.Simon i SAP Fiori som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Öppna ett nytt webbläsarfönster och logga in på din SAP Fiori-företagswebbplats som administratör.

1. Kontrollera att **http-** och **https-tjänster** är aktiva och att relevanta portar har tilldelats transaktionskoden **SMICM**.

1. Logga in på SAP Business Client för SAP-system **T01**, där enkel inloggning krävs. Aktivera sedan HTTP-hantering av säkerhetssessioner.

    1. Gå till transaktionskod **SICF_SESSIONS**. Alla relevanta profilparametrar med aktuella värden visas. De ser ut som följande exempel:

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
        > Justera parametrarna baserat på organisationens krav. Föregående parametrar anges endast som ett exempel.

    1. Om det behövs justerar du parametrar i instansprofilen (standard) för SAP-systemet och startar om SAP-systemet.

    1. Dubbelklicka på den relevanta klienten för att aktivera en HTTP-säkerhetssession.

        ![Sidan Aktuella värden för relevanta profilparametrar i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktivera följande SICF-tjänster:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Gå till transaktionskoden **SAML2** i Business Client för SAP-system [**T01/122**]. Konfigurationsgränssnittet öppnas i ett nytt webbläsarfönster. I det här exemplet använder vi Företagsklient för SAP-system 122.

    ![Inloggningssidan för SAP Fiori Business Client](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Ange ditt användarnamn och lösenord och välj sedan **Logga in**.

    ![SAML 2.0-konfigurationen av ABAP-system T01/122 sida i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Ersätt **T01122** i rutan **Providernamn** med **http:\//T01122**och välj sedan **Spara**.

    > [!NOTE]
    > Som standard är providernamnet i \<formatet sid \<>klient>. Azure AD förväntar sig \<att namnet i\<formatprotokollet>:// namn>. Vi rekommenderar att du underhåller\://\<providernamnet som \<https sid>-klient> så att du kan konfigurera flera SAP Fiori ABAP-motorer i Azure AD.

    ![Det uppdaterade leverantörsnamnet i SAML 2.0-konfigurationen av ABAP-system T01/122-sidan i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Välj **metadata för fliken** > **Lokal**leverantör .

1. I dialogrutan **SAML 2.0-metadata** hämtar du den genererade XML-filen för metadata och sparar den på datorn.

    ![Länken Hämta metadata i dialogrutan SAP SAML 2.0-metadata](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Leta reda på avsnittet **SAP Fiori** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts fylls **URL-värdena identifierare** och **svara** automatiskt i fönstret **Grundläggande SAML-konfiguration.** Ange en URL med följande mönster i rutan `https:\//\<your company instance of SAP Fiori\>`Logga in på **WEBBADRESS:** .

    > [!NOTE]
    > Några kunder rapporterar fel relaterade till felaktigt konfigurerade **svars-URL-värden.** Om det här felet visas kan du använda följande PowerShell-skript för att ange rätt svars-URL för din instans:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Du kan `ServicePrincipal` ställa in objekt-ID själv innan du kör skriptet, eller så kan du skicka det här.

1. SAP Fiori-programmet förväntar sig att SAML-påståendena är i ett visst format. Konfigurera följande anspråk för det här programmet. Om du vill hantera dessa attributvärden väljer du **Redigera**i fönstret **Konfigurera enkel inloggning med SAML** .

    ![Fönstret Användarattribut](common/edit-attribute.png)

1. I fönstret **Användarattribut & anspråk** konfigurerar du SAML-tokenattributen som visas i föregående bild. Utför sedan följande steg:

    1. Välj **Redigera** om du vill öppna fönstret **Hantera användarens anspråk.**

    1. Välj **ExtractMailPrefix()** i listan **Omformning.**

    1. Välj **user.userprincipalname**i listan **Parameter 1** .

    1. Välj **Spara**.

       ![Fönstret Hantera användarens anspråk](./media/sapfiori-tutorial/nameidattribute.png)

       ![Avsnittet Omvandling i fönstret Hantera användarens anspråk](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera SAP Fiori.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Fiori.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **SAP Fiori**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sap-fiori-sso"></a>Konfigurera SAP Fiori SSO

1. Logga in på SAP-systemet och gå till transaktionskoden **SAML2**. Ett nytt webbläsarfönster öppnas med konfigurationssidan för SAML.

1. Om du vill konfigurera slutpunkter för en betrodd identitetsprovider (Azure AD) väljer du fliken **Betrodda providers.**

    ![Fliken Betrodda leverantörer i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Välj **Lägg till**och välj sedan Ladda upp **metadatafil** på snabbmenyn.

    ![Alternativen För att lägga till och ladda upp metadatafil i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Ladda upp metadatafilen som du hämtade i Azure-portalen. Välj **Nästa**.

    ![Välj den metadatafil som ska överföras i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Ange aliasnamnet i rutan **Alias** på nästa sida. Till exempel **aadsts**. Välj **Nästa**.

    ![Rutan Alias i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Kontrollera att värdet i rutan **Digest-algoritm** är **SHA-256**. Välj **Nästa**.

    ![Verifiera värdet för digestalgoritmen i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Under **Slutpunkter för enkel inloggning**väljer du HTTP **POST**och väljer sedan **Nästa**.

    ![Alternativ för enkla inloggningsslutpunkter i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Under **Slutpunkter för enkel utloggning**väljer du **HTTP-omdirigering**och väljer sedan **Nästa**.

    ![Alternativ för eninloggningsslutpunkter i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Under **Artefaktslutpunkter**väljer du **Nästa** för att fortsätta.

    ![Alternativ för artefaktslutpunkter i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Under **Autentiseringskrav**väljer du **Slutför**.

    ![Alternativ för autentiseringskrav och alternativet Slutför i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Välj **Trusted Provider** > **Identity Federation** (längst ned på sidan). Välj **Redigera**.

    ![Flikarna Betrodd leverantör och identitetsfederation i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Välj **Lägg till**.

    ![Alternativet Lägg till på fliken Identitetsfederation](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Välj **Ospecificerad**i dialogrutan **NameID-format som stöds** . Välj **OK**.

    ![Dialogrutan NameID-format som stöds och alternativ i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Värdena för **användar-ID-käll-** och **användar-ID-mappningsläge** bestämmer länken mellan SAP-användaren och Azure AD-anspråket.  

    **Scenario 1:** SAP-användare till Azure AD-användarmappning

    1. I SAP, under **Information om NameID-format "Ospecificerat"**, notera detaljerna:

        ![Dialogrutan Information om NameID-format "Ospecificerat" i SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. Observera de anspråk som krävs från Azure AD under **Användarattribut & anspråk.**

        ![Dialogrutan Användarattribut & anspråk i Azure-portalen](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenario 2**: Välj SAP-användar-ID baserat på den konfigurerade e-postadressen i SU01. I det här fallet ska e-post-ID:et konfigureras i SU01 för varje användare som behöver SSO.

    1.  I SAP, under **Information om NameID-format "Ospecificerat"**, notera detaljerna:

        ![Dialogrutan Information om NameID-format "Ospecificerat" i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Observera de anspråk som krävs från Azure AD under **Användarattribut & anspråk.**

       ![Dialogrutan Användarattribut & anspråk i Azure-portalen](./media/sapfiori-tutorial/claimsaad2.png)

1. Välj **Spara**och välj sedan **Aktivera** för att aktivera identitetsprovidern.

    ![Alternativen Spara och aktivera i SAP](./media/sapfiori-tutorial/configuration1.png)

1. Välj **OK** när du uppmanas att göra det.

    ![Alternativet OK i dialogrutan SAML 2.0-konfiguration i SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Skapa SAP Fiori-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i SAP Fiori. Arbeta med ditt interna SAP-team av experter eller din organisation SAP-partner för att lägga till användaren i SAP Fiori-plattformen.

## <a name="test-sso"></a>Testa SSO

1. När identitetsprovidern Azure AD har aktiverats i SAP Fiori försöker du komma åt någon av följande webbadresser för att testa enkel inloggning (du bör inte uppmanas att ange ett användarnamn och lösenord):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Ersätt *sapurl* med det faktiska SAP-värdnamnet.

1. Test-URL:en ska ta dig till följande testprogramsida i SAP. Om sidan öppnas har azure AD enkel inloggning konfigurerats.

    ![Standardtestprogramsidan i SAP](./media/sapfiori-tutorial/testingsso.png)

1. Om du uppmanas att ange ett användarnamn och lösenord aktiverar du spårning för att diagnostisera problemet. Använd följande URL för spårningen:\//\<https: sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SAP Fiori med Azure AD](https://aad.portal.azure.com/)
