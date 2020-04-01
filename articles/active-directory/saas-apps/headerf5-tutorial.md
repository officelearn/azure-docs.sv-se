---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med F5 | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 352f52a2a6d84d352bb46e09f104efde303307f5
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478044"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med F5

I den här självstudien får du lära dig hur du integrerar F5 med Azure Active Directory (Azure AD). När du integrerar F5 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till F5.
* Gör att användarna automatiskt loggas in på F5 med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med enkel inloggning i Azure AD finns [i Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).

* F5 enkel inloggning (SSO) aktiverad prenumeration.

* Distribuera den gemensamma lösningen kräver följande licens:

    * F5 BIG-IP® Bästa bunt (eller) 

    * F5 BIG-IP Access Policy Manager™ (APM) fristående licens 

    * F5 BIG-IP Access Policy Manager™ (APM) tilläggslicens på en befintlig BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM).

    * Utöver ovanstående licens kan F5-systemet också licensieras med: 

        * En URL-filtreringsprenumeration för att använda URL-kategoridatabasen

        * En F5 IP Intelligence-prenumeration för att upptäcka och blockera kända angripare och skadlig trafik
     
        * En säkerhetsmodul för nätverksmaskinvara (HSM) för att skydda och hantera digitala nycklar för stark autentisering

* F5 BIG-IP-systemet är etablerat med APM-moduler (LTM är valfritt)

* Även om det är valfritt rekommenderas det starkt att distribuera F5-systemen i en [enhetsgrupp för synkronisering/redundans](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (GD S/F), som innehåller det aktiva vänteparet, med en flytande IP-adress för hög tillgänglighet (HA). Ytterligare interfaceredundans kan uppnås med hjälp av LINK Aggregation Control Protocol (LACP). LACP hanterar de anslutna fysiska gränssnitten som ett enda virtuellt gränssnitt (aggregerad grupp) och upptäcker eventuella gränssnittsfel i gruppen.

* För Kerberos-program är ett lokalt AD-tjänstkonto för begränsad delegering.  Se [F5-dokumentation](https://support.f5.com/csp/article/K43063049) för att skapa ett AD-delegeringskonto.

## <a name="access-guided-configuration"></a>Få tillgång till guidad konfiguration

* Åtkomststyrd konfiguration stöds på F5 TMOS version 13.1.0.8 och senare. Om ditt BIG-IP-system kör en version under 13.1.0.8 läser du avsnittet **Avancerad konfiguration.**

* Åtkomststyrd konfiguration presenterar en helt ny och strömlinjeformad användarupplevelse. Den här arbetsflödesbaserade arkitekturen ger intuitiva konfigurationssteg för återtillkomna som är anpassade till den valda topologin.

* Innan du fortsätter till konfigurationen uppgraderar du den guidade konfigurationen genom att hämta det senaste användningsfallspaketet från [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748). För att uppgradera, följ nedanstående procedur.

    >[!NOTE]
    >Skärmbilderna nedan är för den senaste versionen (BIG-IP 15.0 med AGC version 5.0). Konfigurationsstegen nedan gäller för det här användningsfallet från 13.1.0.8 till den senaste BIG-IP-versionen.

1. På webbgränssnittet F5 BIG-IP klickar du på **Konfigurationen av Access >> Guide**.

1. På sidan **Guidad konfiguration** klickar du på Uppgradera **guidad konfiguration** i det övre vänstra hörnet.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure14.png) 

1. På popskärmen för uppgraderingsguidekonfiguration väljer du **Välj fil** för att ladda upp det nedladdade användningspaketet och klicka på knappen **Ladda upp och installera.**

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure15.png) 

1. När uppgraderingen är klar klickar du på knappen **Fortsätt.**

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* F5 SSO kan konfigureras på tre olika sätt.

