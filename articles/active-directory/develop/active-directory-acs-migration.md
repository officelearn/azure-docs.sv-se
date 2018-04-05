---
title: Migrera från tjänsten Azure Access Control | Microsoft Docs
description: Alternativ för att flytta appar och tjänster från Azure Access Control service
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: 6c22f85d3e76a005c45a4679ddfd8948a46acffc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="migrate-from-the-azure-access-control-service"></a>Migrera från tjänsten Azure Access Control

Azure Access Control, en tjänst av Azure Active Directory (Azure AD), kommer tas bort på 7 November 2018. Program och tjänster som använder åtkomstkontroll måste fullständigt flyttas till en annan autentiseringsmekanism innan dess. Den här artikeln beskriver rekommendationer för aktuella kunder som du planerar att inaktualisera din användning av åtkomstkontroll. Om du inte använder åtkomstkontroll, behöver du inte vidta några åtgärder.


## <a name="overview"></a>Översikt

Åtkomstkontroll är en molnbaserad autentiseringstjänst som erbjuder ett enkelt sätt att autentisera och auktorisera användare för åtkomst till ditt webbprogram och tjänster. Det gör att många funktioner för autentisering och auktorisering vägas ut från din kod. Åtkomstkontroll används främst av utvecklare och arkitekter av Microsoft .NET-klienter, ASP.NET-webbprogram och webbtjänster för Windows Communication Foundation (WCF).

Användningsområden för åtkomstkontroll kan delas upp i tre huvudkategorier:

- Autentisering till vissa Microsoft-molntjänster, inklusive Azure Service Bus och Dynamics CRM. Klientprogram hämta token från åtkomstkontroll för att autentisera till dessa tjänster för att utföra olika åtgärder.
- Att lägga till autentisering till webbprogram, både anpassade och färdigförpackade (till exempel SharePoint). Med hjälp av åtkomstkontroll ”passiv” autentisering stöder webbprogram logga in med ett Microsoft-konto (tidigare Live ID) och konton från Google, Facebook, Yahoo, Azure AD och Active Directory Federation Services (AD FS).
- Att säkra anpassade webbtjänster med token som utfärdats av åtkomstkontroll. Med hjälp av ”aktiv” autentisering webbtjänster se till att de tillåter endast åtkomst till kända klienter som har autentiserats med åtkomstkontroll.

Var och en av dessa använda fall och rekommenderade migreringen strategier beskrivs i följande avsnitt. 

> [!WARNING]
> I de flesta fall krävs betydande kodändringar för att migrera befintliga appar och tjänster till nyare teknik. Vi rekommenderar att du omedelbart börja planera och köra migreringen för att undvika eventuella potentiella avbrott eller driftstopp.

Åtkomstkontroll innehåller följande komponenter:

- En säker token-tjänsten (STS) som tar emot begäranden om autentisering och utfärdar säkerhetstoken i RETUR.
- Den klassiska Azure-portalen där du skapa, ta bort, och aktivera och inaktivera åtkomstkontroll namnområden.
- En separat åtkomstkontroll management portal där du kan anpassa och konfigurera åtkomstkontroll namnområden.
- En management-tjänst som du kan använda för att automatisera portalerna funktioner.
- En token regeln omvandlingsmotorn, där du kan definiera komplex logik för att ändra utdataformatet för token som utfärdas för åtkomstkontroll.

Om du vill använda dessa komponenter måste du skapa ett eller flera namnområden för åtkomstkontroll. En *namnområde* är en dedikerad instans av åtkomstkontroll som dina program och tjänster som kommunicerar med. Ett namnområde är isolerad från andra Access Control-kunder. Andra Access Control-kunder att använda sina egna namnområden. Ett namnområde i åtkomstkontroll har en dedikerad URL som ser ut så här:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

All kommunikation med STS och hanteringsåtgärder är klar på denna URL. Du kan använda olika sökvägar för olika ändamål. Ta reda på om dina program eller tjänster använder åtkomstkontroll genom att övervaka för all trafik till https://<namespace>. accesscontrol.windows.net. All trafik till URL: en hanteras av åtkomstkontroll och behöver avbrytas. 

