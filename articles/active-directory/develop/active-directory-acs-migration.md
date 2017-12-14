---
title: "Migrera från Azure Access Control Service (ACS)"
description: "Alternativ för att flytta appar och tjänster från Azure Access Control Service | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>Migrera från Azure Access Control Service (ACS)

Microsoft Azure Active Directory-åtkomstkontroll (även kallat åtkomstkontrolltjänsten eller ACS) dras i November 2018.  Program och tjänster som för närvarande använder ACS måste fullständigt migrera till en annan autentiseringsmekanism före det här datumet. Det här dokumentet beskriver rekommendationer för kunderna när de planerar att inaktualisera deras användning av ACS. Om du inte använder för närvarande ACS, behöver du inte vidta några åtgärder.


## <a name="brief-acs-overview"></a>Kort ACS-översikt

ACS är en molntjänst för autentisering som ger ett enkelt sätt att autentisera användare att få åtkomst till webbaserade program och tjänster medan många funktioner för autentisering och auktorisering vägas ut från din kod. Det används främst av utvecklare & arkitekter av .NET-klienter, ASP.NET-webbprogram och WCF-webbtjänster.

Användningsområden för ACS kan delas upp i tre huvudkategorier:

- Autentisering till vissa Microsoft-molntjänster, inklusive Azure Service Bus och Dynamics CRM. Klientprogram kan hämta token från ACS som kan användas för att autentisera till dessa tjänster för att utföra olika åtgärder.
- Lägger till autentisering i webbprogram, både sortens egna anpassade & förhand packade sortens (till exempel Sharepoint). ACS ”passiva” autentisering med webbprogram kan ha stöd för inloggning med konton från Google, Facebook, Yahoo, Microsoft-konto (Live ID), Azure Active Directory och AD FS.
- Att säkra anpassade inbyggda webbtjänster med token som utfärdats av ACS. ”Aktiv” autentisering med kan webbtjänster säkerställa att de endast tillåter åtkomst från kända klienter som har autentiserats med ACS.

Var och en av dessa användningsfall och deras rekommenderade övergångsstrategi beskrivs i följande avsnitt. I det stora flertalet av fall krävs betydande kodändringar för att migrera befintliga appar och tjänster till nyare teknik. Vi rekommenderar att du börjar planera & köra migreringen direkt för att undvika eventuella avbrott eller driftstopp.

> [!WARNING]
> I flesta fall krävs betydande kodändringar för att migrera befintliga appar och tjänster till nyare teknik. Vi rekommenderar att du börjar planera & köra migreringen direkt för att undvika eventuella avbrott eller driftstopp.

ACS är arkitektoniskt sett likadant helt består av följande komponenter:

- En säker token-tjänsten (STS) som tar emot autentiseringsbegäranden & utfärdar säkerhetstoken i RETUR.
- Den klassiska Azure-portalen som används för att skapa, ta bort och aktivering/inaktivering av ACS-namnområden.
- En separat ACS-hanteringsportalen, som används för att anpassa & Konfigurera beteende för en ACS-namnområdet.
- En management-tjänst som kan användas för att automatisera portalerna funktioner.
- En token regeln omvandlingsmotorn, som kan användas för att definiera komplex logik för att ändra utdataformatet för token som utfärdats av ACS.

Om du vill använda dessa komponenter måste du skapa en eller flera ACS **namnområden**. En namnrymd är en dedikerad instans av ACS som dina program och tjänster som kommunicerar med; Det är isolerad från alla andra ACS kunder som använder sina egna namnområden. Ett namnområde i ACS har en dedikerad URL som:

```HTTP
https://mynamespace.accesscontrol.windows.net
```

