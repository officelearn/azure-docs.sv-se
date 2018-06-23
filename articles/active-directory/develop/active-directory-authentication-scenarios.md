---
title: Autentiseringsscenarier för Azure AD | Microsoft Docs
description: En översikt över de fem vanligaste scenarierna för autentisering för Azure Active Directory (AD Azure)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f85b97791c9e2d17417f82dae05d27838be63703
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319021"
---
# <a name="authentication-scenarios-for-azure-ad"></a>Autentiseringsscenarier för Azure AD

Azure Active Directory (AD Azure) förenklar autentisering för utvecklare genom att tillhandahålla identitet som en tjänst med stöd för standardiserade protokoll, till exempel OAuth 2.0 och OpenID Connect som öppen källkod bibliotek för olika plattformar som hjälper dig att Börja koda snabbt. Den här artikeln hjälper dig att förstå olika scenarier för Azure AD stöder och hur du kommer igång. Den är uppdelad i följande avsnitt:

* [Grunderna för autentisering i Azure AD](#basics-of-authentication-in-azure-ad)
* [Anspråk i säkerhetstoken för Azure AD](#claims-in-azure-ad-security-tokens)
* [Grunderna för att registrera ett program i Azure AD](#basics-of-registering-an-application-in-azure-ad)
* [Programtyper och scenarier](#application-types-and-scenarios)

  * [Webbläsare till webbprogram](#web-browser-to-web-application)
  * [Sida program (SPA)](#single-page-application-spa)
  * [Det ursprungliga programmet i webb-API](#native-application-to-web-api)
  * [Webbprogram i webb-API](#web-application-to-web-api)
  * [Daemon eller ett serverprogram till webb-API](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Grunderna för autentisering i Azure AD

Om du är bekant med grundläggande begrepp för autentisering i Azure AD kan läsa det här avsnittet. I annat fall kanske du vill hoppa över ned till [programtyper och scenarier](#application-types-and-scenarios).

Nu ska vi titta enklaste scenariot där identitet krävs: en användare i en webbläsare måste autentisera till ett webbprogram. Det här scenariot beskrivs mer detaljerat i den [webbläsare till webbprogrammet](#web-browser-to-web-application) avsnitt, men det är en bra utgångspunkt för att illustrera funktionerna i Azure AD och få en överblick hur scenariot fungerar. Överväg följande diagram i det här scenariot:

![Översikt över inloggning till webbprogram](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Diagrammet ovan i åtanke är här vad du behöver veta om dess olika komponenter:

* Azure AD är identitetsleverantören som ansvarar för att verifiera identiteten för användare och program som finns i en organisation directory och slutligen utfärdar säkerhetstoken vid autentisering av användare och program.
* Ett program som du vill flytta ut autentisering till Azure AD måste registreras i Azure AD, som registrerar och som unikt identifierar appen i katalogen.
* Utvecklare kan använda öppen källkod Azure AD-autentiseringsbibliotek så att autentisering lättare genom att hantera protocol-information för dig. Mer information finns i [Azure Active Directory-Autentiseringsbibliotek](active-directory-authentication-libraries.md).
* När en användare har autentiserats, måste programmet verifiera användarens säkerhets-token för att säkerställa att autentiseringen lyckades. Vi har prover av programmet måste göra på olika språk och ramverk på [GitHub](https://github.com/Azure-Samples?q=active-directory). Om du utvecklar ett webbprogram i ASP.NET, finns det [lägga till inloggning för en ASP.NET web app guide](https://docs.microsoft.com/en-us/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp). Om du utvecklar en API-resurs i ASP.NET-webbplats finns på [webb-API Kom igång med](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devquickstarts-webapi-dotnet).
* Flödet av begäranden och -svar för autentiseringsprocessen bestäms av autentiseringsprotokoll som används, till exempel OAuth 2.0, OpenID Connect WS-Federation och SAML 2.0. Dessa protokoll beskrivs i detalj i den [Azure Active Directory-autentiseringsprotokoll](active-directory-authentication-protocols.md) artikeln och i avsnitten nedan.

> [!NOTE]
> Azure AD stöder OAuth 2.0 och OpenID Connect standarder som gör omfattande använder ägar-token, inklusive ägar-token som JWTs. En *ägartoken* är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs. På detta sätt är ”innehavaren” någon part som kan ge token. Även om en part måste de först autentisera med Azure AD ta emot ägartoken om de nödvändiga stegen inte vidtas för att skydda token i överföringen och lagringen, kan de snappas upp och används av en oavsiktlig part. Även om vissa säkerhetstoken har en inbyggd mekanism för att förhindra att obehöriga personer använder dem, ägar-token har inte den här mekanismen och måste transporteras i en säker kanal, till exempel transport layer security (HTTPS). Om ett ägartoken skickas i klartext, kan en man-in-the-middle-attacker användas av skadliga part att hämta token och använda den för en obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper tillämpas när lagring eller cachelagring ägar-token för senare användning. Se alltid till att ditt program överför och lagrar ägar-token på ett säkert sätt. Flera säkerheten på ägar-token finns [RFC 6750 avsnitt 5](http://tools.ietf.org/html/rfc6750).

Nu när du har en översikt över grunderna kan du läsa nedanstående avsnitt för att förstå hur etablering fungerar i Azure AD och vanliga scenarier som har stöd för Azure AD.

## <a name="claims-in-azure-ad-security-tokens"></a>Anspråk i säkerhetstoken för Azure AD

Säkerhetstoken (åtkomst och ID-token) utfärdat av Azure AD innehåller anspråk eller intyg för information om ämnet som har verifierats. Dessa anspråk kan användas av programmet för olika aktiviteter. Program kan använda anspråk för att validera token, identifiera ämnets directory-klient, Visa användarinformation, fastställa användarens tillstånd och så vidare. Anspråk finns i alla angivna säkerhetstoken är beroende av typ av token, vilken typ av autentiseringsuppgift som används för att autentisera användaren och programkonfigurationen. En kort beskrivning av varje typ av anspråk som orsakat av Azure AD har angetts i tabellen nedan. Mer information finns i [vilka typer av token och anspråkstyper stöds](active-directory-token-and-claims.md).

| Begär | Beskrivning |
| --- | --- |
| Program-ID:t | Identifierar det program som använder token. |
| Målgrupp | Identifierar mottagande resursen token är avsett för. |
| Application Authentication Context Class Reference | Anger hur klienten har autentiserat (offentliga klient kontra konfidentiell klienten). |
| Autentisering snabbmeddelanden | Registrerar datum och tid då autentiseringen inträffade. |
| Autentiseringsmetod | Anger hur ämnet för token autentiserades (lösenord, certifikat, etc.). |
| Förnamn | Innehåller det angivna namnet för användaren som angetts i Azure AD. |
| Grupper | Innehåller objekt-ID: N för Azure AD-grupper som användaren är medlem i. |
| Identitetsprovider | Registrerar den identitetsleverantör som autentiserats föremål för token. |
| Utfärdat till | Registrerar den tid då token har utfärdats, används ofta för token dokumentens. |
| Utfärdare | Identifierar STS som orsakat token som Azure AD-klient. |
| Efternamn | Innehåller användarens efternamn som angetts i Azure AD. |
| Namn | Ger en mänsklig läsbar värde som identifierar föremål för token. |
| Objekt-ID | Innehåller en ändras, unik identifierare för ämnet i Azure AD. |
| Roller | Innehåller egna namnen på Azure AD Application roller som användaren har beviljats. |
| Omfång | Anger behörigheterna för klientprogrammet. |
| Ämne | Anger huvudnamn som Assert token information. |
| Klient-ID:t | Innehåller en ändras, unik identifierare för directory-klient som utfärdade token. |
| Livslängd för token | Definierar det tidsintervall inom vilket en token är giltig. |
| Användarens huvudnamn | Innehåller användarens huvudnamn för ämnet. |
| Version | Innehåller versionsnumret för token. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Grunderna för att registrera ett program i Azure AD

Alla program som outsources autentisering till Azure AD måste registreras i en katalog. Det här steget handlar om Azure AD om ditt program, inklusive URL: en där den finns, URL: en att skicka svar efter autentisering, URI: N för att identifiera ditt program och mycket mer. Den här informationen krävs för några viktiga orsaker:

* Azure AD måste kommunicera med programmet när du arbetar med inloggning eller utbyte token. Informationen som skickas mellan Azure AD och programmet innehåller följande:
  
  * **Program-ID URI** -ID för ett program. Det här värdet skickas till Azure AD under autentiseringen för att ange vilket program anroparen vill ha en token för. Det här värdet ingår dessutom i token så att programmet vet var avsedda mål.
  * **Reply URL** och **omdirigerings-URI** -Reply-URL är för en webb-API eller webbprogram, den plats där Azure AD skickar autentiseringssvar, inklusive en token om autentiseringen lyckades. Omdirigerings-URI är en unik identifierare som Azure AD att omdirigera användaragent i en OAuth 2.0-begäran för interna program.
  * **Program-ID** -ID för ett program som genereras av Azure AD när programmet har registrerats. När du begär en Auktoriseringskoden eller token, skickas det program-ID och nyckel till Azure AD under autentiseringen.
  * **Nyckeln** -nyckeln som skickas tillsammans med ett program-ID när du autentiserar till Azure AD att anropa ett webb-API.
* Azure AD måste se till att programmet har behörighet att komma åt dina katalogdata, andra program i din organisation, och så vidare.

Etablering blir tydligare när du förstår att det finns två typer av program som utvecklats och integrerade med Azure AD:

* **Enkel klient programmet** – en enda klient-programmet är avsett att användas i en organisation. Detta är vanligtvis line-of-business (LoB)-appar som skrivits av en enterprise-utvecklare. En enskild klient behöver bara användas av användare i en katalog och därför bara behöver etableras i en katalog. Programmen registreras vanligtvis av en utvecklare i organisationen.
* **Flera innehavare programmet** -ett program med flera innehavare är avsedd att användas i många organisationer är inte en organisation. Detta är vanligtvis programvara som en-tjänst (SaaS)-program som skrivits av en oberoende programvaruleverantör (ISV). Program med flera klienter måste tillhandahållas i varje katalog där de kommer att användas, vilket kräver att användaren eller administratören tillstånd att registrera dem. Medgivande processen startas när ett program som har registrerats i katalogen och ges åtkomst till Graph API eller kanske en annan webb-API. När en användare eller administratör från en annan organisation registrerar sig för att använda programmet, visas en dialogruta som visar de behörigheter som programmet kräver. Användaren eller administratören kan sedan samtycka till programmet, vilket ger programmet tillgång till den angivna data och slutligen registrerar programmet i sina kataloger. Mer information finns i [översikt av Framework medgivande](active-directory-integrating-applications.md#overview-of-the-consent-framework).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Ytterligare överväganden när du utvecklar en eller flera klient appar
Vissa ytterligare överväganden uppstå när du utvecklar ett program med flera innehavare i stället för en enskild klient-program. Till exempel om du gör programmet tillgängligt för användare i flera kataloger, måste en mekanism för att avgöra vilken de inte finns i. En enskild klient behöver bara söka i en egen katalog för en användare när ett program med flera innehavare behöver identifiera en viss användare från alla kataloger i Azure AD. Om du vill utföra den här uppgiften, innehåller Azure AD en gemensam autentisering ändpunkt där alla program med flera innehavare kan dirigera inloggning-förfrågningar, i stället för en klient-specifika slutpunkt. Den här slutpunkten är https://login.microsoftonline.com/common för alla kataloger i Azure AD kan vara en slutpunkt för klient-specifika https://login.microsoftonline.com/contoso.onmicrosoft.com. Vanliga slutpunkten är särskilt viktigt att tänka på när du utvecklar programmet eftersom du behöver den nödvändiga logiken för att hantera flera klienter under inloggning, utloggning, och token-verifiering.

Om du utvecklar för närvarande en enskild klient programmet men vill göra den tillgänglig för många organisationer, kan du enkelt göra ändringar för programmet och dess konfiguration i Azure AD för att göra det med flera innehavare kompatibla. Dessutom använder Azure AD samma signeringsnyckeln för alla token i alla kataloger om du tillhandahåller autentisering i en enskild klient eller ett program för flera innehavare.

Varje scenario som beskrivs i det här dokumentet innehåller ett avsnitt som beskriver etablering krav. Mer detaljerad information om etablering av ett program i Azure AD och skillnaderna mellan enstaka och flera innehavare program finns [integrera program med Azure Active Directory](active-directory-integrating-applications.md) för mer information. Fortsätt läsa för att förstå vanliga Programscenarier för i Azure AD.

## <a name="application-types-and-scenarios"></a>Programtyper och scenarier

Var och en av de scenarier som beskrivs här kan ha utvecklats med olika språk och plattformar. De är alla backas upp av fullständig kodexempel som är tillgängliga i den [kodexempel guide](active-directory-code-samples.md), eller direkt från motsvarande [GitHub-lagringsplatser som exempel](https://github.com/Azure-Samples?q=active-directory). Dessutom, om ditt program måste en viss del eller segment i ett scenario för slutpunkt till slutpunkt, kan i de flesta fall funktionen läggas oberoende av varandra. Till exempel om du har en inbyggd program som anropar ett webb-API, du kan enkelt lägga till ett program som anropar också webb-API. Följande diagram illustrerar dessa scenarier och programtyper, och hur olika komponenter kan läggas till:

![Programtyper och scenarier](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Dessa är de fem primära programmet scenarier som stöds av Azure AD:

* [Webbläsare till webbprogrammet](#web-browser-to-web-application): en användare behöver att logga in på ett program som skyddas av Azure AD.
* [Enkel sida program (SPA)](#single-page-application-spa): en användare behöver att logga in på en enstaka sida-program som skyddas av Azure AD.
* [Det ursprungliga programmet i webb-API](#native-application-to-web-api): en intern program som körs på en mobiltelefon, surfplatta eller dator måste autentisera en användare för att hämta resurser från ett webb-API som skyddas av Azure AD.
* [Webbprogram i webb-API](#web-application-to-web-api): ett webbprogram måste hämta resurser från ett webb-API som skyddas av Azure AD.
* [Daemon eller ett serverprogram till webb-API](#daemon-or-server-application-to-web-api): en daemon-program eller ett serverprogram utan användargränssnitt för web måste hämta resurser från ett webb-API som skyddas av Azure AD.

### <a name="web-browser-to-web-application"></a>Webbläsare till webbprogram

Det här avsnittet beskrivs ett program som autentiserar användare i en webbläsare till ett webbprogram. I det här scenariot dirigerar webbprogrammet användarens webbläsare för att logga in dem på Azure AD. Azure AD returnerar ett svar till användarens webbläsare, som innehåller anspråk om användaren i en säkerhetstoken. Det här scenariot stöder inloggning med WS-Federation, SAML 2.0 och OpenID Connect-protokoll.

#### <a name="diagram"></a>Diagram

![Autentiseringsflödet för webbläsaren till webbprogram](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Beskrivning av protokollet flöde

1. När en användare besöker programmet och behöver logga in, omdirigeras via en begäran till autentiseringsslutpunkten i Azure AD.
1. Användaren loggar in på sidan logga in.
1. Om autentiseringen lyckas skapar en autentiseringstoken Azure AD och returnerar ett svar till programmets Reply URL som har konfigurerats i Azure-portalen. För ett produktionsprogram vara svars-URL: en HTTPS. Returnerade token innehåller anspråk om användaren och Azure AD som krävs av programmet för att validera token.
1. Programmet validerar token med hjälp av en offentlig signeringsnyckel och information om utfärdare tillgänglig federation metadata dokumentet för Azure AD. När programmet validerar token, startar en ny session med användaren. Den här sessionen kan användare komma åt programmet tills den förfaller.

#### <a name="code-samples"></a>Kodexempel

Se kodexempel för webbläsaren till webbprogrammet scenarier. Och kom tillbaka ofta – nya prov läggs ofta. [Webbprogrammet](active-directory-code-samples.md#web-applications).

#### <a name="registering"></a>Registrera

* Enskild klient: Om du skapar ett program för din organisation, den måste registreras i företagets katalog med hjälp av Azure portal.
* Flera innehavare: Om du skapar ett program som kan användas av användare utanför organisationen, den måste vara registrerat i företagets katalog, men även måste registreras i varje organisations katalog som kommer att använda programmet. Om du vill att ditt program finns i deras katalog, kan du inkludera en registreringsprocessen för dina kunder som gör det möjligt för dem att samtycka till ditt program. När de registrerar sig för ditt program, kommer de att visas en dialogruta som visar de behörigheter som krävs för programmet och sedan på alternativet för att godkänna. Beroende på behörigheterna som krävs kan en administratör i den andra organisationen krävas för att ge ditt medgivande. När användaren eller administratören godkänner, registreras programmet i sina kataloger. Mer information finns i [integrera program med Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Token upphör att gälla

Användarens session upphör att gälla när livslängd för token som utfärdas av Azure AD upphör att gälla. Programmet kan förkorta denna tidsperiod om så önskas, till exempel logga ut användare baserat på en period av inaktivitet. När sessionen upphör att gälla, uppmanas användaren att logga in igen.

### <a name="single-page-application-spa"></a>Sida program (SPA)

Det här avsnittet beskrivs autentisering för ett enskilt sidan program som använder Azure AD och OAuth 2.0 implicit auktorisering bevilja för att skydda dess webb-API: N tillbaka avslutas. Den enda sidan program är vanligtvis strukturerade som ett JavaScript presentation lager (klientdel) som körs i webbläsaren och en serverdel för webb-API som körs på en server och implementerar affärslogiken för programmet. Mer information om implicit authorization grant och hjälpa dig att avgöra om det är bäst för ditt program-scenario genom att se [förstå OAuth2 implicit bevilja flödet i Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

I det här scenariot, när användaren loggar in, JavaScript front end använder [Active Directory Authentication Library för JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) och implicit tillstånd att hämta en token med ID (id_token) från Azure AD. Token cachelagras och klienten kopplas till begäran som ägartoken när du anropar dess Web API serverdel som skyddas med OWIN-mellanprogram. 

#### <a name="diagram"></a>Diagram

![Den enda Page Application diagram](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Beskrivning av protokollet flöde

1. Användaren navigerar till webbprogrammet.
1. Programmet returnerar JavaScript klientdelen (presentation layer) till webbläsaren.
1. Användaren initierar inloggning, till exempel klickar du på länken logga in. Webbläsaren skickar en GET till Azure AD-autentiseringsslutpunkt att begära en ID-token. Denna begäran innehåller program-ID och reply URL: en i Frågeparametrar.
1. Azure AD verifierar Reply-URL mot registrerade svars-URL: en som konfigurerats i Azure-portalen.
1. Användaren loggar in på sidan logga in.
1. Om autentiseringen lyckas skapar en ID-token Azure AD och returneras som ett URL-fragment (#) till programmets Reply-URL. För ett produktionsprogram vara svars-URL: en HTTPS. Returnerade token innehåller anspråk om användaren och Azure AD som krävs av programmet för att validera token.
1. JavaScript-klientkoden körs i webbläsaren extraherar token från svaret som ska användas i att skydda anrop till programmets web API tillbaka avslutas.
1. Webbläsaren anropar programmets webb-API: N tillbaka avslutas med åtkomst-token i auktoriseringshuvudet.

#### <a name="code-samples"></a>Kodexempel

Se kodexempel för scenarier med enda sidan program (SPA). Se till att Kom tillbaka ofta – nya prov läggs ofta. [Enkel Page Application (SPA)](active-directory-code-samples.md#single-page-applications).

#### <a name="registering"></a>Registrera

* Enskild klient: Om du skapar ett program för din organisation, den måste registreras i företagets katalog med hjälp av Azure portal.
* Flera innehavare: Om du skapar ett program som kan användas av användare utanför organisationen, den måste vara registrerat i företagets katalog, men även måste registreras i varje organisations katalog som kommer att använda programmet. Om du vill att ditt program finns i deras katalog, kan du inkludera en registreringsprocessen för dina kunder som gör det möjligt för dem att samtycka till ditt program. När de registrerar sig för ditt program, kommer de att visas en dialogruta som visar de behörigheter som krävs för programmet och sedan på alternativet för att godkänna. Beroende på behörigheterna som krävs kan en administratör i den andra organisationen krävas för att ge ditt medgivande. När användaren eller administratören godkänner, registreras programmet i sina kataloger. Mer information finns i [integrera program med Azure Active Directory](active-directory-integrating-applications.md).

När du har registrerat programmet måste den konfigureras för att använda OAuth 2.0 Implicit Grant-protokollet. Det här protokollet är inaktiverat för program som standard. Redigera dess programmanifestet från Azure-portalen och anger du ”oauth2AllowImplicitFlow”-värdet till true om du vill aktivera OAuth2 Implicit Grant-protokollet för ditt program. Detaljerade instruktioner finns [aktiverar OAuth 2.0 Implicit Grant för den enda sidan program](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Token upphör att gälla

Om du använder ADAL.js kan med:

* Uppdatera en token som har upphört att gälla
* begär en åtkomsttoken att anropa ett webb-API-resurs

Efter en lyckad autentisering skriver en cookie i användarens webbläsare för att upprätta en session i Azure AD. Observera att sessionen finns mellan användare och Azure AD (inte mellan användaren och webbprogrammet). När en token upphör att gälla använder ADAL.js den här sessionen tyst få en annan token. ADAL.js använder en dold iFrame för att skicka och ta emot begäran med hjälp av OAuth Implicit Grant-protokollet. ADAL.js kan också använda samma mekanism för att erhålla åtkomsttoken tyst för andra webb-API-resurser som programmet anropar så länge dessa resurser stöder resursdelning för korsande ursprung (CORS) har registrerats i katalogen för den användaren och alla nödvändiga medgivande har angetts av användaren under inloggning.

### <a name="native-application-to-web-api"></a>Det ursprungliga programmet i webb-API

Det här avsnittet beskrivs en programspecifika som anropar ett webb-API för en användares räkning. Det här scenariot bygger på OAuth 2.0 auktorisering kodtypen bevilja med en offentlig klient enligt beskrivningen i avsnittet 4.1 i den [OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749). Det ursprungliga programmet hämtar en åtkomst-token för användaren med hjälp av OAuth 2.0-protokollet. Åtkomsttoken som sedan skickas i förfrågan till ditt webb-API som ger användaren och returnerar en resurs.

#### <a name="diagram"></a>Diagram

![Det ursprungliga programmet för API-Diagram](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Beskrivning av protokollet flöde

Om du använder AD-Autentiseringsbibliotek hanteras de flesta av protokollet informationen som beskrivs nedan, till exempel popup-fönster i webbläsaren, tokencachelagring och hantering av uppdaterings-tokens.

1. Med en webbläsare som popup det ursprungliga programmet gör en begäran till slutpunkten för auktorisering i Azure AD. Denna begäran innehåller program-ID och omdirigerings-URI för det ursprungliga programmet som visas i Azure-portalen och program-ID URI för webb-API. Om användaren inte har redan är inloggad uppmanas de att logga in igen
1. Azure AD autentiserar användaren. Om det är ett program med flera innehavare och medgivande som krävs för att använda programmet, måste användaren tillstånd om de inte redan gjort det. Efter att godkänna och vid autentiseringen, utfärdar Azure AD en auktorisering kod svar tillbaka till klientprogrammets omdirigerings-URI.
1. När Azure AD utfärdar en auktorisering kod svar tillbaka till omdirigerings-URI, stoppar webbläsare interaktion klientprogrammet och extraherar Auktoriseringskoden från svaret. Med den här auktoriseringskod klientprogrammet skickar en begäran till tokenslutpunkten för Azure AD som innehåller Auktoriseringskoden, information om klientprogrammet (program-ID och omdirigerings-URI) och en resurs (program-ID URI för den webb-API).
1. Auktoriseringskoden och information om klienten program och API: et verifieras av Azure AD. När valideringen har lyckats, Azure AD returnerar två token: en JWT-åtkomsttoken och en uppdatering JWT-token. Dessutom kan returnerar Azure AD grundläggande information om användare, till exempel deras visas namnet och klient-ID.
1. Via HTTPS använder klientprogrammet returnerade JWT åtkomsttoken att lägga till JWT-sträng med en ”ägar” beteckning i Authorization-huvud för begäran i webb-API. Webb-API validerar JWT-token och om verifieringen lyckas, returnerar du en resurs.
1. När den åtkomst-token upphör att gälla kommer klientprogrammet ett felmeddelande som anger användaren måste autentiseras igen. Om programmet har en giltig uppdateringstoken kan användas den för att hämta en ny åtkomsttoken utan att användaren uppmanas att logga in igen. Om uppdateringstoken upphör att gälla, måste programmet att interaktivt autentisera användaren igen.

> [!NOTE]
> Den uppdateringstoken som utfärdats av Azure AD kan användas för åtkomst till flera resurser. Om du har ett klientprogram som har behörighet att anropa två webb-API: er, kan uppdateringstoken användas att hämta en åtkomst-token i den andra webben-API samt.

#### <a name="code-samples"></a>Kodexempel

Se kodexempel för programspecifika Web API-scenarier. Och kom tillbaka ofta--vi lägga till nya prov ofta. [Det ursprungliga programmet i webb-API](active-directory-code-samples.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

#### <a name="registering"></a>Registrera

* Enskild klient: Både överordnade länkens interna program och webb-API som måste registreras i samma katalog i Azure AD. Webb-API kan konfigureras för att exponera en uppsättning behörigheter som används för att begränsa interna programmet åtkomst till resurser. Klientprogrammet sedan markerar du önskade behörigheter från listrutan ”behörigheter till andra program” i Azure-portalen.
* Flera innehavare: Först det ursprungliga programmet bara registrerad i utvecklaren eller utgivarens katalog. Andra har till det ursprungliga programmet konfigurerats för att ange de behörigheter som krävs för att fungera korrekt. Den här listan med behörigheter som krävs visas i en dialogruta när en användare eller administratör i målmappen ger du ditt medgivande till programmet, vilket gör den tillgänglig för organisationen. Vissa program kräver endast användarnivå behörigheter som kan godkänna alla användare i organisationen. Andra applikationer kräver på administratörsnivå som en användare i organisationen inte kan samtycka till. Endast directory-administratören kan ge ditt medgivande till program som kräver den här nivån av behörigheter. När användaren eller administratören godkänner, registreras endast webb-API i sina kataloger. Mer information finns i [integrera program med Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Token upphör att gälla

När det ursprungliga programmet använder dess auktoriseringskod för att hämta en JWT åtkomsttoken, emot tas även en uppdatering JWT-token. Uppdateringstoken som kan användas för att autentisera användaren igen utan att de behöver logga in igen när den åtkomst-token upphör att gälla. Den här uppdateringstoken används sedan för att autentisera användaren, vilket leder till en ny åtkomsttoken och uppdateringstoken.

### <a name="web-application-to-web-api"></a>Webbprogram i webb-API

Det här avsnittet beskrivs ett webbprogram som behöver få resurser från ett webb-API. I det här scenariot är två identitetstyper som webbprogrammet kan använda för att autentisera och anropa webb-API: en Programidentitet eller en delegerad användaridentitet.

*Tillämpningsprogrammets identitet:* det här scenariot använder OAuth 2.0 klientens autentiseringsuppgifter bevilja att autentisera som programmet och få åtkomst till webb-API. När du använder en Programidentitet, webb-API kan endast upptäcka att webbprogrammet anropar den, som webb får API inte någon information om användaren. Om programmet får information om användaren kan skickas via programprotokollet och den inte är signerad av Azure AD. Webb-API förtroenden att webbprogrammet autentiserade användaren. Därför kan kallas det här mönstret betrodda undersystemet.

*Delegerad användaridentitet:* det här scenariot kan utföras på två sätt: OpenID Connect och OAuth 2.0 auktorisering kod bevilja en konfidentiell klient. Webbprogrammet hämtar en åtkomsttoken för den användaren, vilket bevisar för webb-API som användaren har autentiserats till webbprogrammet och att webbprogrammet kunde erhålla en delegerad användaridentitet för att anropa webb-API. Åtkomsttoken skickas i förfrågan till ditt webb-API som ger användaren och returnerar en resurs.

#### <a name="diagram"></a>Diagram

![Webbprogram i Web API-diagram](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Beskrivning av protokollet flöde

Både Programidentitet och delegerade identitet användartyper diskuteras nedan flödet. Den viktigaste skillnaden mellan dem är att delegerad användaridentiteten först skaffa ett auktoriseringskod innan användaren kan logga in och få åtkomst till webb-API.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Bevilja Programidentitet med OAuth 2.0 klientens autentiseringsuppgifter

1. En användare är inloggad i Azure AD i webbprogrammet (finns i [webbläsare till webbprogrammet](#web-browser-to-web-application) ovan).
1. Webbprogrammet måste få en åtkomst-token så att den kan autentisera till webb-API och hämta en resurs. Den gör en begäran till tokenslutpunkten för Azure AD, att tillhandahålla autentiseringsuppgifter, program-ID och API: er webbprogrammet ID URI.
1. Azure AD autentiserar programmet och returnerar en JWT åtkomst-token som används för att anropa webb-API.
1. Via HTTPS används webbprogrammet returnerade JWT åtkomsttoken att lägga till JWT-sträng med en ”ägar” beteckning i Authorization-huvud i begäran webb-API. Webb-API validerar JWT-token och om verifieringen lyckas, returnerar du en resurs.

##### <a name="delegated-user-identity-with-openid-connect"></a>Delegerad användaridentiteter med OpenID Connect

1. En användare är inloggad på ett webbprogram med hjälp av Azure AD (finns i [webbläsare till webbprogrammet](#web-browser-to-web-application) ovan). Om användaren av webbprogrammet inte har ännu godkänt för att tillåta att webbprogrammet anropa webb-API i dess ställe, måste användaren att godkänna. Programmet visas de behörigheter som krävs och om någon av dessa finns på administratörsnivå normal användare i katalogen inte kommer att kunna godkänna. Medgivande processen gäller bara för program med flera klienter, inte en organisation program som programmet redan har behörigheterna som krävs. När användaren är inloggad webbprogrammet tog emot en ID-token med information om användaren, samt en Auktoriseringskoden.
1. Med auktoriseringskod som utfärdats av Azure AD skickar webbprogrammet en begäran till tokenslutpunkten för Azure AD som innehåller Auktoriseringskoden information om klientprogrammet (program-ID och omdirigerings-URI) och en resurs (program-ID URI för webb-API).
1. Auktoriseringskoden och information om webbprogram och webb-API verifieras av Azure AD. När valideringen har lyckats, Azure AD returnerar två token: en JWT-åtkomsttoken och en uppdatering JWT-token.
1. Via HTTPS används webbprogrammet returnerade JWT åtkomsttoken att lägga till JWT-sträng med en ”ägar” beteckning i Authorization-huvud i begäran webb-API. Webb-API validerar JWT-token och om verifieringen lyckas, returnerar du en resurs.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegerad användaridentiteter med OAuth 2.0 auktorisering kod bevilja

1. En användare har redan loggat in till ett webbprogram vars autentiseringsmekanism är oberoende av Azure AD.
1. Webbprogrammet kräver en auktoriseringskod att förvärva en åtkomst-token så att den skickar en begäran via webbläsaren till Azure AD autentiseringsslutpunkt att tillhandahålla det program-ID och omdirigerings-URI för webbprogrammet efter en lyckad autentisering. Användaren loggar in på Azure AD.
1. Om användaren av webbprogrammet inte har ännu godkänt för att tillåta att webbprogrammet anropa webb-API i dess ställe, måste användaren att godkänna. Programmet visas de behörigheter som krävs och om någon av dessa finns på administratörsnivå normal användare i katalogen inte kommer att kunna godkänna. Den här medgivande gäller både enkla och flera innehavare program. Om en organisation kan administratör utföra admin medgivande medgivande åt sina användare. Detta kan göras med hjälp av den `Grant Permissions` knappen i den [Azure Portal](https://portal.azure.com). 
1. När användaren har godkänt får webbprogrammet Auktoriseringskoden som behöver få åtkomst-token.
1. Med auktoriseringskod som utfärdats av Azure AD skickar webbprogrammet en begäran till tokenslutpunkten för Azure AD som innehåller Auktoriseringskoden information om klientprogrammet (program-ID och omdirigerings-URI) och en resurs (program-ID URI för webb-API).
1. Auktoriseringskoden och information om webbprogram och webb-API verifieras av Azure AD. När valideringen har lyckats, Azure AD returnerar två token: en JWT-åtkomsttoken och en uppdatering JWT-token.
1. Via HTTPS används webbprogrammet returnerade JWT åtkomsttoken att lägga till JWT-sträng med en ”ägar” beteckning i Authorization-huvud i begäran webb-API. Webb-API validerar JWT-token och om verifieringen lyckas, returnerar du en resurs.

#### <a name="code-samples"></a>Kodexempel

Se kodexempel för webbprogram för Web API-scenarier. Och kom tillbaka ofta – nya prov läggs ofta. Web [programmet till webb-API](active-directory-code-samples.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

#### <a name="registering"></a>Registrera

* Enskild klient: För både Programidentitet och delegerad användare identitet fall kan webbprogrammet och webb-API måste registreras i samma katalog i Azure AD. Webb-API kan konfigureras för att exponera en uppsättning behörigheter som används för att begränsa webbprogrammets åtkomst till resurser. Om en delegerad användaridentitetstypen används måste webbprogrammet Välj önskade behörigheter från listrutan ”behörigheter till andra program” i Azure-portalen. Det här steget är inte nödvändigt om identitetstyp programmet används.
* Flera innehavare: Först webbprogrammet har konfigurerats för att ange de behörigheter som krävs för att fungera korrekt. Den här listan med behörigheter som krävs visas i en dialogruta när en användare eller administratör i målmappen ger du ditt medgivande till programmet, vilket gör den tillgänglig för organisationen. Vissa program kräver endast användarnivå behörigheter som kan godkänna alla användare i organisationen. Andra applikationer kräver på administratörsnivå som en användare i organisationen inte kan samtycka till. Endast directory-administratören kan ge ditt medgivande till program som kräver den här nivån av behörigheter. När användaren eller administratören godkänner, registreras webbprogrammet och webb-API både i sina kataloger.

#### <a name="token-expiration"></a>Token upphör att gälla

När webbprogrammet använder dess auktoriseringskod för att hämta en JWT åtkomsttoken, emot tas även en uppdatering JWT-token. Uppdateringstoken som kan användas för att autentisera användaren igen utan att de behöver logga in igen när den åtkomst-token upphör att gälla. Den här uppdateringstoken används sedan för att autentisera användaren, vilket leder till en ny åtkomsttoken och uppdateringstoken.

### <a name="daemon-or-server-application-to-web-api"></a>Daemon eller ett serverprogram till webb-API

Det här avsnittet beskrivs ett daemon eller server-program som behöver få resurser från ett webb-API. Det finns två underordnade scenarier som gäller för det här avsnittet: en daemon som måste anropa ett webb-API som bygger på OAuth 2.0 autentiseringsuppgifter bevilja klienttyp; och ett serverprogram (till exempel ett webb-API) som måste anropa ett webb-API som bygger på OAuth 2.0 On-Behalf-Of utkast till en specifikation.

För scenariot när en daemon programmet behöver anropa ett webb-API, är det viktigt att förstå några saker. Först är interaktion från användaren inte möjligt med ett daemon-program som kräver att applikationen har sin egen identitet. Ett exempel på en daemon-program är batchjobb eller en tjänst i operativsystemet som körs i bakgrunden. Den här typen av program begär en åtkomst-token med hjälp av dess Programidentitet och presentera dess program-ID och autentiseringsuppgifter (lösenordet eller certifikatet) program-ID URI till Azure AD. Efter en lyckad autentisering får daemon en åtkomst-token från Azure AD, som sedan används för att anropa webb-API.

För scenariot när ett serverprogram måste anropa ett webb-API, är det bra att använda ett exempel. Anta att en användare har autentiserats på det ursprungliga programmet och interna programmet behöver anropa ett webb-API. Azure AD utfärdar en JWT åtkomsttoken att anropa webb-API. Om webb-API måste anropa ett annat underordnat webb-API, använda den on-behalf-of-flöde för att delegera användarens identitet och autentisera till andra nivån webb-API.

#### <a name="diagram"></a>Diagram

![Daemon eller serverprogrammet Web API-diagram](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Beskrivning av protokollet flöde

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Bevilja Programidentitet med OAuth 2.0 klientens autentiseringsuppgifter

1. Serverprogrammet måste först autentisera med Azure AD som själva, utan att någon mänsklig interaktion, till exempel en dialogruta för interaktiv inloggning. Den gör en begäran till tokenslutpunkten för Azure AD, att tillhandahålla autentiseringsuppgifter, program-ID och program-ID-URI.
1. Azure AD autentiserar programmet och returnerar en JWT åtkomst-token som används för att anropa webb-API.
1. Via HTTPS används webbprogrammet returnerade JWT åtkomsttoken att lägga till JWT-sträng med en ”ägar” beteckning i Authorization-huvud i begäran webb-API. Webb-API validerar JWT-token och om verifieringen lyckas, returnerar du en resurs.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegerad användaridentiteter med OAuth 2.0 On-Behalf-Of utkast-specifikationen

Flödet beskrivs nedan förutsätter att en användare har autentiserats på ett annat program (till exempel programspecifika) och deras användar-ID som har använts för att erhålla en åtkomst-token i den första nivån webben-API.

1. Det ursprungliga programmet skickar den åtkomst-token till den första nivån webben-API.
1. Den första nivån webben-API skickar en begäran till tokenslutpunkten för Azure AD, tillhandahåller dess program-ID och autentiseringsuppgifter, samt användarens åtkomsttoken. Dessutom begäran skickas med en on_behalf_of parameter som anger web API begär nya tokens för att anropa en underordnad webb-API för den ursprungliga användaren.
1. Azure AD verifierar att den första nivån webben-API har åtkomstbehörighet till den andra nivån webben-API och verifierar begäran returnerar en JWT-åtkomsttoken och en JWT uppdatera token i den första nivån webben-API.
1. Via HTTPS anropar den första nivån web API andra nivån webb-API genom att lägga till token strängen i Authorization-huvud i begäran. Den första nivån webben-API kan fortsätta att anropa den andra nivån webben-API som åtkomst-token och uppdateringstoken är giltiga.

#### <a name="code-samples"></a>Kodexempel

Se kodexempel för Daemon eller serverprogrammet Web API-scenarier. Och kom tillbaka ofta – nya prov läggs ofta. [Servern eller Daemon programmet till webb-API](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

#### <a name="registering"></a>Registrera

* Enskild klient: För både Programidentitet och delegerad användare identitet fall, daemon eller server-programmet måste registreras i samma katalog i Azure AD. Webb-API kan konfigureras för att exponera en uppsättning behörigheter som används för att begränsa daemon eller serveråtkomst till dess resurser. Om en delegerad användaridentitetstypen används måste serverprogrammet Välj önskade behörigheter från listrutan ”behörigheter till andra program” i Azure-portalen. Det här steget är inte nödvändigt om identitetstyp programmet används.
* Flera innehavare: Först programmet daemon eller server har konfigurerats för att ange de behörigheter som krävs för att fungera korrekt. Den här listan med behörigheter som krävs visas i en dialogruta när en användare eller administratör i målmappen ger du ditt medgivande till programmet, vilket gör den tillgänglig för organisationen. Vissa program kräver endast användarnivå behörigheter som kan godkänna alla användare i organisationen. Andra applikationer kräver på administratörsnivå som en användare i organisationen inte kan samtycka till. Endast directory-administratören kan ge ditt medgivande till program som kräver den här nivån av behörigheter. När användaren eller administratören godkänner, är både web API: er registrerade i sina kataloger.

#### <a name="token-expiration"></a>Token upphör att gälla

När det första programmet använder dess auktoriseringskod för att hämta en JWT åtkomsttoken, emot tas även en uppdatering JWT-token. Uppdateringstoken som kan användas för att autentisera användaren igen utan att fråga efter autentiseringsuppgifter när den åtkomst-token upphör att gälla. Den här uppdateringstoken används sedan för att autentisera användaren, vilket leder till en ny åtkomsttoken och uppdateringstoken.

## <a name="see-also"></a>Se även

[Azure Active Directory-Guide för utvecklare](active-directory-developers-guide.md)

[Azure Active Directory-kodexempel](active-directory-code-samples.md)

[Viktig Information om att signera nyckelförnyelse i Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)
