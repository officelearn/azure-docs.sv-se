---
title: 'Självstudier: Azure Active Directory-integrering med SAP Fiori | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 897685eb967e03cfd30182eec6b237e27386496c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092157"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Självstudier: Azure Active Directory-integrering med SAP Fiori

I den här självstudien får du lära dig hur du integrerar SAP Fiori med Azure Active Directory (AD Azure).

Integrera SAP Fiori med Azure AD ger dig följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till SAP Fiori.
* Användare kan vara loggas in automatiskt till SAP Fiori med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SAP Fiori, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En SAP Fiori-prenumeration med enkel inloggning aktiverat.
* SAP Fiori 7.20 eller senare krävs.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar du och testa Azure AD enkel inloggning i en testmiljö och integrera SAP Fiori med Azure AD.

SAP Fiori stöder följande funktioner:

* **SP-initierad enkel inloggning**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Lägg till SAP Fiori i Azure portal

Om du vill integrera SAP Fiori med Azure AD, måste du lägga till SAP Fiori i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. I den vänstra menyn väljer du **Azure Active Directory**.

    ![Azure Active Directory-alternativet](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett program, Välj **nytt program**.

    ![Alternativet nytt program](common/add-new-app.png)

1. I sökrutan anger **SAP Fiori**. I sökresultaten väljer **SAP Fiori**, och välj sedan **Lägg till**.

    ![SAP Fiori i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP Fiori baserat på en användare med namnet **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta ett länkat förhållande mellan en Azure AD-användare och relaterade användaren i SAP Fiori.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP Fiori, måste du utföra följande byggblock:

| Aktivitet | Beskrivning |
| --- | --- |
| **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** | Gör det möjligt för användarna att använda den här funktionen. |
| **[Konfigurera SAP Fiori enkel inloggning](#configure-sap-fiori-single-sign-on)** | Konfigurerar inställningar för enkel inloggning i programmet. |
| **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** | Med namnet Britta Simon tester Azure AD enkel inloggning för en användare. |
| **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)** | Gör det möjligt för Britta Simon att använda Azure AD enkel inloggning. |
| **[Skapa en SAP Fiori-testanvändare](#create-an-sap-fiori-test-user)** | Skapar en motsvarighet för Britta Simon i SAP Fiori som är länkad till en Azure AD-representation av användaren. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Kontrollerar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet konfigurerar du Azure AD enkel inloggning med SAP Fiori i Azure-portalen.

1. Öppna ett nytt webbläsarfönster och logga in till SAP Fiori företagets platsen som en administratör.

1. Se till att **http** och **https** tjänster är aktiva och att de relevanta portarna har tilldelats transaktionskod **SMICM**.

1. Logga in till SAP Business-klienten för SAP-system **T01**, där enkel inloggning krävs. Aktivera HTTP-Session säkerhetshantering.

    1. Gå till transaktionskod **SICF_SESSIONS**. Alla relevanta profil parametrar med aktuella värden visas. De ser ut som i följande exempel:

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
        > Justera de parametrar utifrån organisationens krav. Föregående parametrarna anges endast som ett exempel.

    1. Om det behövs justera parametrarna i profilen instans (standard) för SAP-system och starta om SAP-system.

    1. Dubbelklicka på relevanta klienten om du vill aktivera en HTTP-security-session.

        ![Sidan aktuella värden för relevanta profil parametrar i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktivera följande SICF tjänster:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Gå till transaktionskod **SAML2** i Business-klienten för SAP-system [**T01/122**]. Konfigurationen Användargränssnittet öppnas i ett nytt webbläsarfönster. I det här exemplet använder vi Business Client för SAP-system 122.

    ![SAP Fiori Business Client på inloggningssidan](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Ange ditt användarnamn och lösenord och välj sedan **inloggning**.

    ![Sidan SAML 2.0 konfiguration av ABAP System T01/122 i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. I den **providernamn** rutan ersätter **T01122** med **http:\//T01122**, och välj sedan **spara**.

    > [!NOTE]
    > Som standard leverantörens namn är i formatet \<sid >\<klienten >. Azure AD förväntar sig namnet i formatet \<protokollet > ://\<namn >. Vi rekommenderar att du behåller providernamn som https\://\<sid >\<klienten > så att du kan konfigurera flera SAP Fiori ABAP-motorer i Azure AD.

    ![Uppdaterade Providernamnet på sidan SAML 2.0 konfiguration av ABAP System T01/122 i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Välj **fliken lokala Provider** > **Metadata**.

1. I den **SAML 2.0-Metadata** dialogrutan rutan, ladda ned den genererade XML-fil och spara filen på datorn.

    ![Hämta Metadata länken i dialogrutan SAP SAML 2.0-Metadata](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. I den [Azure-portalen](https://portal.azure.com/)i den **SAP Fiori** application integration väljer **enkel inloggning**.

    ![Alternativet för enkel inloggning](common/select-sso.png)

1. I den **väljer du en metod för enkel inloggning** fönstret Välj **SAML** eller **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I den **ange in enkel inloggning med SAML** väljer **redigera** (på pennikonen) att öppna den **SAML grundkonfiguration** fönstret.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    1. Välj **ladda upp metadatafilen**.

        ![Alternativet ladda upp metadata för fil](common/upload-metadata.png)

   1. Välj mappikonen för att välja metadatafilen, och välj sedan **överför**.

       ![Välj metadatafilen och välj sedan knappen ladda upp](common/browse-upload-metadata.png)

1. När metadatafilen har överförts, den **identifierare** och **svars-URL** värden fylls i automatiskt i den **SAML grundkonfiguration** fönstret. I den **inloggnings-URL** anger en URL som har följande mönster: https:\//\<din företags-instans av SAP Fiori\>.

    ![SAP Fiori-domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    > [!NOTE]
    > Ett fåtal kunder rapportera fel som rör felkonfigurerad **svars-URL** värden. Om du ser det här felet kan använda du följande PowerShell-skript för att ange rätt svars-URL för din instans:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Du kan ange den `ServicePrincipal` objekt-ID själv innan du kör skriptet eller du kan skicka.

1. SAP Fiori-program som förväntar SAML-intyg kan på ett specifikt format. Konfigurera följande anspråk för det här programmet. Att hantera dessa attributvärden i den **ange in enkel inloggning med SAML** väljer **redigera**.

    ![Fönstret attribut för användare](common/edit-attribute.png)

1. I den **användarattribut och anspråk** fönstret Konfigurera SAML-tokenattribut som visas i föregående bild. Utför sedan följande steg:

    1. Välj **redigera** att öppna den **hantera användaranspråk** fönstret.

    1. I den **omvandling** väljer **ExtractMailPrefix()** .

    1. I den **Parameter 1** väljer **user.userprinicipalname**.

    1. Välj **Spara**.

       ![Fönstret hantera användarens anspråk](./media/sapfiori-tutorial/nameidattribute.png)

       ![Transformationsavsnittet i fönstret hantera användarens anspråk](./media/sapfiori-tutorial/nameidattribute1.png)


1. I den **ange in enkel inloggning med SAML** fönstret i den **SAML-signeringscertifikat** väljer **hämta** bredvid **Federation Metadata XML**. Markera ett nedladdningsalternativ baserat på dina krav. Spara certifikatet på datorn.

    ![Hämtningsalternativet certifikat](common/metadataxml.png)

1. I den **konfigurera SAP Fiori** avsnittet, kopiera följande URL: er utifrån dina behov:

    * Inloggningswebbadress
    * Microsoft Azure Active Directory-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Konfigurera SAP Fiori enkel inloggning

1. Logga in till SAP-system och gå till transaktionskod **SAML2**. Ett nytt webbläsarfönster öppnas med SAML-konfigurationssidan.

1. För att konfigurera slutpunkter för en betrodd identitetsleverantör (Azure AD), Välj den **betrodda Providers** fliken.

    ![Fliken Betrodda Providers i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Välj **Lägg till**, och välj sedan **ladda upp metadatafilen** på snabbmenyn.

    ![Lägg till och ladda upp metadatafilen alternativen i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Ladda upp metadatafilen som du hämtade i Azure-portalen. Välj **Nästa**.

    ![Välj metadatafil att överföra i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. På nästa sida i den **Alias** anger aliasnamnet. Till exempel **aadsts**. Välj **Nästa**.

    ![Rutan Alias i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Se till att värdet i den **Sammandragsalgoritmen** rutan är **SHA-256**. Välj **Nästa**.

    ![Kontrollera Digest Algorithm värdet i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Under **slutpunkter för enkel inloggning**väljer **HTTP POST**, och välj sedan **nästa**.

    ![Alternativ för enkel inloggning slutpunkter i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Under **enkel utloggning slutpunkter**väljer **omdirigering för HTTP**, och välj sedan **nästa**.

    ![Alternativ för enkel utloggning slutpunkter i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Under **artefakt slutpunkter**väljer **nästa** att fortsätta.

    ![Alternativ för slutpunkter av artefakt i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Under **autentiseringskrav**väljer **Slutför**.

    ![Krav för autentiseringsalternativ och slutför alternativet i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Välj **Trusted Provider** > **identitetsfederation** (längst ned på sidan). Välj **Redigera**.

    ![Flikarna Trusted Provider och identitetsfederation i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Välj **Lägg till**.

    ![Alternativet Lägg till på fliken identitetsfederation](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. I den **stöds NameID formaterar** dialogrutan **Ospecificerad**. Välj **OK**.

    ![Dialogrutan stöds NameID-format och alternativ i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Värdena för **användar-ID: T källa** och **ID mappning användarläge** fastställa länken mellan SAP-användare och Azure AD-anspråk.  

    **Scenario 1**: SAP-användare till Azure AD-mappning

    1. I SAP, under **information av NameID-Format ”Ospecificerad”** , notera detaljerna:

        ![Den information för NameID-Format ”okänt” dialogrutan i SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. I Azure-portalen under **användarattribut och anspråk**, notera de begärda anspråk från Azure AD.

        ![Dialogrutan användarattribut och anspråk i Azure portal](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenario 2**: Välj det användar-ID för SAP som baseras på den Konfigurera e-postadressen i SU01. I det här fallet bör du konfigurerat e-post-ID i SU01 för varje användare som kräver enkel inloggning.

    1.  I SAP, under **information av NameID-Format ”Ospecificerad”** , notera detaljerna:

        ![Den information för NameID-Format ”okänt” dialogrutan i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. I Azure-portalen under **användarattribut och anspråk**, notera de begärda anspråk från Azure AD.

       ![Dialogrutan användarattribut och anspråk i Azure portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Välj **spara**, och välj sedan **aktivera** att aktivera identitetsprovidern.

    ![Spara och aktivera alternativen i SAP](./media/sapfiori-tutorial/configuration1.png)

1. Välj **OK** när du tillfrågas.

    ![Alternativet OK i dialogrutan för SAML 2.0-konfiguration i SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** > **Användare** > **Alla användare**.

    ![Användarna och alternativ för alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **brittasimon\@\<din företagsdomän >.\< tillägget >** . Till exempel **brittasimon\@contoso.com**.

    1. Välj den **Show lösenord** markerar du kryssrutan. Skriv ned värdet som visas i den **lösenord** box.

    1. Välj **Skapa**.

    ![Fönstret användare](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska ge du Britta Simon åtkomst till SAP Fiori så att hon kan använda Azure enkel inloggning.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** > **SAP Fiori**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. I listan med program väljer **SAP Fiori**.

    ![SAP Fiori i programlistan](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I den **användare och grupper** väljer **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** fönstret Välj rollen relevant för användaren i listan. Välj **Välj**.

1. I den **Lägg till tilldelning** väljer **tilldela**.

### <a name="create-an-sap-fiori-test-user"></a>Skapa en SAP Fiori-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i SAP Fiori. Arbeta med ditt interna SAP-team av experter eller din organisation SAP-partner för att lägga till användaren i SAP Fiori-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

1. När identitetsprovidern Azure AD har aktiverats i SAP Fiori försök komma åt någon av följande webbadresser att testa enkel inloggning (du inte bör ange ett användarnamn och lösenord):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Ersätt *sapurl* med det faktiska värdnamnet för SAP.

1. Testa URL bör ta dig till följande test-programsidan i SAP. Om du öppnar sidan har Azure AD enkel inloggning har ställts in.

    ![Standard testa programsidan i SAP](./media/sapfiori-tutorial/testingsso.png)

1. Om du uppmanas ange ett användarnamn och lösenord, aktivera spårning för att diagnostisera problemet. Använd följande URL för spårningen: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool? sap-client = 122 & sap-language = SV #.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer kan du granska dessa artiklar:

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
