---
title: Autentisering i Microsofts identitetsplattform | Azure
description: Lär dig mer om grunderna för autentisering i Microsofts identitetsplattform (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6191e67f097b5ab471c5b31eff11a0e570d1c990
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617033"
---
# <a name="authentication-basics"></a>Grundläggande om autentisering

## <a name="what-is-authentication"></a>Vad är autentisering

Den här artikeln beskriver många av de autentiseringsbegrepp du behöver förstå för att skapa skyddade webbappar, webb-API:er eller appar som anropar skyddade webb-API:er. Om du ser en term som du inte är bekant med kan du prova vår [ordlista](developer-glossary.md) eller våra [microsoft-identitetsplattformsvideor](identity-videos.md) som täcker grundläggande begrepp.

**Autentisering** är processen att bevisa att du är den du säger att du är. Autentisering förkortas ibland AuthN.

**Auktorisering** är handlingen att bevilja en autentiserat parti tillstånd att göra något. Den anger vilka data du får åtkomst till och vad du kan göra med dessa data. Auktorisering förkortas ibland AuthZ.

I stället för att skapa appar som var och en har sin egen information om användarnamn och lösenord, vilket medför en stor administrativ börda när du behöver lägga till eller ta bort användare i flera appar, kan appar delegera det ansvaret till en centraliserad identitetsleverantör.

Azure Active Directory (Azure AD) är en centraliserad identitetsleverantör i molnet. Delegera autentisering och auktorisering till det möjliggör scenarier som principer för villkorlig åtkomst som kräver att en användare är på en viss plats, användning av multifaktorautentisering, samt gör det möjligt för en användare att logga in en gång och sedan automatiskt loggas in på alla webbappar som delar samma centraliserade katalog. Den här funktionen kallas Single Sign On (SSO).

En centraliserad identitetsleverantör är ännu viktigare för appar som har användare över hela världen som inte nödvändigtvis loggar in från företagets nätverk. Azure AD autentiserar användare och tillhandahåller åtkomsttoken. En [åtkomsttoken](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) är en säkerhetstoken som utfärdas av en auktoriseringsserver. Den innehåller information om användaren och appen som token är avsedd för. som kan användas för att komma åt webb-API:er och andra skyddade resurser.

Microsofts identitetsplattform förenklar autentiseringen för programutvecklare genom att tillhandahålla identitet som en tjänst, med stöd för branschstandardprotokoll som [OAuth 2.0](https://oauth.net/2/) och [OpenID Connect](https://openid.net/connect/), samt bibliotek med öppen källkod för olika plattformar som hjälper dig att snabbt börja koda. Det gör det möjligt för utvecklare att skapa program som loggar in alla Microsoft-identiteter, få token att anropa [Microsoft Graph](https://developer.microsoft.com/graph/), andra Microsoft API:er eller API:er som utvecklare har byggt. Mer information finns i [Utveckling av Microsofts identitetsplattform](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Klientorganisationer

En molnidentitetsleverantör betjänar många organisationer. Azure AD delas in i klienter med en klient per organisation för att hålla användare från olika organisationer åtskilda.

Klienter håller reda på användare och tillhörande appar. Microsofts identitetsplattform stöder också användare som loggar in med personliga Microsoft-konton.

Azure AD tillhandahåller också Azure Active Directory B2C så att organisationer kan logga in användare, vanligtvis kunder, med hjälp av sociala identiteter som ett Google-konto. Mer information finns i [Azure Active Directory B2C-dokumentation](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Säkerhetstoken

Säkerhetstoken innehåller information om användare och appar. Azure AD använder JSON-baserade token (JWTs) som innehåller anspråk.

Ett anspråk innehåller påståenden om en entitet, till exempel ett [klientprogram](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) eller [resursägare,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)till en annan entitet, till exempel en [resursserver](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server).

Anspråk är namn/värdepar som vidarebefordrar fakta om tokenämnet. Ett anspråk kan till exempel innehålla fakta om säkerhetsobjektet som autentiserades av [auktoriseringsservern](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server). Anspråken som finns i en viss token beror på många saker, inklusive typ av token, vilken typ av autentiseringsuppgifter som används för att autentisera ämnet, programkonfigurationen och så vidare.

Program kan använda anspråk för olika uppgifter, till exempel:

* Validera token
* Identifiera tokenämnets klient
* Visa användarinformation
* Fastställande av ämnets tillstånd

Ett anspråk består av nyckelvärdespar som tillhandahåller information som:

* Security Token Server som genererade token
* Datum då token genererades
* Ämne (t.ex. användaren - med undantag för demoner)
* Målgrupp, som är den app som token skapades för
* App (klienten) som bad om token. När det gäller webbappar kan detta vara samma som

Mer detaljerad anspråksinformation finns [i komma åt token](access-tokens.md) och [ID-token](id-tokens.md).

Det är upp till appen som token skapades för, webbappen som loggade in användaren eller webb-API:et som anropas för att validera token. Token signeras av Security Token Server (STS) med en privat nyckel. STS publicerar motsvarande offentliga nyckel. För att validera en token verifierar appen signaturen med hjälp av sts-tangenten för att verifiera att signaturen skapades med den privata nyckeln.

Tokens är endast giltiga under en begränsad tid. Vanligtvis ger STS ett par token: en åtkomsttoken för att komma åt programmet eller den skyddade resursen och en uppdateringstoken som används för att uppdatera åtkomsttoken när åtkomsttoken är nära att löpa ut.

Åtkomsttoken skickas till ett webb-API som `Authorization` innehavartoken i huvudet. En app kan ge en uppdateringstoken till STS, och om användarens åtkomst till appen inte återkallades får den tillbaka en ny åtkomsttoken och en ny uppdateringstoken. Så här hanteras scenariot för att någon lämnar företaget. När STS tar emot uppdateringstoken utfärdar den inte en annan giltig åtkomsttoken om användaren inte längre är auktoriserad.

### <a name="how-each-flow-emits-tokens-and-codes"></a>Hur varje flöde avger token och koder

Beroende på hur klienten är byggd kan den använda en (eller flera) av autentiseringsflödena som stöds av Azure AD. Dessa flöden kan producera en mängd olika token (id_tokens, uppdatera token, åtkomsttoken) samt auktoriseringskoder och kräva olika token för att få dem att fungera. Det här diagrammet innehåller en översikt:

|Flöde | Kräver | id_token | åtkomsttoken | uppdatera token | auktoriseringskod | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Flöde av auktoriseringskod](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicit flöde](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrid OIDC-flöde](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Uppdatera tokeninlösen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | uppdatera token | x | x | x| |
|[On-Behalf-Of-flöde](v2-oauth2-on-behalf-of-flow.md) | åtkomsttoken| x| x| x| |
|[Klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) | | | x (endast app)| | |

Tokens som utfärdas via implicit läge har en längdbegränsning på grund av `response_mode` `query` att `fragment`de skickas tillbaka till webbläsaren via webbadressen (var är eller ).  Vissa webbläsare har en gräns för storleken på webbadressen som kan placeras i webbläsarfältet och misslyckas när den är för lång.  Dessa tokens har `groups` eller `wids` gör anspråk. 

Nu när du har en översikt över grunderna, läs vidare för att förstå identitetsappmodellen och API:et, lära dig hur etablering fungerar i Azure AD och få länkar till detaljerad information om vanliga scenarier som Azure AD stöder.

## <a name="application-model"></a>Programmodell

Program kan logga in användare själva eller delegera inloggning till en identitetsprovider. Se [Autentiseringsflöden och appscenarier](authentication-flows-app-scenarios.md) om du vill veta mer om inloggningsscenarier som stöds av Azure AD.

För att en identitetsprovider ska veta att en användare har åtkomst till en viss app måste både användaren och programmet registreras hos identitetsprovidern. När du registrerar ditt program med Azure AD tillhandahåller du en identitetskonfiguration för ditt program som gör att det kan integreras med Azure AD. Om du registrerar appen kan du också:

* Anpassa varumärkesprofilen för ditt program i inloggningsdialogrutan. Detta är viktigt eftersom detta är den första upplevelsen en användare kommer att ha med din app.
* Bestäm om du vill att användarna bara ska logga in om de tillhör din organisation. Det här är ett enda klientprogram. Eller tillåt användare att logga in med ett arbets- eller skolkonto. Det här är ett program för flera innehavare. Du kan också tillåta personliga Microsoft-konton eller ett socialt konto från LinkedIn, Google och så vidare.
* Begär scopebehörigheter. Du kan till exempel begära "user.read"-scopet, som ger behörighet att läsa profilen för den inloggade användaren.
* Definiera scope som definierar åtkomst till webb-API:et. När en app vill komma åt ditt API måste den vanligtvis begära behörigheter till de scope som du definierar.
* Dela en hemlighet med Azure AD som bevisar appens identitet till Azure AD.  Detta är relevant i de fall där appen är ett konfidentiellt klientprogram. Ett konfidentiellt klientprogram är ett program som kan innehålla autentiseringsuppgifter på ett säkert sätt. De kräver en betrodd server för server för server för server för server för server för server för att lagra autentiseringsuppgifterna.

När programmet har registrerats får det en unik identifierare som appen delar med Azure AD när den begär token. Om appen är ett [konfidentiellt klientprogram](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)kommer den också att dela hemligheten eller den offentliga nyckeln*-beroende på om certifikat eller hemligheter har använts.

Microsofts identitetsplattform representerar program som använder en modell som fyller två huvudfunktioner:

* Identifiera appen med de autentiseringsprotokoll som den stöder
* Ange alla identifierare, webbadresser, hemligheter och relaterad information som behövs för att autentisera

Microsofts identitetsplattform:

* Innehåller alla data som krävs för att stödja autentisering vid körning
* Innehåller alla data för att avgöra vilka resurser en app kan behöva komma åt, och under vilka omständigheter en viss begäran ska uppfyllas
* Tillhandahåller infrastruktur för implementering av appetablering i apputvecklarens klientorganisation och till alla andra Azure AD-klienter
* Hanterar användarens medgivande under tokenbegärantid och underlättar dynamisk etablering av appar mellan klienter

Medgivande är processen för en resursägare som beviljar auktorisering för ett klientprogram för åtkomst till skyddade resurser, under specifika behörigheter, för resursägarens räkning. Microsofts identitetsplattform:

* Möjliggör för användare och administratörer att dynamiskt bevilja eller neka medgivande för appen att få åtkomst till resurser för deras räkning.
* Möjliggör för administratörer att i slutänden bestämma vilka appar som tillåts göra vad och vilka användare som kan använda specifika appar samt hur åtkomsten till katalogresurserna går till.

I Microsofts identitetsplattform beskriver ett [programobjekt](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) ett program. Vid distributionen använder Microsoft identity-plattformen programobjektet som en skiss för att skapa ett [huvudnamn](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)för tjänsten , vilket representerar en konkret instans av ett program i en katalog eller klient. Tjänstens huvudnamn definierar vad appen faktiskt kan göra i en specifik målkatalog, vem som kan använda den, vilka resurser den har åtkomst till och så vidare. Microsofts identitetsplattform skapar ett tjänsthuvudnamn från ett programobjekt via **medgivande**.

Följande diagram visar ett förenklat Microsoft identity platform-etableringsflöde som drivs av medgivande. Den visar två klienter: A och B. Klient A äger programmet. Klient B instansierar programmet via ett tjänsthuvudnamn.  

![Förenklat etableringsflöde som drivs av medgivande](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

I det här etableringsflödet sker följande:

1. En användare från klient B försöker logga in med appen, begäranden om auktoriseringsslutpunkt en token för programmet.
1. Användarautentiseringsuppgifterna hämtas och verifieras för autentisering.
1. Användaren uppmanas att ge samtycke till att appen får åtkomst till klient B.
1. Microsoft-identitetsplattformen använder programobjektet i klient A som en skiss för att skapa ett tjänsthuvudnamn i klientnummer B.
1. Användaren tar emot den begärda token.

Du kan upprepa den här processen för ytterligare klienter. Klient A behåller skissen för appen (programobjektet). Användare och administratörer för alla andra klienter där appen får samtycke behåller kontrollen över vad programmet tillåts göra via motsvarande huvudobjekt för tjänsten i varje klient. Mer information finns [i Program- och tjänsthuvudobjekt i Microsofts identitetsplattform](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Inloggningsflöde för webbappar med Azure AD

När en användare navigerar i webbläsaren till en webbapp händer följande:

* Webbappen avgör om användaren är autentiserat.
* Om användaren inte autentiseras delegeras webbappen till Azure AD för att logga in användaren. Inloggningen är kompatibel med organisationens policy, vilket kan innebära att användaren uppmanas att ange sina autentiseringsuppgifter, använda multifaktorautentisering eller inte använda ett lösenord alls (till exempel med Windows Hello).
* Användaren uppmanas att godkänna den åtkomst som klientappen behöver. Det är därför klientappar måste registreras med Azure AD, så att Azure AD kan leverera token som representerar den åtkomst som användaren har samtyckt till.

När användaren har autentiserat:

* Azure AD skickar en token till webbappen.
* En cookie sparas, associerad med Azure AD:s domän, som innehåller användarens identitet i webbläsarens cookieburk. Nästa gång en app använder webbläsaren för att navigera till slutpunkten för Azure AD-auktorisering visar webbläsaren cookien så att användaren inte behöver logga in igen. Detta är också det sätt på vilket SSO uppnås. Cookien produceras av Azure AD och kan endast förstås av Azure AD.
* Webbappen validerar sedan token. Om valideringen lyckas visar webbappen den skyddade sidan och sparar en sessionscookie i webbläsarens cookieburk. När användaren navigerar till en annan sida vet webbappen att användaren autentiseras baserat på sessionscookien.

Följande sekvensdiagram sammanfattar den här interaktionen:

![autentiseringsprocessen för webbappar](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Hur en webbapp avgör om användaren är autentiserad

Webbapputvecklare kan ange om alla eller bara vissa sidor kräver autentisering. I ASP.NET/ASP.NET Core görs till exempel detta genom `[Authorize]` att lägga till attributet i styrenhetens åtgärder. 

Det här attributet gör att ASP.NET kontrollerar om det finns en sessionscookie som innehåller användarens identitet. Om en cookie inte finns omdirigerar ASP.NET autentisering till den angivna identitetsleverantören. Om identitetsprovidern är Azure AD omdirigerar webbappen autentisering till `https://login.microsoftonline.com`, som visar en inloggningsdialogruta.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Så här loggar en webbapp in på Azure AD och skaffar en token

Användarautentisering sker via webbläsaren. OpenID-protokollet använder vanliga HTTP-protokollmeddelanden.
* Webbappen skickar en HTTP 302 (omdirigering) till webbläsaren för att använda Azure AD.
* När användaren autentiseras skickar Azure AD token till webbappen med hjälp av en omdirigering via webbläsaren.
* Omdirigeringen tillhandahålls av webbappen i form av en omdirigera URI. Den här omdirigeringen URI har registrerats med Azure AD-programobjektet. Det kan finnas flera omdirigerings-URI:er eftersom programmet kan distribueras på flera webbadresser. Så webbappen måste också ange den omdirigera URI som ska användas.
* Azure AD verifierar att den omdirigerande URI som skickas av webbappen är en av de registrerade omdirigerings-URI:erna för appen.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Inloggningsflöde för stationära och mobila appar med Azure AD

Det flöde som beskrivs ovan gäller, med små skillnader, för stationära och mobila applikationer.

Stationära och mobila program kan använda en inbäddad webbkontroll, eller en systemwebbläsare, för autentisering. Följande diagram visar hur en skrivbords- eller mobilapp använder Microsofts autentiseringsbibliotek (MSAL) för att hämta åtkomsttoken och anropa webb-API:er.

![Skrivbordsapp hur det verkar vara](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL använder en webbläsare för att hämta token. Precis som med webbappar delegeras autentisering till Azure AD.

Eftersom Azure AD sparar samma identitetscookie i webbläsaren som för webbappar, om den inbyggda eller mobilappen använder systembläddraren kommer den omedelbart att få SSO med motsvarande webbapp.

Som standard använder MSAL systemwebbläsaren. Undantaget är .NET Framework-skrivbordsprogram där en inbäddad kontroll används för att ge en mer integrerad användarupplevelse.

## <a name="next-steps"></a>Nästa steg

* Se [microsoft identity platform developer glossary](developer-glossary.md) för att bekanta dig med vanliga termer.
* Se [Autentiseringsflöden och appscenarier](authentication-flows-app-scenarios.md) om du vill veta mer om andra scenarier för autentisering av användare som stöds av Microsofts identitetsplattform.
* Mer information om Microsoft-bibliotek finns i [MSAL-bibliotek](msal-overview.md) som hjälper dig att utveckla program som fungerar med Microsoft-konton, Azure AD-konton och Azure AD B2C-användare i en enda, strömlinjeformad programmeringsmodell.
* Mer information om hur du konfigurerar autentisering för apptjänstappen finns i [Integrera apptjänsten med Microsofts identitetsplattform.](/azure/app-service/configure-authentication-provider-aad)