All kommunikation med STS och hanteringsåtgärder är klar på denna URL med olika sökvägar för olika ändamål. För att avgöra om ditt program eller tjänster använder ACS övervaka för all trafik till `https://{namespace}.accesscontrol.windows.net`.  All trafik till URL: en är trafik som hanteras av ACS och behöver avbrytas.  Det enda undantaget är all trafik till `https://accounts.accesscontrol.windows.net` -trafik till URL: en hanteras redan av en annan tjänst och påverkas inte av ACS utfasningen.  Du bör också vara noga med att logga in på den klassiska Azure-portalen och Sök efter alla ACS-namnområden i prenumerationer som du äger.  ACS-namnområden finns i den **Active Directory** tjänsten under den **Access Control namnområden** fliken.

Mer information om ACS finns [detta arkiveras dokumentation på MSDN](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Tillbakadragning schema

Från och med November 2017 är fullständigt stöds & operativa alla ACS-komponenter. Den enda begränsningen är att [går inte att skapa nya ACS-namnområden via den klassiska Azure-portalen](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Tidslinje för utfasningen av dessa komponenter följer schemat:

- **November 2017**: Azure AD-administratör som uppstår i den klassiska Azure-portalen [har dragits tillbaka](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Nu hantering av namnområdet för ACS kommer att vara tillgängliga på den här nya, dedikerade URL: `http://manage.windowsazure.com?restoreClassic=true`. Detta är att du kan visa dina befintliga namnområden, aktivera och inaktivera dem och ta bort dem helt om du vill.
- **April 2018**: hantering av ACS namnområden är inte längre tillgänglig på den här dedikerad URL. Vid denna tidpunkt, går det inte att Aktiverar/inaktiverar, ta bort eller räkna upp ACS-namnområden. Hanteringsportalen ACS är dock helt funktionella och finns på `https://{namespace}.accesscontrol.windows.net`. Alla andra komponenter i ACS ändå fungerar normalt samt.
- **November 2018**: alla ACS-komponenter stängas av permanent. Detta inkluderar ACS-hanteringsportalen, management-tjänsten, STS och token omvandlingsmotorn för regeln. Nu är alla förfrågningar som skickas till ACS (på `{namespace}.accesscontrol.windows.net`) misslyckas. Du bör har migrerat alla befintliga appar och tjänster till andra tekniker innan den här tidsperioden.


## <a name="migration-strategies"></a>Migreringsstrategier för

I följande avsnitt beskrivs hög nivå rekommendationer för att migrera från ACS med andra Microsoft-teknikerna.

### <a name="clients-of-microsoft-cloud-services"></a>Klienter för Microsoft-molntjänster

Var och en av Microsoft-molntjänster som accepterar token som utfärdats av ACS nu stöder minst en alternativ form av autentisering. Rätt autentiseringsmekanism varierar för varje tjänst, så vi rekommenderar att du refererar till den specifika dokumentationen för varje tjänst officiella anvisningar. För enkelhetens skull finns varje uppsättning dokumentation här:

| Tjänst | Riktlinjer |
| ------- | -------- |
| Azure Service Bus | [Migrera till signaturer för delad åtkomst](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Relay | [Migrera till signaturer för delad åtkomst](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Cache | [Migrera till Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure Data marknaden | [Migrera till kognitiva Services-API: er](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Migrera till Azure Logikappar](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migrera till Azure AD-autentisering](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uppgradera Azure Backup-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>Webbprogram med hjälp av passiv autentisering

För webbprogram med hjälp av ACS för autentisering av användare, angavs ACS följande funktioner och funktioner för utvecklare och arkitekter webbprogram:

- Djupgående integrering med Windows Identity Foundation (WIF).
- Federation med Google, Facebook, Yahoo, Microsoft-konto (Live ID), Azure Active Directory och AD FS.
- Stöd för följande autentiseringsprotokoll: OAuth 2.0-utkast 13 och Ws-Trust Ws-Federation.
- Stöd för token följande format: JSON web token (JWT), SAML 1.1, SAML 2.0 och enkel web token (SWT).
- En identifiering av hemsfär upplevelse, integrerade i WIF som tillåts användaren att välja vilken typ av konto de använder för att logga in. Finns det här upplevelsen av webbprogrammet och helt anpassningsbar.
- Token omvandling får omfattande anpassning av anspråk som tagits emot av webbprogrammet från ACS, inklusive:
    - passera anspråk från identitetsleverantörer
    - lägga till ytterligare anpassade anspråk
    - enkel om sedan logik för att utfärda anspråk vissa villkor

Det finns inte en tjänst som tillhandahåller alla dessa funktioner som motsvarande.  Bör du utvärdera vilka funktioner i ACS du behöver och välj sedan mellan att använda [ **Azure Active Directory** ](https://azure.microsoft.com/develop/identity/signin/) (Azure AD), [ **Azure AD B2C** ](https://azure.microsoft.com/services/active-directory-b2c/), eller en annan molntjänst för autentisering.

#### <a name="migrating-to-azure-active-directory"></a>Migrera till Azure Active Directory

En sökväg till att tänka på integrera dina appar och tjänster direkt med Azure Active Directory. Azure AD är moln baserat identitetsleverantören Microsoft arbetar & skolkonton - det är identitetsleverantören för Office 365, Azure och mycket mer. Det ger liknande federerad autentisering med funktioner för ACS, men stöder inte alla ACS-funktioner. Det primära exemplet är federation med sociala identitetsleverantörer, till exempel Facebook, Google och Yahoo. Om dina användare loggar in med dessa typer av autentiseringsuppgifter, är inte Azure AD för dig. Den också matchar inte nödvändigtvis på exakt samma autentiseringsprotokoll som ACS - när båda ACS-stöd och Azure AD-support OAuth, till exempel det finns vissa skillnader mellan varje implementering som du behöver ändra koden som en del av migreringen.

Azure AD tillhandahåller dock flera potentiella fördelar för ACS-kunder. Den internt stöder Microsoft arbetar & skolkonton som finns i molnet, och som ofta används av ACS-kunder.  En Azure AD-klient kan också externa till en eller flera instanser av lokala Active Directory via ADFS, vilket gör att din app att autentisera både molnbaserade användare och användare som finns lokalt.  Det stöder också Ws-Federation-protokollet, vilket gör det relativt enkelt att integrera med ett webbprogram med hjälp av Windows Identity Foundation (WIF).

I följande tabell jämförs funktionerna i ACS som är relevanta för webbprogram med de som är tillgängliga i Azure AD. På en hög nivå **Azure Active Directory är antagligen rätt val för din migrering om användarna logga in med sina Microsoft work & skolkonton kan endast**.

| Funktion | ACS-stöd | Azure AD-stöd |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsoft arbetar & skolkonton | Stöds | Stöds |
| Konton från Windows Server Active Directory och AD FS | Stöds via federation med en Azure AD-klient <br /> Stöds via direkt federation med AD FS | Stöds bara via federation med en Azure AD-klient | 
| Konton från andra företagssystem identity management | Möjligt via federation med en Azure AD-klient <br />Stöds via direkt federation | Möjligt via federation med en Azure AD-klient |
| Microsoft-konton för personligt bruk (Windows Live ID) | Stöds | Stöds via Azure AD v2.0 OAuth-protokollet, men inte via andra protokoll. | 
| Facebook, Google, Yahoo konton | Stöds | Stöds inte helst. |
| **Protokoll och SDK-kompatibilitet** | | |
| Windows Identity Foundation (WIF) | Stöds | Stöds men begränsad instruktioner som är tillgängliga. |
| WS-Federation | Stöds | Stöds |
| OAuth 2.0 | Stöd för ett utkast till 13 | Stöd för RFC 6749, i de flesta moderna-specifikationen. |
| Ws-Trust | Stöds | Stöds inte |
| **Token format** | | |
| JSON Web token (JWTs) | Stöds i Beta | Stöds |
| 1.1 för SAML-token | Stöds | Stöds |
| SAML 2.0-token | Stöds | Stöds |
| Simple Web Tokens (SWT) | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbara startsfär/kontot för identifiering av plocka UI | Nedladdningsbar kod som kan införlivas i appar | Stöds inte |
| Överför anpassade certifikat för tokensignering | Stöds | Stöds |
| Anpassa anspråk i token | PASSTHROUGH inkommande anspråk från identitetsleverantörer<br />Hämta token från identitetsleverantören som ett anspråk<br />Utfärda utgående anspråk baserade på värden för inkommande anspråk<br />Utfärda utgående anspråk med konstanta värden | Det går inte att passthrough anspråk från federerad Identitetsproviders<br />Det går inte att hämta åtkomst-token från identitetsleverantören som ett anspråk<br />Det går inte att utfärda utgående anspråk baserat på värdena för inkommande anspråk<br />Kan utfärda utgående anspråk med konstanta värden<br />Kan utfärda utgående anspråk baserat på Egenskaper för användare som har synkroniserats till Azure AD |
| **Automation** | | |
| Automatisera uppgifter för konfiguration och hantering | Stöds via Management-tjänsten för ACS | Stöds via Microsoft Graph & Azure AD Graph API. |

Om du väljer att Azure AD är rätt sökväg framåt för ditt program och tjänster, bör du vara medveten om två sätt att integrera din app med Azure AD.

Om du vill använda WIF-Ws-Federation för att integrera med Azure AD, rekommenderar vi följande [den metod som beskrivs i den här artikeln](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Artikeln syftar till att konfigurera Azure AD för SAML-baserade enkel inloggning, men fungerar för att konfigurera Ws-Federation samt. Efter den här metoden kräver en Azure AD Premium-licens, men har två fördelar:

- Du får fullständig flexibilitet för anpassning av Azure AD-token. På så sätt kan du anpassa anspråk som utfärdats av Azure AD så att den matchar de utfärdade av ACS, inklusive särskilt användar-ID eller namnidentifierare anspråk. Du måste se till att användar-ID som utfärdats av Azure AD matchar de som utfärdats av ACS så att du fortsätter att få konsekvent-ID: N för dina användare även när du har ändrat tekniker.
- Du kan konfigurera ett certifikat som är specifik för ditt program för tokensignering vars livstid som du bestämmer.

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> Gå med den här metoden kräver en Azure AD Premium-licens. Om du är en ACS-kund och du behöver en premium-licens för att konfigurera enkel inloggning på för ett program, nå ut till oss och vi att uppskatta ger utvecklare licenser som används.

En annan metod är att följa [detta kodexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), vilket ger något annorlunda instruktioner om hur du ställer in Ws-Federation. Det här kodexemplet använder inte WIF, men i stället ASP.NET 4.5 OWIN mellanprogram. Dock instruktioner för registrering av appen är giltiga för appar som använder WIF och kräver inte en Azure AD Premium-licens.  Om du väljer den här metoden, heneed att förstå [signering nyckelförnyelse i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Den här metoden använder globala signeringsnyckel problemet tokens Azure AD. Som standard uppdateras WIF inte automatiskt Signeringsnycklar. När Azure AD roterar dess globala Signeringsnycklar, måste WIF-implementering vara beredd att acceptera ändringar.

Om du ska kunna integrera med Azure AD via OpenID Connect eller OAuth-protokoll, rekommenderar vi då.  Vi har en omfattande dokumentation och riktlinjer för hur du integrerar Azure AD i ditt webbprogram som är tillgängliga i vår [Utvecklarhandbok för Azure AD](http://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>Migrera till Azure AD B2C

Den andra migreringsvägen att tänka på är Azure AD B2C.  B2C är en autentisering i molnet tjänsten, som liknar ACS, gör att utvecklare kan flytta över sina management logik för autentisering och identitet till en molntjänst.  Det är en betald tjänst (med ledigt & premium nivåer) för konsumenten mot program som kan ha upp till miljontals aktiva användare.

Som ACS en av de mest tilltalande funktionerna för B2C är som stöder många olika typer av konton. Med B2C, du kan logga in användare med Facebook, Google, Microsoft, LinkedIn, GitHub, Yahoo konton och mycket mer. B2C stöder dessutom ”lokala konton” eller användarnamn och lösenord som användaren skapar specifikt för ditt program. Azure AD B2C ger också omfattande utökningsbarhet som du kan använda för att anpassa inloggnings-flöden. Men stöder inte bredd autentiseringsprotokoll & token format som kan kräva att ACS-kunder. Den även kan inte användas för att hämta token & fråga ytterligare information om användaren från identitetsleverantören Microsoft eller på annat sätt.

I följande tabell jämförs funktionerna i ACS som är relevanta för webbprogram med de som är tillgängliga i Azure AD B2C. På en hög nivå **Azure AD B2C är antagligen det rätta valet för din migrering om ditt program är inför konsumenten eller om den har stöd för många olika typer av konton.**

| Funktion | ACS-stöd | Azure AD B2C-Support |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsoft arbetar & skolkonton | Stöds | Stöds via anpassade principer  |
| Konton från Windows Server Active Directory och AD FS | Stöds via direkt federation med AD FS | Stöds via SAML federation anpassade principer |
| Konton från andra företagssystem identity management | Stöds via direkt federation via Ws-Federation | Stöds via SAML federation anpassade principer |
| Microsoft-konton för personligt bruk (Windows Live ID) | Stöds | Stöds | 
| Facebook, Google, Yahoo konton | Stöds | Facebook & Google stöds internt, Yahoo stöds via OpenID Connect federation anpassade principer |
| **Protokoll och SDK-kompatibilitet** | | |
| Windows Identity Foundation (WIF) | Stöds | Stöds inte. |
| WS-Federation | Stöds | Stöds inte. |
| OAuth 2.0 | Stöd för ett utkast till 13 | Stöd för RFC 6749, i de flesta moderna-specifikationen. |
| Ws-Trust | Stöds | Stöds inte. |
| **Token format** | | |
| JSON Web token (JWTs) | Stöds i Beta | Stöds |
| 1.1 för SAML-token | Stöds | Stöds inte |
| SAML 2.0-token | Stöds | Stöds inte |
| Simple Web Tokens (SWT) | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbara startsfär/kontot för identifiering av plocka UI | Nedladdningsbar kod som kan införlivas i appar | Helt anpassningsbar UI via anpassade CSS. |
| Överför anpassade certifikat för tokensignering | Stöds | Anpassade Signeringsnycklar, inte certifikat stöds via anpassade principer. |
| Anpassa anspråk i token | PASSTHROUGH inkommande anspråk från identitetsleverantörer<br />Hämta token från identitetsleverantören som ett anspråk<br />Utfärda utgående anspråk baserade på värden för inkommande anspråk<br />Utfärda utgående anspråk med konstanta värden | Kan passthrough anspråk från identitetsleverantörer. Anpassade principer som krävs för några anspråk.<br />Det går inte att hämta åtkomst-token från identitetsleverantören som ett anspråk<br />Kan utfärda utgående anspråk baserat på värdena för inkommande anspråk via anpassade principer<br />Kan utfärda utgående anspråk med konstanta värden via anpassade principer |
| **Automation** | | |
| Automatisera uppgifter för konfiguration och hantering | Stöds via Management-tjänsten för ACS | Skapande av användare via Azure AD Graph API. Kan inte skapa B2C-klienter, program eller principer programmässigt. |

Om du väljer att Azure AD B2C är rätt sökväg framåt för ditt program och tjänster, bör du börja med följande resurser:

- [Azure AD B2C-dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C priser](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>Andra migreringsalternativ

Om varken Azure AD eller Azure AD B2C uppfyller behoven för ditt webbprogram, kontakta oss och vi kan hjälpa dig att identifiera bästa sökvägen framåt.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>Webbtjänster som använder active-autentisering

För webbtjänster token som utfärdats av ACS-säkrad tillhandahålls ACS följande funktioner och funktioner:

- Möjligheten att registrera en eller flera **tjänsten identiteter** i namnområdet ACS som kan användas för att begära token.
- Stöd för OAuth OMSLUTER & OAuth 2.0 utkast 13 protokoll för att begära token som använder följande typer av autentiseringsuppgifter:
    - Ett enkelt lösenord som skapats för tjänstidentiteten
    - En signerad med en symmetrisk nyckel eller X509 SWT certifikat
    - En SAML-token som utfärdas av en betrodd identitetsleverantör (vanligtvis en AD FS-instans)
- Stöd för token följande format: JSON web token (JWT), SAML 1.1, SAML 2.0 och enkel web token (SWT).
- Enkel token omvandling regler

Tjänstidentiteter i ACS används vanligtvis för att implementera server-till-server (S2S) som autentisering.  

#### <a name="migrating-to-azure-active-directory"></a>Migrera till Azure Active Directory

Vår rekommendation för den här typen av autentiseringsflödet är att migrera till [ **Azure Active Directory** ](https://azure.microsoft.com/develop/identity/signin/) (Azure AD). Azure AD är moln baserat identitetsleverantören Microsoft arbetar & skolkonton - det är identitetsleverantören för Office 365, Azure och mycket mer. Men kan även användas för server till server-autentisering med hjälp av Azure AD-implementeringen av OAuth-klient autentiseringsuppgifter grant.  I följande tabell jämförs funktionerna i ACS i server till server-autentisering med de som finns i Azure AD.

| Funktion | ACS-stöd | Azure AD-stöd |
| ---------- | ----------- | ---------------- |
| Så här registrerar du en webbtjänst | Skapa en förlitande part i ACS-hanteringsportalen. | Skapa en Azure AD-webbprogram i Azure-portalen. |
| Så här registrerar du en klient | Skapa en tjänstidentitet i ACS-hanteringsportalen. | Skapa en annan Azure AD-webbprogram i Azure-portalen. |
| Protokoll som används | OMSLUTA OAuth-protokollet<br />Bevilja OAuth 2.0 utkast 13 klientens autentiseringsuppgifter | Bevilja OAuth 2.0 klientens autentiseringsuppgifter |
| Klientautentiseringsmetoder | Enkla lösenord<br />Signerade SWT<br />SAML-token från federerade IDP | Enkla lösenord<br />Signerade JWT |
| Token format | JWT<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | Endast JWT |
| Omvandling av token | Lägga till anpassade anspråk<br />Enkel om sedan anspråk utfärdande-logiken | Lägga till anpassade anspråk | 
| Automatisera uppgifter för konfiguration och hantering | Stöds via Management-tjänsten för ACS | Stöds via Microsoft Graph & Azure AD Graph API. |

Anvisningar om genomförandet server till server-scenarier, finns i följande resurser:

- [Tjänst-till-tjänst-avsnittet i Azure AD-Utvecklarhandbok](https://aka.ms/aaddev).
- [Daemon kodexemplet med enkla lösenord klientens autentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Daemon kodexempel som använder certifikat klientens autentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>Andra migreringsalternativ

Om Azure AD inte uppfyller behoven för webbtjänsten, lämna en kommentar och vi kan hjälpa dig att identifiera bästa planen för din specifika fall.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>Frågor, problem och Feedback

Vi förstår att många ACS-kunder inte kommer att hitta en tydlig migreringsväg när du har läst den här artikeln och kanske måste vissa hjälp eller riktlinjer för att fastställa rätt plan. Lämna en kommentar på den här sidan om du vill diskutera din Migreringsscenarier & frågor.
