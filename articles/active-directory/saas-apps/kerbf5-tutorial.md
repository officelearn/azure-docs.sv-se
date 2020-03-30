---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med F5 | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f06b0b5aa59328d2fe39d501cfdf3ad7524427
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75431460"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med F5

I den här självstudien får du lära dig hur du integrerar F5 med Azure Active Directory (Azure AD). När du integrerar F5 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till F5.
* Gör att användarna automatiskt loggas in på F5 med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

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

2. På sidan **Guidad konfiguration** klickar du på Uppgradera **guidad konfiguration** i det övre vänstra hörnet.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure14.png) 

3. På popskärmen för uppgraderingsguidekonfiguration väljer du **Välj fil** för att ladda upp det nedladdade användningspaketet och klicka på knappen **Ladda upp och installera.**

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure15.png) 

4. När uppgraderingen är klar klickar du på knappen **Fortsätt.**

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* F5 stöder **SP och IDP** initierad SSO
* F5 SSO kan konfigureras på tre olika sätt.

- [Konfigurera F5 enkel inloggning för Kerberos-program](#configure-f5-single-sign-on-for-kerberos-application)

- [Konfigurera F5 enkel inloggning för header-baserat program](headerf5-tutorial.md)

- [Konfigurera F5 enkel inloggning för Avancerat Kerberos-program](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Viktiga autentiseringsscenarier

Förutom Azure Active Directory-stöd för inbyggd integrering av moderna autentiseringsprotokoll som Open ID Connect, SAML och WS-Fed utökar F5 säker åtkomst för äldre autentiseringsappar för både intern och extern åtkomst med Azure AD, vilket möjliggör moderna scenarier (t.ex. lösenordslös åtkomst) till dessa program. Detta inkluderar:

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

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du XML och certifikat för **federationsmetadata** **(Base64)** och väljer sedan **Hämta** för att hämta certifikatet och spara det på datorn.

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

- [Konfigurera F5 enkel inloggning för header-baserat program](headerf5-tutorial.md)

- [Konfigurera F5 enkel inloggning för Avancerat Kerberos-program](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Konfigurera F5 enkel inloggning för Kerberos-program

### <a name="guided-configuration"></a>Guidad konfiguration

1. Öppna ett nytt webbläsarfönster och logga in på din F5-företagswebbplats (Kerberos) som administratör och utför följande steg:

1. Du måste importera metadatacertifikatet till F5 som kommer att användas senare i installationsprocessen.

1. Navigera till **System > Certifikathantering > trafikcertifikathantering > SSL-certifikatlista**. Välj **Importera** från det högra hörnet. Ange ett **certifikatnamn** (kommer att refereras senare i konfigurationen). Välj Ladda upp fil i **certifikatkällan**och ange det certifikat som hämtas från Azure när SAML-enkel inloggning konfigureras. Klicka på **Importera**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure01.png) 

1. Dessutom behöver du **SSL-certifikat för programvärdnamnet. Navigera till System > certifikathantering > trafikcertifikathantering > SSL-certifikatlista**. Välj **Importera** från det högra hörnet. **Importtypen** är **PKCS 12(IIS)**. Ange ett **nyckelnamn** (kommer att refereras senare i konfigurationen) och ange PFX-filen. Ange **lösenordet** för PFX. Klicka på **Importera**.

    >[!NOTE]
    >I exemplet använder vårt `Kerbapp.superdemo.live`appnamn , vi använder ett Wild Card-certifikat vårt nyckelnamn är`WildCard-SuperDemo.live`

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure02.png) 
 
1. Vi använder den guidade upplevelsen för att konfigurera Azure AD-federationen och Application Access. Gå till – F5 BIG-IP **Main** och välj **Access > Guided Configuration > Federation > SAML Service Provider**. Klicka på **Nästa** och klicka sedan på **Nästa** för att påbörja konfigurationen.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure03.png) 

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure04.png)

1. Ange ett **konfigurationsnamn**. Ange **entitets-ID** (samma som det du konfigurerade i Azure AD-programkonfigurationen). Ange **värdnamnet**. Lägg till en **beskrivning** som referens. Acceptera de återstående standardposterna och markera och klicka sedan på **Spara & Nästa**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure05.png) 

1. I det här exemplet skapar vi en ny virtuell server som 192.168.30.200 med port 443. Ange IP-adressen för den virtuella servern i **måladressen**. Välj **klient-SSL-profil**, välj Skapa ny. Ange tidigare överfört programcertifikat (jokerteckencertifikatet i det här exemplet) och den associerade nyckeln och klicka sedan på **Spara & Nästa**.

    >[!NOTE]
    >i det här exemplet körs vår interna webbserver på port 80 och vi vill publicera den med 443.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure06.png)

1. Under **Välj metod för att konfigurera IdP-anslutningen**anger du Metadata, klickar på Välj fil och överför METADATA XML-filen som hämtats tidigare från Azure AD. Ange ett unikt **namn** för SAML IDP-anslutning. Välj det **metadatasigneringscertifikat** som laddades upp tidigare. Klicka på **Spara & Nästa**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure07.png)  