Undantaget till detta är all trafik till `https://accounts.accesscontrol.windows.net`. Trafik till URL: en hanteras redan av en annan tjänst och **är inte** påverkas av utfasningen åtkomstkontroll. 

Mer information om åtkomstkontroll finns [Access Control Service 2.0 (arkiverad)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Tillbakadragning schema

Från och med November 2017 är alla komponenter för åtkomstkontroll fullständigt stöds och fungerar. Den enda begränsningen är att du [kan inte skapa nya åtkomstkontroll namnområden via den klassiska Azure-portalen](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Här följer schemat för att sluta åtkomstkontroll komponenter:

- **November 2017**: Azure AD-administratör som uppstår i den klassiska Azure-portalen [har dragits tillbaka](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Nu hantering av namnområdet för åtkomstkontroll är tillgängligt på en ny, dedikerad URL: `http://manage.windowsazure.com?restoreClassic=true`. Använd denna URl att visa dina befintliga namnområden, aktivera och inaktivera namnområden och ta bort namnområden, om du väljer.
- **Den 2 april 2018**: Azure klassiska portal helt är pensionerad, vilket innebär att hantering av åtkomstkontroll namnområden är inte längre tillgängligt via en URL. Nu kan du inaktivera eller aktivera, ta bort eller räkna upp Access Control-namnområden. Access Control management portal kommer dock att helt funktionella och finns på `https://\<namespace\>.accesscontrol.windows.net`. Alla andra komponenter i åtkomstkontroll fortsätter att fungera normalt.
- **7 november 2018**: alla åtkomstkontroll komponenter permanent är avstängd. Detta inkluderar hanteringsportalen för åtkomstkontroll, management-tjänsten, STS och token regeln omvandlingsmotorn. Nu är alla förfrågningar som skickas till åtkomstkontroll (som finns i \<namnområde\>. accesscontrol.windows.net) misslyckas. Du bör har migrerat alla befintliga appar och tjänster till andra tekniker väl före denna tidpunkt.


## <a name="migration-strategies"></a>Migreringsstrategier för

I följande avsnitt beskrivs utförligt rekommendationer för att migrera från åtkomstkontroll med andra Microsoft-teknikerna.

### <a name="clients-of-microsoft-cloud-services"></a>Klienter för Microsoft-molntjänster

Varje Microsoft-molntjänst som accepterar token som utfärdas av åtkomstkontroll nu stöder minst en alternativ form av autentisering. Rätt autentiseringsmekanism varierar för varje tjänst. Vi rekommenderar att du refererar till specifika dokumentationen för varje tjänst officiella anvisningar. För enkelhetens skull finns varje uppsättning dokumentation här:

| Tjänst | Riktlinjer |
| ------- | -------- |
| Azure Service Bus | [Migrera till signaturer för delad åtkomst](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [Migrera till signaturer för delad åtkomst](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Managed Cache | [Migrera till Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Migrera till kognitiva Services-API: er](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Migrera till funktionen Logic Apps i Azure App Service](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migrera till Azure AD-autentisering](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uppgradera Azure Backup-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="sharepoint-customers"></a>SharePoint-kunder

SharePoint 2013 2016 och SharePoint Online-kunder har länge använt ACS för autentisering i molnet och lokala hybrid scenarier. Vissa funktioner för SharePoint och användningsfall kommer att påverkas av ACS pensionering, medan andra inte kommer. I tabellen nedan sammanfattas vägledning för migrering för några av de mest populära SharePoint funktion som utnyttjar ACS:

| Funktion | Riktlinjer |
| ------- | -------- |
| Autentisering av användare från Azure AD | Tidigare Azure AD stöder inte 1.1 för SAML-token som krävs för SharePoint för autentisering och ACS användes som en mellanhand som SharePoint-kompatibelt med Azure AD-token-format. Nu kan du [SharePoint ansluta direkt till Azure AD med hjälp av token utgivningsprinciper](https://docs.microsoft.com/Office365/Enterprise/using-azure-ad-for-sharepoint-server-authentication). |
| [Appen autentisering & server till server-autentisering i SharePoint på lokalt](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Inte påverkas av ACS pensionering; Inga ändringar behövs. | 
| [Låg förtroende auktorisering för SharePoint-tillägg (värd-providern och SharePoint finns)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Inte påverkas av ACS pensionering; Inga ändringar behövs. |
| [SharePoint molnet hybrid-sökning](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Inte påverkas av ACS pensionering; Inga ändringar behövs. |

### <a name="web-applications-that-use-passive-authentication"></a>Webbprogram som använder passiv autentisering

För webbprogram som använder åtkomstkontroll för autentisering av användare, innehåller Access Control följande funktioner och möjligheter för webbprogram och arkitekter:

- Djupgående integrering med Windows Identity Foundation (WIF).
- Federation med Google, Facebook, Yahoo, Azure Active Directory och AD FS-konton och Microsoft-konton.
- Stöd för följande autentiseringsprotokoll: OAuth 2.0-utkast 13, WS-Trust och Web Services Federation (WS-Federation).
- Stöd för token följande format: JSON-Webbtoken (JWT), SAML 1.1, SAML 2.0 och Simple Web Token (SWT).
- En startsfär identifiering upplevelse, integrerade i WIF, som gör det möjligt för användaren att välja vilken typ av konto de använder för att logga in. Den här upplevelsen finns på webbprogrammet och är helt anpassningsbar.
- Token transformation som tillåter omfattande anpassning av anspråk som tagits emot av webbprogrammet från åtkomstkontroll, inklusive:
    - Släpp igenom anspråk från identitetsleverantörer.
    - Lägger till ytterligare anpassade anspråk.
    - Enkel om sedan logik för att utfärda anspråk vissa villkor.

Det finns tyvärr inte en tjänst som erbjuder alla dessa funktioner som motsvarande. Bör du utvärdera vilka funktioner för åtkomstkontroll du behöver och välj sedan mellan att använda [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) eller en annan autentisering i molnet tjänsten.

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

En sökväg till överväga integrera dina appar och tjänster direkt med Azure AD. Azure AD är molnbaserad identitetsleverantören Microsoft arbets- eller skolkonton. Azure AD är identitetsleverantören för Office 365, Azure och mycket mer. Det ger liknande federerad autentisering med funktioner för åtkomstkontroll, men stöder inte alla funktioner för åtkomstkontroll. 

Det primära exemplet är federation med sociala identitetsleverantörer, till exempel Facebook, Google och Yahoo. Dina användare logga in med dessa typer av autentiseringsuppgifter, är Azure AD inte om lösningen för dig. 

Azure AD stöder inte nödvändigtvis också exakt samma autentiseringsprotokoll som åtkomstkontroll. Till exempel men både åtkomstkontroll och Azure AD stöder OAuth, finns det vissa skillnader mellan varje genomförande. Olika implementeringar måste du ändra koden som en del av en migrering.

Dock tillhandahåller Azure AD flera potentiella fördelar för åtkomstkontroll kunder. Den internt stöder Microsoft arbets- eller skolkonton som finns i molnet, och som ofta används av Access Control-kunder. 

Också kan vara externa Azure AD-klient till en eller flera instanser av lokala Active Directory via AD FS. På så sätt kan din app kan autentisera molnbaserade användare och användare som finns lokalt. Det stöder också WS-Federation-protokollet, vilket gör det relativt enkelt att integrera med ett webbprogram med hjälp av WIF.

I följande tabell jämförs funktionerna för åtkomstkontroll som är relevanta för webbprogram med de funktioner som är tillgängliga i Azure AD. 

På en hög nivå *Azure Active Directory är antagligen det bästa valet för din migrering om du låter användare loggar i endast med sina Microsoft arbets- eller skolkonton*.

| Funktion | Stöd för kontrollen | Azure AD-stöd |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsoft arbets- eller skolkonton | Stöds | Stöds |
| Konton från Windows Server Active Directory och AD FS |-Stöds via federation med en Azure AD-klient <br />-Stöds via direkt federation med AD FS | Stöds bara via federation med en Azure AD-klient | 
| Konton från andra företagssystem identity management |-Möjlig via federation med en Azure AD-klient <br />-Stöds via direkt federation | Möjligt via federation med en Azure AD-klient |
| Microsoft-konton för personligt bruk | Stöds | Via Azure AD v2.0 OAuth-protokollet, men inte via andra protokoll som stöds | 
| Facebook, Google, Yahoo konton | Stöds | Stöds inte helst |
| **Protokoll och SDK-kompatibilitet** | | |
| WIF | Stöds | Stöds, men begränsad instruktioner finns |
| WS-Federation | Stöds | Stöds |
| OAuth 2.0 | Stöd för ett utkast till 13 | Stöd för RFC 6749, i de flesta moderna specifikationen |
| WS-Trust | Stöds | Stöds ej |
| **Token format** | | |
| JWT | Stöds i Beta | Stöds |
| SAML 1.1 | Stöds | Förhandsgranskning |
| SAML 2.0 | Stöds | Stöds |
| SWT | Stöds | Stöds ej |
| **Anpassningar** | | |
| Anpassningsbara startsfär identifiering/konto-plockning UI | Nedladdningsbar kod som kan införlivas i appar | Stöds ej |
| Ladda upp anpassade certifikat för tokensignering | Stöds | Stöds |
| Anpassa anspråk i token |-Släpp igenom inkommande anspråk från identitetsleverantörer<br />-Hämta token från identitetsleverantören som ett anspråk<br />-Utfärda utgående anspråk baserade på värden för inkommande anspråk<br />-Utfärda utgående anspråk med konstanta värden |-Det går inte att passera anspråk från federerad Identitetsproviders<br />-Det går inte att hämta token från identitetsleverantören som ett anspråk<br />-Det går inte att utfärda utgående anspråk baserat på värdena för inkommande anspråk<br />-Kan utfärda utgående anspråk med konstanta värden<br />-Kan utfärda utgående anspråk baserat på Egenskaper för användare som synkroniseras till Azure AD |
| **Automation** | | |
| Automatisera hantering och konfiguration av uppgifter | Stöds via Access Control Management-tjänsten | Stöds via Microsoft Graph och Azure AD Graph API |

Om du väljer att Azure AD är den bästa migreringsvägen för dina program och tjänster, bör du vara medveten om två sätt att integrera din app med Azure AD.

Om du vill använda WS-Federation eller WIF för att integrera med Azure AD, rekommenderar vi metoden som beskrivs i följande [konfigurera federerad enkel inloggning för ett icke-galleriet program](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Artikeln syftar till att konfigurera Azure AD för SAML-baserade enkel inloggning, men fungerar även för att konfigurera WS-Federation. Efter den här metoden kräver en Azure AD Premium-licens. Den här metoden har två fördelar:

- Du får fullständig flexibilitet för anpassning av Azure AD-token. Du kan anpassa de anspråk som utfärdas av Azure AD för att matcha de anspråk som utfärdas av åtkomstkontroll. Detta inkluderar särskilt användar-ID eller namnidentifierare anspråk. Se till att användar-ID utfärdat av Azure AD matchar de utfärdade av åtkomstkontroll för att fortsätta att ta emot konsekvent-ID: N för dina användare när du har ändrat tekniker.
- Du kan konfigurera ett certifikat för tokensignering som är specifika för ditt program och med livslängden som du bestämmer.

<!--

Possible nameIdentifiers from Access Control (via AAD or AD FS):
- AD FS - Whatever AD FS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> Den här metoden kräver en Azure AD Premium-licens. Om du är en åtkomstkontroll kund och du behöver en premium-licens för att konfigurera enkel inloggning på för ett program, kontakta oss. Vi att uppskatta ger utvecklare licenser som du kan använda.

En annan metod är att följa [detta kodexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), vilket ger något annorlunda instruktioner för att konfigurera WS-Federation. Det här kodexemplet använder inte WIF, men i stället ASP.NET 4.5 OWIN mellanprogram. Dock instruktioner för registrering av appen är giltiga för appar som använder WIF och kräver inte en Azure AD Premium-licens. 

Om du väljer den här metoden måste du förstå [signering nyckelförnyelse i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Den här metoden använder globala signeringsnyckel problemet tokens Azure AD. Som standard uppdateras WIF inte automatiskt Signeringsnycklar. När Azure AD roterar dess globala Signeringsnycklar, måste WIF-implementering vara beredd att acceptera ändringar.

Om du kan integrera med Azure AD via OpenID Connect eller OAuth-protokoll, rekommenderar vi då. Vi har en omfattande dokumentation och information om hur du integrerar Azure AD i ditt webbprogram som är tillgängliga i vår [Utvecklarhandbok för Azure AD](https://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrera till Azure Active Directory B2C

Den andra migreringsvägen att tänka på är Azure AD B2C. Azure AD B2C är en molntjänst för autentisering som som åtkomstkontroll gör att utvecklare kan flytta över sina management logik för autentisering och identitet till en molntjänst. Det är en betald tjänst (med kostnadsfria premium nivåer och) som är avsedd för konsumentinriktade program som kan ha upp till miljontals aktiva användare.

Precis som åtkomstkontroll är en av de mest tilltalande funktionerna i Azure AD B2C stöder många olika typer av konton. Med Azure AD B2C kan du logga in användare med hjälp av sitt Microsoft-konto eller Facebook, Google, LinkedIn, GitHub eller Yahoo konton med mera. Azure AD B2C stöder också ”lokala konton” eller användarnamn och lösenord som användaren skapar specifikt för ditt program. Azure AD B2C ger också omfattande utökningsbarhet som du kan använda för att anpassa din inloggning flöden. 

Dock har inte stöd för Azure AD B2C-autentiseringsprotokoll bredd och token formaterar åtkomstkontrollen kunder kan behöva. Du kan också använda Azure AD B2C för att hämta token och fråga för ytterligare information om användaren från identitetsleverantören Microsoft eller på annat sätt.

I följande tabell jämförs funktionerna för åtkomstkontroll som är relevanta för webbprogram med de som är tillgängliga i Azure AD B2C. På en hög nivå *Azure AD B2C är antagligen det rätta valet för din migrering om ditt program är inför konsumenten eller om den har stöd för många olika typer av konton.*

| Funktion | Stöd för kontrollen | Azure AD B2C-support |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsoft arbets- eller skolkonton | Stöds | Stöds via anpassade principer  |
| Konton från Windows Server Active Directory och AD FS | Stöds via direkt federation med AD FS | Stöds via SAML federation med hjälp av anpassade principer |
| Konton från andra företagssystem identity management | Stöds via direkt federation via WS-Federation | Stöds via SAML federation med hjälp av anpassade principer |
| Microsoft-konton för personligt bruk | Stöds | Stöds | 
| Facebook, Google, Yahoo konton | Stöds | Facebook och Google stöds internt, Yahoo stöds via OpenID Connect federation med hjälp av anpassade principer |
| **Protokoll och SDK-kompatibilitet** | | |
| Windows Identity Foundation (WIF) | Stöds | Stöds ej |
| WS-Federation | Stöds | Stöds ej |
| OAuth 2.0 | Stöd för ett utkast till 13 | Stöd för RFC 6749, i de flesta moderna specifikationen |
| WS-Trust | Stöds | Stöds ej |
| **Token format** | | |
| JWT | Stöds i Beta | Stöds |
| SAML 1.1 | Stöds | Stöds ej |
| SAML 2.0 | Stöds | Stöds ej |
| SWT | Stöds | Stöds ej |
| **Anpassningar** | | |
| Anpassningsbara startsfär identifiering/konto-plockning UI | Nedladdningsbar kod som kan införlivas i appar | Helt anpassningsbar UI via anpassade CSS |
| Ladda upp anpassade certifikat för tokensignering | Stöds | Anpassade Signeringsnycklar, inte certifikat stöds via anpassade principer |
| Anpassa anspråk i token |-Släpp igenom inkommande anspråk från identitetsleverantörer<br />-Hämta token från identitetsleverantören som ett anspråk<br />-Utfärda utgående anspråk baserade på värden för inkommande anspråk<br />-Utfärda utgående anspråk med konstanta värden |-Kan passera anspråk från identitetsleverantörer; anpassade principer som krävs för några anspråk<br />-Det går inte att hämta token från identitetsleverantören som ett anspråk<br />-Kan utfärda utgående anspråk baserat på värdena för inkommande anspråk via anpassade principer<br />-Kan utfärda utgående anspråk med konstanta värden via anpassade principer |
| **Automation** | | |
| Automatisera hantering och konfiguration av uppgifter | Stöds via Access Control Management-tjänsten |-Skapande av användare via Azure AD Graph API<br />-Det går inte att skapa B2C-klienter, program eller principer programmässigt |

Om du väljer att Azure AD B2C är den bästa migreringsvägen för dina program och tjänster, börjar du med följande resurser:

- [Azure AD B2C-dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Priser för Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till Ping identitet eller Auth0

I vissa fall kanske du upptäcker att Azure AD och Azure AD B2C inte är tillräckligt för att ersätta åtkomstkontroll i ditt webbprogram utan ändringar av större kod. Några vanliga exempel kan innehålla:

- Webbprogram som använder WIF eller WS-Federation för inloggning med sociala identitetsleverantörer, till exempel Google eller Facebook.
- Webbprogram som utför direkt federering till en enterprise-identitetsleverantör via protokollet WS-Federation.
- Webbprogram som kräver åtkomst-token som utfärdas av en sociala identitetsprovider (till exempel Google eller Facebook) som ett anspråk i de token som utfärdats av åtkomstkontroll.
- Webbprogram med reglerna för omvandling av komplexa token som Azure AD eller Azure AD B2C inte kan återskapa.
- Flera innehavare webbprogram som centralt hantera federering till många olika identitetsleverantörer ACS

I dessa fall kanske du vill bör du migrera ditt webbprogram till en annan molntjänst för autentisering. Vi rekommenderar att utforska alternativen nedan. Var och en av följande alternativ ger funktioner som liknar åtkomstkontroll:



|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) är en flexibel identitet molntjänst som har skapats [övergripande migrering vägledning för kunder i åtkomstkontroll](https://auth0.com/acs), och stöder nästan alla funktioner som ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping-identitet](https://www.pingidentity.com) erbjuder två lösningar som liknar ACS. PingOne är en molntjänst identitet som stöder många av samma funktioner som ACS och PingFederate är en produkt med liknande lokal identitet som ger bättre flexibilitet. Referera till [Ping's ACS pensionering vägledning](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) för mer information om hur du använder dessa produkter.  |

Vårt mål i att arbeta med Ping identitets- och Auth0 är att säkerställa att alla åtkomstkontroll kunder har en migreringssökväg för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från åtkomstkontroll.

<!--

## Sharepoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webbtjänster som använder active-autentisering

Access Control erbjuder följande funktioner och möjligheter för webbtjänster som är säkrade med token som utfärdats av åtkomstkontroll:

- Möjligheten att registrera en eller flera *tjänsten identiteter* i namnområdet för åtkomstkontroll. Tjänstidentiteter kan användas för att begära token.
- Stöd för OAuth OMSLUTER och OAuth 2.0 utkast 13 protokoll för att begära token med hjälp av följande typer av autentiseringsuppgifter:
    - Ett enkelt lösenord som har skapats för tjänstidentiteten
    - En signerad SWT med hjälp av en symmetrisk nyckel eller X509 certifikat
    - En SAML-token som utfärdas av en betrodd identitetsleverantör (vanligtvis en instans av AD FS)
- Stöd för token följande format: JWT, SAML 1.1, SAML 2.0 och SWT.
- Enkel token omvandling regler.

Tjänstidentiteter i åtkomstkontroll används vanligtvis för att implementera server till server-autentisering.  

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

Vår rekommendation för den här typen av autentiseringsflödet är att migrera till [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD är molnbaserad identitetsleverantören Microsoft arbets- eller skolkonton. Azure AD är identitetsleverantören för Office 365, Azure och mycket mer. 

Du kan också använda Azure AD för autentisering av server-till-server med hjälp av Azure AD-implementeringen av OAuth-klient autentiseringsuppgifter grant. I följande tabell jämförs funktionerna för åtkomstkontroll i server-till-server-autentisering med de som är tillgängliga i Azure AD.

| Funktion | Stöd för kontrollen | Azure AD-stöd |
| ---------- | ----------- | ---------------- |
| Så här registrerar du en webbtjänst | Skapa en förlitande part i hanteringsportalen för åtkomstkontroll | Skapa en Azure AD-webbapp i Azure-portalen |
| Så här registrerar du en klient | Skapa en tjänstidentitet i hanteringsportalen för åtkomstkontroll | Skapa en annan Azure AD-webbprogram i Azure-portalen |
| Protokoll som används |-Protokollet OAuth OMSLUTNING<br />-Bevilja OAuth 2.0 utkast 13 klientens autentiseringsuppgifter | Bevilja OAuth 2.0 klientens autentiseringsuppgifter |
| Klientautentiseringsmetoder |-Enkla lösenord<br />- Signed SWT<br />-SAML token från en federerad identitet-provider |-Enkla lösenord<br />- Signed JWT |
| Token format |-JWT<br />-SAML 1.1<br />- SAML 2.0<br />-SWT<br /> | Endast JWT |
| Omvandling av token |-Lägg till anpassade anspråk<br />-Enkelt om sedan anspråk utfärdande-logiken | Lägga till anpassade anspråk | 
| Automatisera hantering och konfiguration av uppgifter | Stöds via Access Control Management-tjänsten | Stöds via Microsoft Graph och Azure AD Graph API |

Information om implementering server till server-scenarier finns i följande resurser:

- Tjänst-till-tjänst-avsnitt i den [Utvecklarhandbok för Azure AD](https://aka.ms/aaddev)
- [Daemon kodexemplet genom att använda enkla lösenord klientens autentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Daemon kodexemplet genom att använda certifikat klientens autentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till Ping identitet eller Auth0

I vissa fall kanske du upptäcker att autentiseringsuppgifter för Azure AD-klienten och OAuth ger implementeringen inte är tillräckligt för att ersätta åtkomstkontroll i din arkitektur utan större kodändringar. Några vanliga exempel kan innehålla:

- Server-till-server-autentisering med hjälp av token format än JWTs.
- Server-till-server-autentisering med hjälp av en inkommande token som tillhandahålls av en extern identitetsleverantör.
- Server-till-server-autentisering med reglerna för omvandling av token som Azure AD inte kan återskapa.

I dessa fall kan du migrera ditt webbprogram till en annan molntjänst för autentisering. Vi rekommenderar att utforska alternativen nedan. Var och en av följande alternativ ger funktioner som liknar åtkomstkontroll:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) är en flexibel identitet molntjänst som har skapats [övergripande migrering vägledning för kunder i åtkomstkontroll](https://auth0.com/acs), och stöder nästan alla funktioner som ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping-identitet](https://www.pingidentity.com) erbjuder två lösningar som liknar ACS. PingOne är en molntjänst identitet som stöder många av samma funktioner som ACS och PingFederate är en produkt med liknande lokal identitet som ger bättre flexibilitet. Referera till [Ping's ACS pensionering vägledning](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) för mer information om hur du använder dessa produkter.  |

Vårt mål i att arbeta med Ping identitets- och Auth0 är att säkerställa att alla åtkomstkontroll kunder har en migreringssökväg för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från åtkomstkontroll.

## <a name="questions-concerns-and-feedback"></a>Frågor, problem och feedback

Vi förstår att många åtkomstkontroll kunder inte att hitta en tydlig migreringsväg när du har läst den här artikeln. Du kanske måste vissa hjälp eller riktlinjer för att fastställa rätt plan. Om du vill att diskutera din Migreringsscenarier och frågor, lämna en kommentar på den här sidan.