- [Konfigurera F5 enkel inloggning för header-baserat program](#configure-f5-single-sign-on-for-header-based-application)

- [Konfigurera F5 enkel inloggning för Kerberos-program](kerbf5-tutorial.md)

- [Konfigurera F5 enkel inloggning för Avancerat Kerberos-program](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Viktiga autentiseringsscenarier

* Förutom Azure Active Directory-stöd för inbyggd integrering av moderna autentiseringsprotokoll som Open ID Connect, SAML och WS-Fed utökar F5 säker åtkomst för äldre autentiseringsappar för både intern och extern åtkomst med Azure AD, vilket möjliggör modern åtkomst (t.ex. lösenordslös åtkomst) till dessa program. Detta inkluderar:

* Rubrikbaserade autentiseringsappar

* Kerberos-autentiseringsappar

* Anonym autentisering eller inga inbyggda autentiseringsappar

* NTLM-autentiseringsappar (skydd med dubbla uppmaningar för användaren)

* Formulärbaserad applikation (skydd med dubbla uppmaningar för användaren)

## <a name="adding-f5-from-the-gallery"></a>Lägga till F5 från galleriet

Om du vill konfigurera integreringen av F5 i Azure AD måste du lägga till F5 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **F5** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **F5** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurera och testa en enkel Azure AD-inloggning för F5

Konfigurera och testa Azure AD SSO med F5 med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i F5.

Så här konfigurerar och testar du Azure AD SSO med F5:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera F5 SSO](#configure-f5-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa F5-testanvändare](#create-f5-test-user)** – om du vill ha en motsvarighet till B.Simon i F5 som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<YourCustomFQDN>.f5.com/`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<YourCustomFQDN>.f5.com/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [F5 Client support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du XML och certifikat för **federationsmetadata** **(Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera F5** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till F5.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **F5**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**
1. Klicka på **Villkorlig åtkomst** .
1. Klicka på **Ny policy**.
1. Du kan nu se din F5-app som en resurs för CA-principen och tillämpa eventuell villkorlig åtkomst, inklusive Multifactor Auth, Enhetsbaserad åtkomstkontroll eller identitetsskyddsprincip.

## <a name="configure-f5-sso"></a>Konfigurera F5 SSO

- [Konfigurera F5 enkel inloggning för Kerberos-program](kerbf5-tutorial.md)

- [Konfigurera F5 enkel inloggning för Avancerat Kerberos-program](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Konfigurera F5 enkel inloggning för header-baserat program

### <a name="guided-configuration"></a>Guidad konfiguration

1. Öppna ett nytt webbläsarfönster och logga in på din F5-företagswebbplats (Rubrikbaserad) som administratör och utför följande steg:

1. Navigera till **System > Certifikathantering > trafikcertifikathantering > SSL-certifikatlista**. Välj **Importera** från det högra hörnet. Ange ett **certifikatnamn** (kommer att refereras senare i konfigurationen). Välj Ladda upp fil i **certifikatkällan**och ange det certifikat som hämtas från Azure när SAML-enkel inloggning konfigureras. Klicka på **Importera**.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure12.png)
 
1. Dessutom behöver du **SSL-certifikat för programvärdnamnet. Navigera till System > certifikathantering > trafikcertifikathantering > SSL-certifikatlista**. Välj **Importera** från det högra hörnet. **Importtypen** är **PKCS 12(IIS)**. Ange ett **nyckelnamn** (kommer att refereras senare i konfigurationen) och ange PFX-filen. Ange **lösenordet** för PFX. Klicka på **Importera**.

    >[!NOTE]
    >I exemplet vårt appnamn är, `Headerapp.superdemo.live`vi använder ett Wild `WildCard-SuperDemo.live`Card-certifikat vårt nyckelnamn är .

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure13.png)

1. Vi använder den guidade upplevelsen för att konfigurera Azure AD-federationen och Application Access. Gå till – F5 BIG-IP **Main** och välj **Access > Guided Configuration > Federation > SAML Service Provider**. Klicka på **Nästa** och klicka sedan på **Nästa** för att påbörja konfigurationen.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure01.png)

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure02.png)
 
1. Ange ett **konfigurationsnamn**. Ange **entitets-ID** (samma som det du konfigurerade i Azure AD-programkonfigurationen). Ange **värdnamnet**. Lägg till en **beskrivning** som referens. Acceptera de återstående standardposterna och markera och klicka sedan på **Spara & Nästa**.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure03.png) 

1. I det här exemplet skapar vi en ny virtuell server som 192.168.30.20 med port 443. Ange IP-adressen för den virtuella servern i **måladressen**. Välj **klient-SSL-profil**, välj Skapa ny. Ange tidigare överfört programcertifikat (jokerteckencertifikatet i det här exemplet) och den associerade nyckeln och klicka sedan på **Spara & Nästa**.

    >[!NOTE]
    >i det här exemplet körs vår interna webbserver på port 888 och vi vill publicera den med 443.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure04.png) 

1. Under **Välj metod för att konfigurera IdP-anslutningen**anger du Metadata, klickar på Välj fil och överför METADATA XML-filen som hämtats tidigare från Azure AD. Ange ett unikt **namn** för SAML IDP-anslutning. Välj det **metadatasigneringscertifikat** som laddades upp tidigare. Klicka på **Spara & Nästa**.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure05.png)
 