1. Under **Välj en pool**anger du Skapa **ny** (välj alternativt en pool som den redan finns). Låt andra värden vara standard. Skriv IP-adressen under **IP-adress/nodnamn**under Poolservrar. Ange **port .** Klicka på **Spara & Nästa**.
 
    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure08.png)

1. På skärmen Enkla inloggningsinställningar väljer du **Aktivera enkel inloggning**. Välj **Kerberos** **under Vald enkel inloggningstyp** . Ersätt **session.saml.last.Identity** med **session.saml.last.attr.name.Identity** under **Användarnamnskälla** ( den här variabeln som anges med hjälp av anspråksmappning i Azure AD ). Välj **Visa avancerad inställning**. Skriv domännamnet under **Kerberos Realm.** Under **Kontonamn/kontolösenord** anger APM-delegeringskontot och lösenordet. Ange domänkontrollantens IP i **KDC-fältet.** Klicka på **Spara & Nästa**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure09.png)   

1. I den här vägledningen hoppar vi över slutpunktskontroller.  Mer information finns i F5-dokumentationen.  Välj **Spara & Nästa**på skärmen .

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure10.png) 

1. Acceptera standardinställningarna och klicka på **Spara & Nästa**. Mer information om hanteringsinställningar för SAML-sessionshantering finns i F5-dokumentationen.


    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure11.png) 
 
1. Granska sammanfattningsskärmen och välj **Distribuera** för att konfigurera BIG-IP.
 
    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure12.png)

1. När programmet har konfigurerats klickar du på **Slutför**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Avancerad konfiguration

>[!NOTE]
>För referens klicka [här](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Konfigurera en AAA-server i Active Directory

Du konfigurerar en Active Directory AAA-server i APM (Access Policy Manager) för att ange domänkontrollanter och autentiseringsuppgifter som APM ska använda för att autentisera användare.

1.  Klicka på **Åtkomstprincip > AAA-servrar > Active Directory på**fliken Huvud. Listskärmen för Active Directory-servrar öppnas.

2.  Klicka på **Skapa**. Egenskapsskärmen för Ny server öppnas.

3.  Skriv ett unikt namn för autentiseringsservern i fältet **Namn.**

4.  Skriv namnet på Windows-domänen i fältet **Domännamn.**

5.  För inställningen **Serveranslutning** väljer du något av följande alternativ:

    * Välj **Använd pool** för att ställa in hög tillgänglighet för AAA-servern.

    * Välj **Direkt** om du vill konfigurera AAA-servern för fristående funktioner.

6.  Om du har valt **Direkt**skriver du ett namn i fältet **Domänkontrollant.**

7.  Om du har valt Använd **pool**konfigurerar du poolen:

    * Skriv ett namn i fältet Domännamn **för domänkontrollantpool.**

    * Ange **domänkontrollanterna** i poolen genom att skriva IP-adressen och värdnamnet för varje och klicka på knappen **Lägg till.**

    * För att övervaka AAA-serverns hälsa har du möjlighet att välja en hälsoövervakare: endast **gateway_icmp** övervakare är lämplig i det här fallet; Du kan välja den i listan **Serverpoolövervakare.**

8.  I fältet **Administratörsnamn** skriver du ett ärendekänsligt namn för en administratör som har administratörsbehörighet i Active Directory. APM använder informationen i fälten **Administratörsnamn** och **adminlösenord** för AD Query. Om Active Directory är konfigurerat för anonyma frågor behöver du inte ange ett administratörsnamn. Annars behöver APM ett konto med tillräcklig behörighet för att binda till en Active Directory-server, hämta information om användargrupper och hämta Active Directory-lösenordsprinciper för att stödja lösenordsrelaterade funktioner. (APM måste hämta lösenordsprinciper, till exempel om du väljer den uppmanas användaren att ändra lösenord före förfallodatum i en AD-frågeåtgärd.) Om du inte anger administratörskontoinformation i den här konfigurationen använder APM användarkontot för att hämta information. Detta fungerar om användarkontot har tillräcklig behörighet.

9.  Skriv administratörslösenordet som är associerat med domännamnet i fältet **Admin Password.**

10. Skriv in administratörslösenordet som är associerat med inställningen **Domännamn** i fältet **Verifiera administratörslösenord.**

11. Ange antalet dagar i fältet **Livstid för gruppcache.** Standardlivstiden är 30 dagar.

12. Ange antalet dagar i fältet Livstid för **lösenordsskyddsobjektcachen.** Standardlivstiden är 30 dagar.

13. Välj en krypteringstyp i listan **Kerberos Preauthentication Encryption Type.** Standard är **Ingen**. Om du anger en krypteringstyp innehåller BIG-IP-systemet Kerberos preauthentication-data i det första AS-REQ-paketet (Authentication Service Request).

14. I fältet **Timeout** skriver du ett timeout-intervall (i sekunder) för AAA-servern. (Den här inställningen är valfri.)

15. Klicka på **Klar**. Den nya servern visas i listan. Då läggs den nya Active Directory-servern till i listan Active Directory-servrar.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML-konfiguration

1. Du måste importera metadatacertifikatet till F5 som kommer att användas senare i installationsprocessen. Navigera till **System > Certifikathantering > trafikcertifikathantering > SSL-certifikatlista**. Välj **Importera** från det högra hörnet.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure18.png)

