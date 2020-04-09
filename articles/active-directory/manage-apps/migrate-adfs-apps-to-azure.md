---
title: Flytta programautentisering från AD FS till Azure Active Directory
description: Den här artikeln är avsedd att hjälpa organisationer att förstå hur du flyttar program till Azure AD, med fokus på federerade SaaS-program.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892124"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Flytta programautentisering från Active Directory Federation Services till Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) erbjuder en universell identitetsplattform som ger dina personer, partner och kunder en enda identitet för att komma åt program och samarbeta från alla plattformar och enheter. Azure AD har en [komplett uppsättning funktioner för identitetshantering](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Genom att standardisera autentiseringen och auktoriseringen av ditt program (app) till Azure AD aktiveras de fördelar som dessa funktioner ger. 

> [!NOTE]
> Den här artikeln fokuserar på att flytta programautentisering från lokala Active Directory- och Active Directory Federation Services till Azure AD. Se faktabladet [Migrera programautentisering till Azure AD](https://aka.ms/migrateapps/whitepaper) för en översikt över planeringen av den här flytten. I vitboken beskrivs hur du planerar migrering, testning och insikter.

## <a name="introduction"></a>Introduktion

Om du har en lokal katalog som innehåller användarkonton har du förmodligen många program som användare autentiserar till. Var och en av dessa appar är konfigurerad för användare att komma åt med hjälp av sina identiteter. 


Användare kan också autentisera direkt med din lokala Active Directory. Ad FS (Active Directory Federation Services) är en standardbaserad identitetstjänst för lokala enheter. AD FS utökar möjligheten att använda SSO-funktioner (Single Sign-on) mellan betrodda affärspartners utan att användarna behöver logga in separat på varje program. Detta kallas Federation.

Många organisationer har SaaS-appar (Software as a Service) eller anpassade LOB-appar (Line-of-Business) som är direkt tillatt till AD FS, tillsammans med Office 365- och Azure AD-baserade appar. 

![Program som är direkt anslutna lokalt](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**För att öka programsäkerheten är ditt mål att ha en enda uppsättning åtkomstkontroller och principer i lokala miljöer och molnmiljöer.** 

![Program som är anslutna via Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Typer av appar som ska migreras

Att migrera all programautentisering till Azure AD är optimalt, eftersom det ger dig ett enda kontrollplan för identitets- och åtkomsthantering.

Dina program kan använda moderna eller äldre protokoll för autentisering. Överväg att först migrera program som använder moderna autentiseringsprotokoll (till exempel SAML och Open ID Connect). Dessa appar kan konfigureras om för att autentisera med Azure AD via antingen en inbyggd anslutningsapp i vårt Appgalleri eller genom att registrera programmet i Azure AD. Appar som använder äldre protokoll kan integreras med [Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy). 

Mer information finns i [Vilka typer av program kan jag integrera med Azure AD?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)

Du kan använda [AD FS-programaktivitetsrapporten för att migrera program till Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) om du har Azure AD Connect Health [aktiverat](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs). 

### <a name="the-migration-process"></a>Migreringsprocessen

Under processen att flytta appautentiseringen till Azure AD testar du dina appar och konfiguration på lämpligt sätt. Vi rekommenderar att du fortsätter att använda befintliga testmiljöer för migreringstestning som flyttar till produktionsmiljön. Om en testmiljö inte är tillgänglig för tillfället kan du konfigurera en med [Azure App Service](https://azure.microsoft.com/services/app-service/) eller Azure Virtual [Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), beroende på programmets arkitektur.

Du kan välja att konfigurera en separat test Azure AD-klient som ska användas när du utvecklar dina appkonfigurationer. 

Migreringsprocessen kan se ut så här:

**Steg 1 – Aktuellt tillstånd: Produktionsapp som autentiserar med AD FS**

![Flyttningssteg 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**Steg 2 – VALFRITT: Testinstans av app som pekar på att testa Azure-klient**

Uppdatera konfigurationen för att peka din testinstans av appen till en test Azure AD-klientorganisation och gör nödvändiga ändringar. Appen kan testas med användare i test azure AD-klienten. Under utvecklingsprocessen kan du använda verktyg som [Fiddler](https://www.telerik.com/fiddler) för att jämföra och verifiera förfrågningar och svar.

Om det inte är möjligt att konfigurera en separat testklient, hoppa över det här steget och ställ upp en testinstans av en app och peka den på din produktion Azure AD-klient som beskrivs i steg 3 nedan.

![Flyttningssteg 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Steg 3 – Testapp som pekar på produktion av Azure-klientorganisation**

Uppdatera konfigurationen för att peka din testinstans av appen till din produktionsinstans av Azure. Du kan nu testa med användare i din produktionsinstans. Om det behövs granska avsnittet i den här artikeln om övergångsanvändare.

![Flyttningssteg 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Steg 4 – Produktionsapp som pekar på produktion AD-klient**

Uppdatera konfigurationen av ditt produktionsprogram så att den pekar på din produktion Azure-klientorganisation.

![Flyttningssteg 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Appar som autentiserar med AD FS kan använda Active Directory-grupper för behörigheter. Använd [Azure AD Connect-synkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) för att synkronisera identitetsdata mellan din lokala miljö och Azure AD innan du påbörjar migreringen. Verifiera dessa grupper och medlemskap före migreringen så att du kan bevilja åtkomst till samma användare när programmet migreras.

### <a name="line-of-business-lob-apps"></a>LOB-appar (Line of business)

LOB-appar utvecklas internt av din organisation eller är tillgängliga som en standardpaketerad produkt som är installerad i ditt datacenter. Exempel är appar som bygger på Windows Identity Foundation- och SharePoint-appar (inte SharePoint Online). 

LOB-appar som använder OAuth 2.0, OpenID Connect eller WS-Federation kan integreras med Azure AD som [appregistreringar](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). Integrera anpassade appar som använder SAML 2.0 eller WS-Federation som [icke-galleriprogram](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) på företagsprogramsidan i [Azure-portalen](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>SAML-baserade enkel sign-On

Appar som använder SAML 2.0 för autentisering kan konfigureras för [SAML-baserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SAML-baserad SSO). Med [SAML-baserade SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)kan du mappa användare till specifika programroller baserat på regler som du definierar i dina SAML-anspråk. 

Mer om du vill konfigurera ett SaaS-program för SAML-baserad enkel inloggning finns i [Konfigurera SAML-baserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications). 

![SSO SAML Användare Skärmdumpar ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
Många SaaS-program har en [programspecifik självstudiekurs](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) som hjälper dig att gå igenom konfigurationen för SAML-baserad enkel inloggning.

![app självstudiekurs](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Vissa appar är enkla att migrera. Andra appar med mer komplexa krav, som t.ex. anpassade anspråk, kan kräva ytterligare konfiguration i Azure AD och/eller Azure AD Connect. Information om anspråksmappningar som stöds finns [i Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

Tänk på följande begränsningar vid mappning av attribut:

* Alla attribut som kan utfärdas i AD FS visas inte i Azure AD som attribut för att släppa ut till SAML-token, även om dessa attribut synkroniseras. När du redigerar attributet visar listrutan Värde de olika attribut som är tillgängliga i Azure AD. Kontrollera [Azure AD Connect-synkroniseringskonfigurationen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) för att säkerställa att ett obligatoriskt attribut – till exempel samAccountName – synkroniseras med Azure AD. Du kan använda tilläggsattributen för att avge alla anspråk som inte ingår i standardanvändarschemat i Azure AD.

* I de vanligaste scenarierna behöver endast NameID-anspråk och andra vanliga anspråk för användaridentifierare anges för en app. Ta reda på om ytterligare anspråk krävs genom att undersöka vilka anspråk du utfärdar från AD FS.

* Alla anspråk kan inte vara problem eftersom vissa anspråk är skyddade i Azure AD. 

* Möjligheten att använda krypterade SAML-token är nu i förhandsversion. Se [Så här anpassar du anspråk som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

 

### <a name="software-as-a-service-saas-apps"></a>SaaS-appar (Software as a Service)

Om användaren loggar in på SaaS-appar som Salesforce, ServiceNow eller Workday och är integrerade med AD FS använder du federerad inloggning för SaaS-appar. 

De flesta SaaS-program kan redan konfigureras i Azure AD. Microsoft har många förkonfigurerade anslutningar till SaaS-appar i [Azure AD-appgalleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), vilket gör övergången enklare. SAML 2.0-program kan integreras med Azure AD via Azure AD-appgalleriet eller som program som [inte är galleri.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

Appar som använder OAuth 2.0 eller OpenID Connect kan integreras med Azure AD på samma sätt som [appregistreringar](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). Appar som använder äldre protokoll kan använda [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) för att autentisera med Azure AD.

Om du har problem med att onboarding dina SaaS-appar kan du kontakta [supportaliaset för SaaS-programintegrering](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**SAML-signeringscertifikat för SSO**: Signeringscertifikat är en viktig del av alla SSO-distributioner. Azure AD skapar signeringscertifikat för att upprätta SAML-baserad federerad SSO till dina SaaS-program. När du har lagt till program för galleri eller icke-galleri konfigurerar du det tillagda programmet med alternativet Federerad SSO. Se [Hantera certifikat för federerade enkel inloggning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Appar och konfigurationer som kan flyttas idag

Appar som du enkelt kan flytta i dag inkluderar SAML 2.0-appar som använder standarduppsättningen konfigurationselement och anspråk:

* User Principal Name

* E-postadress

* Tilltalsnamn

* Efternamn

* Alternativa attribut som SAML **NameID**, inklusive e-postattribut för Azure AD, e-postprefix, anställnings-ID, tilläggsattribut 1–15 eller lokalt **SamAccountName**-attribut. Mer information finns i [Redigera NameIdentifier-anspråket](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

* Anpassade anspråk.

Följande kräver ytterligare konfigurationssteg för att migrera till Azure AD:

* MFA-regler (Custom authorization or Multi-Factor Authentication) i AD FS. Du konfigurerar dem med hjälp av [Azure AD Conditional Access-funktionen.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

* Appar med flera slutpunkter för svars-URL. Du konfigurerar dem i Azure AD med hjälp av PowerShell eller i Azure-portalgränssnittet.

* WS-Federation-appar, till exempel SharePoint-appar som kräver SAML version 1.1-tokens. Du kan konfigurera dem manuellt med PowerShell. Du kan också lägga till en förintegrerad allmän mall för SharePoint- och SAML 1.1-program från galleriet. Vi stöder SAML 2.0-protokollet.

* Komplexa anspråksutgivningstransformerar regler. Information om anspråksmappningar som stöds finns i:
   *  [Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Appar och konfigurationer som inte stöds i Azure AD i nuläget

Appar som kräver följande funktioner kan inte migreras idag.

**Protokollfunktioner**

* Stöd för WS-Trust ActAs-mönstret

* SAML-artefaktmatchning

* Signaturverifiering av undertecknade SAML-begäranden  
Observera att signerade begäranden accepteras, men signaturen verifieras inte.  
Med tanke på att Azure AD endast returnerar token till slutpunkter som är förkonfigurerade i programmet, krävs signaturverifiering sannolikt inte i de flesta fall.

**Anspråk i tokenfunktioner**

* Anspråk från andra attributarkiv än Azure AD-katalogen, såvida inte dessa data synkroniseras med Azure AD. Mer information finns i [azure AD-synkroniserings-API-översikten](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Utfärdande av attribut med flera värden i katalogen. Vi kan till exempel inte utfärda ett flervärdesanspråk för proxyadresser just nu.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mappa appinställningar från AD FS till Azure AD

Migreringen börjar med att utvärdera hur programmet är konfigurerat lokalt och mappar konfigurationen till Azure AD. AD FS och Azure AD fungerar på samma sätt, så begreppen att konfigurera förtroende, inloggnings- och ut logga ut webbadresser och identifierare gäller i båda fallen. Dokumentera AD FS-konfigurationsinställningarna för dina program så att du enkelt kan konfigurera dem i Azure AD.

### <a name="map-app-configuration-settings"></a>Inställningar för mappningsapp

I följande tabell beskrivs några av de vanligaste mappningen av inställningar mellan ett AD FS Relying Party Trust to Azure AD Enterprise Application:

* AD FS – Hitta inställningen i AD FS Relying Party Trust för appen. Högerklicka på den förlitande parten och välj Egenskaper. 

* Azure AD – Inställningen är konfigurerad i [Azure-portalen](https://portal.azure.com/) i varje programs enskilda inloggningsegenskaper.

| Konfigurationsuppsättning| AD FS| Konfigurera i Azure AD| SAML-token |
| - | - | - | - |
| **Inloggnings-URL för appen** <p>URL:en för användaren att logga in på appen i ett SP-initierat SAML-flöde (Service Provider).| Ej tillämpligt| Öppna grundläggande SAML-konfiguration från SAML-baserad inloggning| Ej tillämpligt |
| **Appens svars-URL** <p>URL:en för appen från identitetsleverantörens (IdP:s) perspektiv. IdP skickar användaren och token här efter att användaren har loggat in på IdP.  Detta kallas även **FÖR KONSUMENT-slutpunkt för KONSUMENTER FÖR SAML-påstående**.| Välj fliken **Slutpunkter**| Öppna grundläggande SAML-konfiguration från SAML-baserad inloggning| Målelementet i SAML-token. Exempelvärde:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **Appens webbadress för utloggning** <p>Det här är webbadressen som "sign-out cleanup"-begäranden skickas till när en användare loggar ut från en app. IdP skickar begäran om att logga ut användaren från alla andra appar också.| Välj fliken **Slutpunkter**| Öppna grundläggande SAML-konfiguration från SAML-baserad inloggning| Ej tillämpligt |
| **Appidentifierare** <p>Det här är appidentifieraren från IdP:s perspektiv. Url-värdet för inloggning används ofta för identifieraren (men inte alltid).  Ibland app kallar detta "entitets-ID."| Välj fliken **Identifierare**|Öppna grundläggande SAML-konfiguration från SAML-baserad inloggning| Mappar till **målgruppselementet** i SAML-token. |
| **Federationsmetadata för appen** <p>Det här är platsen för appens federationsmetadata. IdP:n använder den till att automatiskt uppdatera specifika konfigurationsinställningar, som t.ex. slutpunkter eller krypteringscertifikat.| Välj fliken **Övervakning**| Ej tillämpligt. Azure AD stöder inte att använda metadata för programfederation direkt. Du kan importera federationsmetadata manuellt.| Ej tillämpligt |
| **Id för användaridentifierare/namn** <p>Attribut som används för att unikt ange användarens identitet från Azure AD eller AD FS till din app.  Det här attributet är vanligtvis antingen UPN eller användarens e-postadress.| Regler för anspråk. I de flesta fall utfärdar anspråksregeln ett anspråk med en typ som slutar med NameIdentifier.| Du hittar identifieraren under rubriken **Användarattribut och anspråk**. Som standard används UPN| Mappar till **NameID-elementet** i SAML-token. |
| **Övriga fordringar** <p>Exempel på annan anspråksinformation som vanligtvis skickas från IdP till appen är förnamn, efternamn, e-postadress och gruppmedlemskap.| I AD FS finns detta som övriga anspråksregler hos den förlitande parten.| Du hittar identifieraren under rubriken **Användarattribut & anspråk**. Välj **Visa** och redigera alla andra användarattribut.| Ej tillämpligt |


### <a name="map-identity-provider-idp-settings"></a>Inställningar för Mappidentitetsprovider (IdP)

Konfigurera dina program så att de pekar på Azure AD jämfört med AD FS för SSO. Här fokuserar vi på SaaS-appar som använder SAML-protokollet. Det här konceptet omfattar dock även anpassade LOB-appar.

> [!NOTE]
> Konfigurationsvärdena för Azure AD följer mönstret där ditt Azure-klient-ID ersätter {tenant-id} och program-ID ersätter {application-id}. Du hittar den här informationen i [Azure-portalen](https://portal.azure.com/) under Azure Active Directory > Properties: 

* Välj Katalog-ID för att se ditt klient-ID. 

* Välj Program-ID för att visa ditt program-ID.

 På en hög nivå mappar du följande viktiga Konfigurationselement för SaaS-appar till Azure AD. 

 

| Element| Konfigurationsvärde |
| - | - |
| Utfärdare av identitetsprovider| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| Url till inloggningsadress för identitetsprovider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| URL för inloggning av identitetsprovider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Plats för federationsmetadata| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>Kartlägger SSO-inställningar för SaaS-appar

SaaS-appar måste veta var autentiseringsbegäranden ska skickas och hur du validerar de mottagna token. I följande tabell beskrivs elementen för att konfigurera SSO-inställningar i appen och deras värden eller platser i AD FS och Azure AD

| Konfigurationsuppsättning| AD FS| Konfigurera i Azure AD |
| - | - | - |
| **URL för idP-inloggning** <p>Inloggningsadress för IdP från appens perspektiv (där användaren omdirigeras för inloggning).| AD FS-inloggnings-URL:en är AD FS-federationstjänstnamnet följt av "/adfs/ls/.". <p>Till exempel:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| Ersätt {tenant-id} med ditt klient-ID. <p> För appar som använder SAML-P-protokollet:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>För appar som använder WS-Federation-protokollet:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **URL för ut logga ut-IdP**<p>Utloggnings-URL för IdP från appens perspektiv (där användaren omdirigeras när de väljer att logga ut från appen).| Ut logga ut-URL:en är antingen samma som inloggnings-URL:en eller samma WEBBADRESS med "wa=wsignout1.0" tillalat. Till exempel:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| Ersätt {tenant-id} med ditt klient-ID.<p>För appar som använder SAML-P-protokollet:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> För appar som använder WS-Federation-protokollet:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certifikat för signering av token**<p>IdP använder den privata nyckeln för certifikatet för att signera utfärdade token. Den kontrollerar att token kom från samma IdP som appen är konfigurerad att ha förtroende för.| AD FS-certifikatet för tokensignering finns i AD FS-hanteringen under **Certifikat**.| Hitta den i **Azure-portalen** i programmets egenskaper för enkel inloggning under huvudet **SAML Signeringscertifikat**. Därifrån kan du ladda ner certifikatet för uppladdning till appen.  <p>Om programmet har mer än ett certifikat kan du hitta alla certifikat i XML-filen för federationsmetadata. |
| **Identifierare/ "emittent"**<p>Identifierare för IdP från appens perspektiv (kallas ibland "utfärdar-ID").<p>I SAML-token visas värdet som Utfärdarelementet.| Identifieraren för AD FS är vanligtvis federationstjänstidentifieraren i AD FS-hantering under **Service > Redigera federationstjänstegenskaper**. Till exempel:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| Ersätt {tenant-id} med ditt klient-ID.<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **Metadata för IdP-federation**<p>Plats för IdP:s allmänt tillgängliga federationsmetadata. (Federationsmetadata används av vissa appar som ett alternativ för administratören och konfigurerar URL:er, identifierare och certifikat för tokensignering individuellt.)| Hitta URL:en för AD FS-federationsmetadata i AD FS-hantering under **Service >-slutpunkter > metadata > typ: Federationmetadata**. Till exempel:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| Motsvarande värde för Azure AD [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)följer mönstret . Ersätt {TenantDomainName} med klientens namn i formatet "contoso.onmicrosoft.com".   <p>Mer information finns i [Federationsmetadata](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Representera AD FS-säkerhetsprinciper i Azure AD

När du flyttar appautentiseringen till Azure AD skapar du mappningar från befintliga säkerhetsprinciper till motsvarande eller alternativa varianter som är tillgängliga i Azure AD. Om du ser till att dessa mappningar kan göras samtidigt som de uppfyller de säkerhetsstandarder som krävs av dina appägare blir resten av appmigreringen betydligt enklare.

För varje regeltyp och dess exempel föreslår vi här hur regeln ser ut i AD FS, AD FS-regelspråksekvivalentkoden och hur den här kartan i Azure AD.

### <a name="map-authorization-rules"></a>Regler för karttillstånd

Följande är exempel på typer av auktoriseringsregler i AD FS och hur du kan mappa dem till Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Exempel 1: Tillåt åtkomst till alla användare

Tillåt åtkomst till alla användare ser ut i AD FS: 

![Flyttningssteg 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Detta mappar till Azure AD på något av följande sätt:

I [Azure-portalen:](https://portal.azure.com/)
* Alternativ 1: Ange användartilldelning som krävs för nej ![redigera åtkomstkontrollprincip för SaaS-appar ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Observera att om du anger den användartilldelning som krävs växlar du till Ja kräver att användarna tilldelas programmet för att få åtkomst. När den är inställd på Nej har alla användare åtkomst. Den här växeln styr inte vad som visas för användare i upplevelsen Mina appar. 

 
* Alternativ 2: På fliken Användare och grupper tilldelar du programmet till den automatiska gruppen "Alla användare". <p>
Du måste [aktivera dynamiska grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) i din Azure AD-klient för att standardgruppen Alla användare ska vara tillgänglig.

   ![Mina SaaS-appar i Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Exempel 2: Tillåt en grupp uttryckligen

Uttrycklig gruppauktorisering i AD FS:


![regler för utfärdande av auktorisering ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


Så här mappar regeln till Azure AD:

I [Azure-portalen](https://portal.azure.com/)skapar du först [en användargrupp](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) som motsvarar gruppen användare från AD FS och sedan tilldelar appbehörigheter till den gruppen:

![Lägg till tilldelning ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Exempel 3: Auktorisera en viss användare

Uttryckligt användarauktorisering i AD FS:

![regler för utfärdande av auktorisering ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Så här mappar regeln till Azure AD:

Lägg [Azure portal](https://portal.azure.com/)till en användare i appen via fliken Lägg till tilldelning i appen enligt nedan:

![Mina SaaS-appar i Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Kartregler för multifaktorautentisering 

En lokal distribution av [MFA (MultiFaktor Authentication)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) och AD FS fungerar fortfarande efter migreringen eftersom du är federerad med AD FS. Överväg dock att migrera till Azures inbyggda MFA-funktioner som är kopplade till Azure AD:s arbetsflöden för villkorlig åtkomst. 

Följande är exempel på typer av MFA-regler i AD FS och hur du kan mappa dem till Azure AD baserat på olika villkor:

MFA-regelinställningar i AD FS:

![Azure AD MFA-inställningar](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Exempel 1: Framtvinga MFA baserat på användare/grupper

Väljaren För användare/grupper är en regel som gör att du kan tillämpa MFA per grupp (Grupp SID) eller per användare (Primär SID). Förutom tilldelningarna användare/grupper fungerar alla ytterligare kryssrutor i AD FS MFA-konfigurationsgränssnittet som ytterligare regler som utvärderas när användar-/gruppregeln har tillämpats. 


Ange MFA-regler för en användare eller en grupp i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Välj **Tilldelningar**. Lägg till de användare eller grupper som du vill framtvinga MFA på.

3. Konfigurera alternativen **för Access-kontroller** enligt nedan:  
‎

![AAD MFA-inställningar](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Exempel 2: Framtvinga MFA för oregistrerade enheter

Ange MFA-regler för oregistrerade enheter i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Ange **tilldelningarna** till **alla användare**.

3. Konfigurera alternativen **för Access-kontroller** enligt nedan:  
‎

![AAD MFA-inställningar](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
När du ställer in alternativet För flera kontroller på Kräv en av de markerade kontrollerna betyder det att om något av villkoren som anges i kryssrutan uppfylls av användaren, kommer de att beviljas åtkomst till din app.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Exempel 3: Framtvinga MFA baserat på plats

Ange MFA-regler baserat på en användares plats i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

1. Ange **tilldelningarna** till **alla användare**.

1. [Konfigurera namngivna platser i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) annars är federationen inifrån företagets nätverk betrodd. 

1. Konfigurera **villkoren för** att ange de platser som du vill tillämpa MFA för.

![Azure AD MFA-inställningar](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Konfigurera alternativen **för Access-kontroller** enligt nedan:


![Principer för kontroll av mappning](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>Kartreamulitattribut som anspråksregel

Här är ett exempel på hur attribut mappas i AD FS:

![Azure AD MFA-inställningar](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


Så här mappar regeln till Azure AD:

I [Azure-portalen](https://portal.azure.com/)väljer du **Enterprise Applications**, Enkel inloggning och lägger till **SAML-tokenattribut** enligt nedan: **Single sign-on**

![Azure AD MFA-inställningar](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Kartlägger inbyggda principer för åtkomstkontroll

AD FS 2016 har flera inbyggda åtkomstkontrollprinciper som du kan välja mellan:

![Azure AD inbyggd åtkomstkontroll](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Om du vill implementera inbyggda principer i Azure AD kan du använda en [ny princip för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) och konfigurera åtkomstkontrollerna, eller så kan du använda den anpassade principdesignern i AD FS 2016 för att konfigurera principer för åtkomstkontroll. Regelredigeraren har en uttömmande lista över alternativ för tillstånd och undantag som kan hjälpa dig att göra alla typer av permutationer.

![Azure AD-principer för åtkomstkontroll](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



I den här tabellen har vi listat några användbara alternativ för tillstånd och undantag och hur de mappas till Azure AD. 


| | Hur konfigurerar du alternativet Tillåt i Azure AD?| Hur konfigurerar alternativ Förutom i Azure AD? |
| - | - | - |
| Från specifika nätverk| Kartor till [namngiven plats](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) i Azure AD| Använda alternativet **Uteslut** för [betrodda platser](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| Från specifika grupper| [Ange tilldelning av användare/grupper](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| Använda alternativet **Uteslut** i Användare och grupper |
| Från enheter med specifik förtroendenivå| Ange detta från kontrollen Enhetstillstånd under Tilldelningar -> villkor| Använd alternativet **Uteslut** under Enhetstillståndsvillkor och Inkludera **alla enheter** |
| Med specifika anspråk i begäran| Den här inställningen kan inte migreras| Den här inställningen kan inte migreras |


Ett exempel på hur du konfigurerar alternativet Uteslut för betrodda platser i Azure Portal:

![Skärmbild av principer för mappning av åtkomstkontroll](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Överföra användare från AD FS till Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synkronisera AD FS-grupper i Azure AD

När du mappar auktoriseringsregler kan appar som autentiseras med AD FS använda Active Directory-grupper för behörigheter. I så fall kan du använda [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) för att synkronisera dessa grupper med Azure AD innan du migrerar programmen. Kontrollera att du verifierar dessa grupper och medlemskap före migreringen så att du kan bevilja åtkomst till samma användare när programmet migreras.

Mer information finns i [Förutsättningar för användning av gruppattribut som synkroniserats från Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims).

### <a name="setup-user-self-provisioning"></a>Självetablering av konfigurerar användare 

Vissa SaaS-program stöder möjligheten att självetablera användare när de loggar in på programmet för första gången. I Azure Active Directory (Azure AD) avser termen appetablering att automatiskt skapa användaridentiteter och roller i molnet[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)program som användarna behöver åtkomst till. Användare som migreras har redan ett konto i SaaS-programmet. Alla nya användare som läggs till efter migreringen måste etableras. Testa [SaaS-apptablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) när programmet har migrerats.

### <a name="sync-external-users-in-azure-ad"></a>Synkronisera externa användare i Azure AD

Dina befintliga externa användare kan konfigureras på två huvudsätt inom AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Externa användare med ett lokalt konto inom organisationen

Du kommer även i fortsättningen att kunna använda dessa konton på samma sätt som dina interna användarkonton fungerar. Dessa externa användarkonton har ett principnamn inom organisationen, även om kontots e-post kan peka externt. När du går vidare med migreringen kan du dra nytta av de fördelar som [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) erbjuder genom att migrera dessa användare för att använda sin egen företagsidentitet när en sådan identitet är tillgänglig. Detta effektiviserar inloggningsprocessen för dessa användare, eftersom de ofta är inloggade med sin egen företagsinloggning. Organisationens administration kommer också att underlättas genom att du inte längre behöver hantera konton för externa användare.

#### <a name="federated-external-identities"></a>Federerade externa identiteter

Om du för närvarande matas med en extern organisation har du några sätt att ta:

* [Lägg till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). Du kan proaktivt skicka B2B-samarbetsanbjudningar från Azure AD-administratörsportalen till partnerorganisationen för enskilda medlemmar att fortsätta använda de appar och resurser som de är vana vid. 

* [Skapa ett självbetjäningsarbetsflöde för B2B-registrering](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) som genererar en begäran för enskilda användare i din partnerorganisation med hjälp av B2B-inbjudnings-API:et.

Oavsett hur dina befintliga externa användare är konfigurerade har de troligen behörigheter som är associerade med deras konto, antingen i gruppmedlemskap eller specifika behörigheter. Utvärdera om dessa behörigheter behöver migreras eller rensas. Konton inom organisationen som representerar en extern användare måste inaktiveras när användaren har migrerats till en extern identitet. Migreringsprocessen bör diskuteras med dina affärspartners, eftersom det kan finnas ett avbrott i deras förmåga att ansluta till dina resurser.

## <a name="migrate-and-test-your-apps"></a>Migrera och testa dina appar

Följ migreringsprocessen som beskrivs i den här artikeln.

Gå sedan till [Azure-portalen](https://aad.portal.azure.com/) för att testa om migreringen lyckades. Följ instruktionerna nedan:
1. Välj **Företagsprogram** > **Alla program** och hitta din app i listan.

1. Välj **Hantera** > **användare och grupper** om du vill tilldela minst en användare eller grupp till appen.

1. Välj **Hantera** > **villkorlig åtkomst**. Granska listan med principer och se till att du inte blockerar åtkomsten till programmet med en [princip för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

Kontrollera att SSO fungerar korrekt beroende på hur du konfigurerar appen. 

| Autentiseringstyp| Testning |
| - | - |
| OAuth / OpenID Connect| Välj **Enterprise-program > behörigheter** och se till att du har samtyckt till det program som ska användas i organisationen i användarinställningarna för din app.  
‎ |
| SAML-baserad SSO| Använd knappen [Testa SAML-inställningar](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) som finns under **Enkel inloggning**.  
‎ |
| Lösenordsbaserad SSO| Ladda ner och installera [MyApps Secure Sign](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[in Extension](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction). Det här tillägget hjälper dig att starta någon av organisationens molnappar som kräver att du använder en SSO-process.  
‎ |
| Programproxy| Kontrollera att kontakten körs och tilldelas ditt program. Besök [felsökningsguiden](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) [ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)för programproxy för ytterligare hjälp.  
‎ |

> [!NOTE]
> Cookies från den gamla AD FS-miljön är fortfarande beständiga på användarens datorer. Dessa cookies kan orsaka problem med migreringen eftersom användare kan dirigeras till den gamla AD FS-inloggningsmiljön jämfört med den nya Azure AD-inloggningen. Du kan behöva rensa cookies i användarbläddraren manuellt eller använda ett skript. Du kan också använda System Center Configuration Manager eller en liknande plattform.

### <a name="troubleshoot"></a>Felsöka

Om det finns några fel från testet av de migrerade programmen kan felsökning vara det första steget innan du går tillbaka till de befintliga AD FS-förlitande parterna. Se [Så här felsöker du SAML-baserad enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues).

### <a name="rollback-migration"></a>Migrering för återställning

Om migreringen misslyckas rekommenderar vi att du lämnar de befintliga förlitande parterna på AD FS-servrarna och tar bort åtkomsten till de förlitande parterna. Detta möjliggör en snabb återgång om det behövs under distributionen.

### <a name="end-user-communication"></a>Kommunikation mellan slutanvändare

Även om själva det planerade utmatningsfönstret kan vara minimalt, bör du ändå planera att kommunicera dessa tidsramar proaktivt till anställda samtidigt som du gör nedskärningen från AD FS till Azure AD. Se till att din appupplevelse har en feedbackknapp eller pekare till din helpdesk för problem.

När distributionen är klar kan du skicka kommunikation som informerar användarna om den lyckade distributionen och påminner dem om alla nya steg som de behöver vidta.

* Instruera användarna att använda [åtkomstpanelen](https://myapps.microsoft.com.com/) för att komma åt alla migrerade program. 

* Påminn användarna om att de kan behöva uppdatera sina MFA-inställningar. 

* Om Återställning av lösenord för självbetjäning distribueras kan användare behöva uppdatera eller verifiera sina autentiseringsmetoder. Se [MFA-](https://aka.ms/mfatemplates) och [SSPR-kommunikationsmallar](https://aka.ms/ssprtemplates) för slutanvändare.

Kommunikation till externa användare: Den här gruppen användare är oftast den mest kritiskt påverkade i händelse av problem. Detta gäller särskilt om din säkerhetsposition dikterar en annan uppsättning regler för villkorlig åtkomst eller riskprofiler för externa partner. Se till att externa partner är medvetna om molnet migreringsschema och har en tidsram under vilken de uppmuntras att delta i en pilotdistribution som testar alla flöden som är unika för externt samarbete. Slutligen, se till att de har ett sätt att komma åt din helpdesk i händelse av att bryta problem.

## <a name="next-steps"></a>Efterföljande moment
Läsa [Migrera programautentisering till Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
Ställ in [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) och [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