1. Under **Välj en pool**anger du Skapa **ny** (välj alternativt en pool som den redan finns). Låt andra värden vara standard. Skriv IP-adressen under **IP-adress/nodnamn**under Poolservrar. Ange **port .** Klicka på **Spara & Nästa**.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure06.png)

1. På skärmen Enkla inloggningsinställningar väljer du **Aktivera enkel inloggning**. Under Vald enkel inloggningstyp väljer du **HTTP-rubrikbaserad**. Ersätt **session.saml.last.Identity** med **session.saml.last.attr.name.Identity** under Användarnamnskälla ( den här variabeln som anges med hjälp av anspråksmappning i Azure AD ). Under SSO-rubriker.

    * **HeaderName : MyAuthorization**

    * **Sidhuvudvärde : %{session.saml.last.attr.name.Identity}**

    * Klicka på **Spara & nästa**

    Se bilaga för fullständig lista över variabler och värden. Du kan lägga till fler rubriker efter behov.

    >[!NOTE]
    >Kontonamn Är f5-delegeringskontot skapat (kontrollera F5-dokumentation).

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure07.png) 

1. I den här vägledningen hoppar vi över slutpunktskontroller.  Mer information finns i F5-dokumentationen. Välj **Spara & Nästa**.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure08.png)

1. Acceptera standardinställningarna och klicka på **Spara & Nästa**. Mer information om HANTERING AV SAML-sessionshanteringsinställningar finns i F5-dokumentationen.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure09.png)

1. Granska sammanfattningsskärmen och välj **Distribuera** för att konfigurera BIG-IP. klicka på **Slutför**.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure10.png)

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Avancerad konfiguration

Det här avsnittet är avsett att användas om du inte kan använda den guidade konfigurationen eller vill lägga till/ändra ytterligare parametrar. Du behöver ett TLS/SSL-certifikat för programvärdnamnet.

1. Navigera till **System > Certifikathantering > trafikcertifikathantering > SSL-certifikatlista**. Välj **Importera** från det högra hörnet. **Importtypen** är **PKCS 12(IIS)**. Ange ett **nyckelnamn** (kommer att refereras senare i konfigurationen) och ange PFX-filen. Ange **lösenordet** för PFX. Klicka på **Importera**.

    >[!NOTE]
    >I exemplet vårt appnamn är, `Headerapp.superdemo.live`vi använder ett Wild `WildCard-SuperDemo.live`Card-certifikat vårt nyckelnamn är .
  
    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Lägga till en ny webbserver i BigIP-F5

1. Klicka på **Main > IApps > Application Services > Application > Create**.

1. Ange **namnet** och under **Mall** välj **f5.http**.
 
    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure18.png)

1. Vi kommer att publicera vår HeaderApp2 externt som HTTPS i detta fall, **hur ska BIG-IP-systemet hantera SSL-trafik?** Vi anger **Avsluta SSL från klient, klartext till servrar (SSL Avlastning).** Ange ditt certifikat och nyckel under **Vilket SSL-certifikat vill du använda?** och **Vilken SSL-privat nyckel vill du använda?**. Ange den virtuella serverns IP under **Vilken IP-adress vill du använda för den virtuella servern?**. 

    * **Ange annan information**

        * FQDN  

        * Ange spännande apppool eller skapa en ny.

        * Om du skapar en ny App Server ange **intern IP-adress** och **portnummer**.

        ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure19.png) 

1. Klicka på **Klar**.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure20.png) 

1. Kontrollera att appegenskaperna kan ändras. Klicka på **Huvud > IApps > Application Services: Program >> HeaderApp2**. Avmarkera **Strikta uppdateringar** (vi kommer att ändra vissa inställningar utanför det grafiska gränssnittet). Klicka på **Knappen Uppdatera.**

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure21.png) 

1. Nu bör du kunna bläddra i den virtuella servern.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Konfigurera F5 som SP och Azure som IDP

1.  Klicka på **> Federation> SAML-serviceprovider > Lokal SP-tjänst > klicka på Skapa eller + signera**.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure22.png)

1. Ange information för tjänsten Provider. Ange **namn** som representerar F5 SP-konfiguration. Ange **enhets-ID** (vanligtvis samma som programmets URL).

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure23.png)

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure24.png)

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure25.png)

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure26.png)

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure27.png)

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Skapa Idp-anslutning