2. För att konfigurera SAML IDP **navigerar du till Access > Federation > SAML: Tjänsteleverantör > externa Idp-kopplingar**och klickar på **Skapa > från metadata**.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure19.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure20.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure21.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure22.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure23.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure24.png)

1. När du konfigurerar SAML SP navigerar du till **Access > Federation > SAML-serviceprovider > lokala SP-tjänster** och klickar på **Skapa**. Fyll i följande information och klicka på **OK**.

    * Typnamn: KerbApp200SAML
    * Enhets-ID*:https://kerbapp200.superdemo.live
    * Inställningar för SP-namn
    * Schema: https
    * Värd: kerbapp200.superdemo.live
    * Beskrivning: kerbapp200.superdemo.live

     ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure25.png)

     b. Välj SP-konfiguration, KerbApp200SAML och klicka på **Bind/UnBind IdP-kopplingar**.

     ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure26.png)

     ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure27.png)

     c. Klicka på **Lägg till ny rad** och markera den externa **IdP-koppling** som skapades i föregående steg, klicka på **Uppdatera**och klicka sedan på **OK**.

     ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure28.png)

1. Om du konfigurerar Kerberos SSO navigerar du till **Access > Enkel inloggning > Kerberos**, fullständig information och klickar på **Färdig**.

    >[!Note]
    > Du måste skapa och angiven kerberos-delegeringskonto. Se KCD-avsnittet (se tillägg för variabelreferenser)

    * **Användarnamn Källa**: session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **Källa för användarskape:** session.logon.last.domain

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure29.png)

1. Om du konfigurerar åtkomstprofil navigerar du till **Access > profil/principer > åtkomstprofil (per sessionsprinciper),** klickar på **Skapa**, slutför följande information och klickar på **Färdig**.

    * Namn: KerbApp200
    * Profiltyp: Alla
    * Profilomfattning: Profil
    * Språk: Engelska

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure30.png)

1. Klicka på namnet, KerbApp200, fyll i följande information och klicka på **Uppdatera**.

    * Domän Cookie: superdemo.live
    * SSO-konfiguration: KerAppSSO_sso

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure31.png)

1. Klicka på **Åtkomstprincip** och sedan på **Redigera åtkomstprincip** för profilen "KerbApp200".

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure32.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure33.png)

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN-expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain TEXT superdemo.live**

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure36.png)

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. Om du vill lägga till ny nod navigerar du till **Lokal trafik > noder > nodlista, klickar på Skapa**, slutför följande information och klickar sedan på **Slutför**.

    * Namn: KerbApp200
    * Beskrivning: KerbApp200
    * Adress: 192.168.20.200

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure39.png)

1. Om du vill skapa en ny pool navigerar du till **Lokal trafik > pooler > poollista, klickar på Skapa,** slutför följande information och klickar på **Färdig**.

    * Namn: KerbApp200-Pool
    * Beskrivning: KerbApp200-Pool
    * Hälsa Monitorer: http
    * Adress: 192.168.20.200
    * Serviceport: 81

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure40.png)

1. Om du vill skapa Virtual Server navigerar du till **lokal trafik > virtuella servrar > virtual serverlista > +** fyller du i följande information och klickar på **Färdig**.

    * Namn: KerbApp200
    * Måladress/mask: Värd 192.168.30.200
    * Serviceport: Port 443 HTTPS
    * Åtkomstprofil: KerbApp200
    * Ange den Åtkomstprofil som skapats i föregående steg

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure41.png)

        ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Ställa in Kerberos-delegering 

>[!NOTE]
>För referens klicka [här](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **Steg 1:** Skapa ett delegeringskonto

    **Exempel:**
    * Domännamn: **superdemo.live**

    * Namn på **Sam-konto: big-ipuser**

    * Ny-ADUser -Namn "APM delegering host/big-ipuser.superdemo.live@superdemo.live konto" -UserPrincipalName -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Läs-Host -AsSecureString "Password!1234")

* **Steg 2:** Ange SPN (på APM-delegeringskontot)

    **Exempel:**
    * setspn -En **värd /big-ipuser.superdemo.live** big-ipuser

* **Steg 3:** SPN-delegering (för apptjänstkontot) konfigurerar lämplig delegering för F5-kontot för delegering.
    I exemplet nedan konfigureras APM-delegeringskontot för KCD för FRP-App1.superdemo. live-appen.

    ![F5-konfiguration (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Lämna de uppgifter som nämns i ovanstående referensdokument under [detta](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html).

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

- [Konfigurera F5 enkel inloggning för header-baserat program](headerf5-tutorial.md)

- [Konfigurera F5 enkel inloggning för Avancerat Kerberos-program](advance-kerbf5-tutorial.md)