1. Klicka på **Knappen Bind/Ta bort bindbindade IdP-kopplingar,** välj **Skapa ny IdP-koppling** och välj Alternativet Från **metadata** och utför sedan följande steg:
 
    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure29.png)

    a. Bläddra till metadata.xml-filen som hämtats från Azure AD och ange ett **identitetsprovidernamn**.

    b. Klicka **på ok**.

    c. Kopplingen skapas och certifikatet är automatiskt klar från metadata xml-filen.
    
    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure30.png)

    d. Konfigurera F5BIG-IP för att skicka alla begäranden till Azure AD.

    e. Klicka på **Lägg till ny rad**, välj **AzureIDP** (som skapats i tidigare steg, ange 

    f. **Matchande källa = %{session.server.landinguri}** 

    g. **Matchande värde = /***

    h. Klicka på **uppdatera**

    i. Klicka på **OK**

    j. **INSTALLATIONEN AV SAML IDP har slutförts**
    
    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Konfigurera F5-princip för att omdirigera användare till Azure SAML IDP

1. Så här konfigurerar du F5-principen för att omdirigera användare till Azure SAML IDP:

    a. Klicka på **> > > åtkomstprofiler i >.**

    b. Klicka på knappen **Skapa.**

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure32.png)
 
    c. Ange **namn** (HeaderAppAzureSAMLPolicy i exemplet).

    d. Du kan anpassa andra inställningar i F5-dokumentationen.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure33.png)

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure34.png) 

    e. Klicka på **Klar**.

    f. När principskapandet har slutförts klickar du på principen och går till fliken **Åtkomstprincip.**

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure35.png)
 
    g. Klicka på **visualrineraren**och redigera **åtkomstprincip för profillänken.**

    h. Klicka på + Logga in i Visual Policy editor och välj **SAML Auth**.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure36.png)

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure37.png)
 
    i. Klicka på **Lägg till objekt**.

    j. Klicka på **SPARA**under **Egenskaper** anger **Namn** och under **AAA Server** väljer du det tidigare konfigurerade SP.
 
    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure38.png)

    k. Den grundläggande principen är klar kan du anpassa principen för att införliva ytterligare källor / attribut butiker.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure39.png)
 
    l. Se till att du klickar på länken **Använd åtkomstprincip** högst upp.

### <a name="apply-access-profile-to-the-virtual-server"></a>Tillämpa åtkomstprofil på den virtuella servern

1. Tilldela åtkomstprofilen till den virtuella servern för att F5 BIG-IP APM ska kunna tillämpa profilinställningarna på inkommande trafik och köra den tidigare definierade åtkomstprincipen.

    a. Klicka på > **huvudvirkliga** > virtuella servrar för**lokal trafik**. **Main**

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure40.png)
 
    b. Klicka på den virtuella servern, bläddra till **avsnittet Åtkomstprincip,** i listrutan **Åtkomstprofil** och välj den SAML-princip som skapats (i exemplet HeaderAppAzureSAMLPolicy)

    c. Klicka på **uppdatera**
 
    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure41.png)

    d. skapa en F5 BIG-IP iRule® för att extrahera anpassade SAML-attribut från det inkommande påståendet och skicka dem som HTTP-huvuden till backend-testprogrammet. Klicka på > > **iRules-listan för > > iRule-> klicka på Skapa**

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure42.png)
 
    e. Klistra in texten F5 BIG-IP iRule nedan i definitionsfönstret.

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure43.png)
 
    när RULE_INIT { ange statisk::debug 0 } när ACCESS_ACL_ALLOWED {

    set AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] om { $static::debug } { logga local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } om { !( [HTTP::header finns "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    set AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name.http://schemas.microsoft.com/identity/claims/displayname"] om { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } om { !( [HTTP::header finns "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    set AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] om { $static::debug } { logga local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } om { !( [HTTP::header finns "AZUREAD_EMAILADDRESS"]) } { HTTP::header infoga "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Exempel på utdata nedan**

    ![F5-konfiguration (rubrikbaserad)](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Skapa F5-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i F5. Arbeta med [F5 Client support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att lägga till användarna i F5-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på F5-panelen på åtkomstpanelen ska du automatiskt loggas in på den F5 som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova F5 med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera F5 enkel inloggning för Kerberos-program](kerbf5-tutorial.md)

- [Konfigurera F5 enkel inloggning för Avancerat Kerberos-program](advance-kerbf5-tutorial.md)

