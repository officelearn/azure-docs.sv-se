---
title: Autentisering i Microsoft Identity Platform | Azure
description: Lär dig grunderna i autentisering i Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: d979745d9b5bb65bd08f69db86801156de2a489d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161749"
---
# <a name="authentication-basics"></a>Grundläggande om autentisering

Den här artikeln beskriver många av de autentiseringsmetoder du behöver känna till för att skapa skyddade webbappar, webb-API: er eller appar som anropar skyddade webb-API: er. Om du ser en term som du inte är bekant med kan du prova vår [ord lista](developer-glossary.md) eller våra [Microsoft Identity Platform-videor](identity-videos.md) som beskriver grundläggande begrepp.

## <a name="authentication-vs-authorization"></a>Autentisering kontra auktorisering

**Autentisering** är en process för att bevisa att du är den som du säger. Autentisering förkortas ibland AuthN. Microsoft Identity Platform implementerar [OpenID Connect](https://openid.net/connect/) -protokollet för att hantera autentisering.

**Auktorisering** innebär att ge en autentiserad part behörighet att göra något. Den anger vilka data du får åtkomst till och vad du kan göra med dessa data. Auktorisering förkortas ibland AuthZ. Microsoft Identity Platform implementerar [OAuth 2,0](https://oauth.net/2/) -protokollet för hantering av auktorisering.

I stället för att skapa appar som var och en upprätthåller sin egen användar namn och lösen ords information, vilket innebär en hög administrativ börda när du behöver lägga till eller ta bort användare över flera appar, kan appar delegera det ansvaret till en centraliserad identitets leverantör.

Azure Active Directory (Azure AD) är en centraliserad identitets leverantör i molnet. Genom att delegera autentisering och auktorisering till det möjliggör scenarier som villkorliga åtkomst principer som kräver att en användare befinner sig på en speciell plats, användningen av Multi-Factor Authentication, samt att göra det möjligt för en användare att logga in en gång och sedan loggas in automatiskt till alla webbappar som delar samma centrala katalog. Den här funktionen kallas **enkel inloggning (SSO)**.

Microsoft Identity Platform fören klar autentisering och auktorisering för programutvecklare genom att tillhandahålla identitet som en tjänst, med stöd för bransch standard protokoll som OAuth 2,0 och OpenID Connect, samt bibliotek med öppen källkod för olika plattformar som hjälper dig att snabbt komma igång med att koda. Det gör det möjligt för utvecklare att skapa program som loggar in alla Microsoft-identiteter, Hämta token för att anropa [Microsoft Graph](https://developer.microsoft.com/graph/), andra Microsoft API: er eller API: er som utvecklare har skapat. Mer information finns i [utvecklingen av Microsoft Identity Platform](about-microsoft-identity-platform.md).

## <a name="security-tokens"></a>Säkerhetstoken

En centraliserad identitets leverantör är särskilt viktig för appar som har användare som finns i hela världen och som inte nödvändigt vis loggar in från företagets nätverk. Microsoft Identity Platform autentiserar användare och ger säkerhetstoken, till exempel [åtkomsttoken](developer-glossary.md#access-token), [uppdateringstoken](developer-glossary.md#refresh-token)och [ID-token](developer-glossary.md#id-token), som gör att ett [klient program](developer-glossary.md#client-application) kan komma åt skyddade resurser på en [resurs Server](developer-glossary.md#resource-server).

En **åtkomsttoken är en säkerhetstoken som** utfärdas av en Authorization Server. Den innehåller information om användaren och appen som token är avsedd för. som kan användas för att få åtkomst till webb-API: er och andra skyddade resurser. Mer information om hur Microsoft Identity Platform utfärdar åtkomst-token finns i [åtkomsttoken](access-tokens.md).

Åtkomst-token är bara giltiga under en kort tids period, så att auktoriseringsdata ibland utfärdar en **uppdateringstoken** på samma gång som åtkomsttoken utfärdas. Klient programmet kan sedan byta ut denna uppdateringstoken för en ny åtkomsttoken vid behov. Mer information om hur Microsoft Identity Platform använder Refresh tokens för att återkalla behörigheter finns i återkalla [token](access-tokens.md#token-revocation).

**ID-token** skickas till klient programmet som en del av ett [OpenID Connect](v2-protocols-oidc.md) -flöde. De kan skickas på sidan eller i stället för en åtkomsttoken och används av klienten för att autentisera användaren. Mer information om hur Microsoft Identity Platform utfärdar ID-token finns i [ID-token](id-tokens.md).

### <a name="validating-security-tokens"></a>Verifierar säkerhetstoken

Det är upp till appen för vilken token har skapats, webbappen som signerade användaren eller webb-API: et som anropades för att validera token. Token signeras av säkerhetstokentjänst (STS) med en privat nyckel. STS publicerar motsvarande offentliga nyckel. För att validera en token verifierar appen signaturen med hjälp av den offentliga STS-nyckeln för att verifiera att signaturen skapades med hjälp av den privata nyckeln.

Tokens är bara giltiga under en begränsad tid. STS tillhandahåller vanligt vis ett par med token:

* En åtkomsttoken för att komma åt programmet eller den skyddade resursen, och
* En uppdateringstoken som används för att uppdatera åtkomst-token när åtkomsttoken ligger nära att gå ut.

Åtkomsttoken skickas till ett webb-API som Bearer-token i `Authorization` huvudet. En app kan tillhandahålla en uppdateringstoken till STS, och om användarens åtkomst till appen inte har återkallats kommer den att få tillbaka en ny åtkomsttoken och en ny uppdateringstoken. Detta är hur scenariot för någon som lämnar företaget hanteras. När STS tar emot uppdateringstoken, utfärdar den ingen annan giltig åtkomsttoken om användaren inte längre är auktoriserad.

### <a name="json-web-tokens-jwts-and-claims"></a>JSON-webbtoken (JWTs) och anspråk

Microsoft Identity Platform implementerar säkerhetstoken som JSON-webbtoken (JWTs) som innehåller anspråk.

Ett [anspråk](developer-glossary.md#claim) ger intyg om en entitet, till exempel ett klient program eller en [resurs ägare](developer-glossary.md#resource-owner), till en annan entitet, till exempel en resurs Server.

Anspråk är namn/värde-par som vidarebefordrar fakta om ämnets token. Ett anspråk kan till exempel innehålla fakta om säkerhets objekt som autentiserats av [auktoriseringsservern](developer-glossary.md#authorization-server). De anspråk som förekommer i en specifik token är beroende av många saker, inklusive typen av token, vilken typ av autentiseringsuppgift som används för att autentisera ämnet, program konfigurationen och så vidare.

Program kan använda anspråk för olika uppgifter, till exempel:

* Token verifieras
* Identifierar innehavarens token-innehavare
* Visar användar information
* Avgöra ämnets auktorisering

Ett anspråk består av nyckel/värde-par som innehåller information som:

* Säkerhetstoken som genererade token
* Datum när token genererades
* Ämne (till exempel användare--utom för daemon)
* Mål grupp, som är appen för vilken token genererades
* App (klienten) som bad om token. Om det gäller Web Apps kan detta vara samma som mål gruppen

Mer information om hur Microsoft Identity Platform implementerar token och anspråks information finns [i åtkomsttoken](access-tokens.md) och [ID-token](id-tokens.md).

### <a name="how-each-flow-emits-tokens-and-codes"></a>Hur varje flöde avger tokens och koder

Beroende på hur din klient har skapats kan den använda en (eller flera) av de autentiserings flöden som stöds av Microsoft Identity Platform. Dessa flöden kan skapa en mängd olika tokens (id_tokens, uppdatera tokens, åtkomsttoken) och auktoriseringsregler, och kräver olika token för att de ska fungera. Det här diagrammet innehåller en översikt:

|Flöde | Innebär | id_token | åtkomsttoken | uppdatera token | auktoriseringskod |
|-----|----------|----------|--------------|---------------|--------------------|
|[Flöde för auktoriseringskod](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Implicit flöde](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrid OIDC-flöde](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Uppdatera token-inlösen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | uppdatera token | x | x | x| |
|[On-Behalf-Of-flöde](v2-oauth2-on-behalf-of-flow.md) | åtkomsttoken| x| x| x| |
|[Klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) | | | x (endast app-only)| | |

Token som utfärdas via det implicita läget har en längd begränsning på grund av att de skickas tillbaka till webbläsaren via URL `response_mode` : `query` en `fragment`(där är eller).  Vissa webbläsare har en gräns för storleken på URL: en som kan placeras i webbläsarens fält och inte fungerar när den är för lång.  Detta innebär att dessa tokens inte har eller `groups` `wids` är anspråk.

## <a name="tenants"></a>Klientorganisationer

En moln identitets leverantör hanterar många organisationer. För att användarna ska vara åtskilda från olika organisationer är Azure AD partitionerad i klienter, med en klient organisation per organisation.

Klienter håller reda på användare och deras associerade appar. Microsoft Identity Platform stöder även användare som loggar in med personliga Microsoft-konton.

Azure AD tillhandahåller också Azure Active Directory B2C så att organisationer kan logga in användare, vanligt vis kunder, med sociala identiteter som ett Google-konto. Mer information finns i [Azure Active Directory B2C-dokumentationen](https://docs.microsoft.com/azure/active-directory-b2c) .

Nu när du har en översikt över grunderna kan du läsa om hur du kan förstå Identity app-modellen och API: et, lära dig hur du arbetar i Microsoft Identity Platform och få länkar till detaljerad information om vanliga scenarier som Microsoft Identity Platform stöder.

## <a name="application-model"></a>Programmodell

Program kan logga in användare själva eller delegera inloggning till en identitets leverantör. Se [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md) för att lära dig om inloggnings scenarier som stöds av Microsoft Identity Platform.

För en identitets leverantör att veta att en användare har åtkomst till en viss app måste både användaren och programmet vara registrerat hos identitets leverantören. När du registrerar ditt program med Azure AD ger du en identitets konfiguration för ditt program som gör det möjligt att integrera med Microsoft Identity Platform. Genom att registrera appen kan du också:

* Anpassa anpassningen av programmet i dialog rutan för inloggning. Detta är viktigt eftersom det är den första upplevelsen som en användare kommer att ha med din app.
* Bestäm om du bara vill låta användarna logga in om de tillhör din organisation. Detta är ett enda klient program. Eller Tillåt användare att logga in med ett arbets-eller skol konto. Detta är ett program med flera innehavare. Du kan också tillåta personliga Microsoft-konton eller ett socialt konto från LinkedIn, Google och så vidare.
* Begär omfångs behörigheter. Du kan till exempel begära kommandot "User. Read", som ger behörighet att läsa profilen för den inloggade användaren.
* Definiera omfattningar som definierar åtkomst till ditt webb-API. När en app vill komma åt ditt API måste du vanligt vis begära behörigheter till de omfattningar som du definierar.
* Dela en hemlighet med Microsoft Identity Platform som visar appens identitet.  Detta är relevant i de fall där appen är ett konfidentiellt klient program. Ett konfidentiellt klient program är ett program som kan lagra autentiseringsuppgifter på ett säkert sätt. De kräver en betrodd backend-server för att lagra autentiseringsuppgifterna.

När programmet har registrerats får du en unik identifierare som appen delar med Microsoft Identity Platform när den begär token. Om appen är ett [konfidentiellt klient program](developer-glossary.md#client-application), kommer den också att dela hemligheten eller den offentliga nyckeln * – beroende på om certifikat eller hemligheter användes.

Microsoft Identity Platform representerar program som använder en modell som uppfyller två huvud funktioner:

* Identifiera appen med de autentiseringsprotokoll som stöds
* Ange alla identifierare, URL: er, hemligheter och relaterad information som behövs för att autentisera

Microsoft Identity Platform:

* Innehåller alla data som krävs för att stödja autentisering vid körning
* Innehåller alla data för att bestämma vilka resurser en app kan behöva komma åt och under vilka omständigheter en specifik begäran ska vara uppfylld
* Innehåller en infrastruktur för att implementera app-etablering i appens utvecklares klient organisation och till en annan Azure AD-klient
* Hanterar användarens medgivande under Tokenbegäran och underlättar dynamisk etablering av appar över klient organisationer

Samtycke är en resurs ägare som beviljar auktorisering för ett klient program att komma åt skyddade resurser, under specifika behörigheter för resurs ägarens räkning. Microsoft Identity Platform:

* Möjliggör för användare och administratörer att dynamiskt bevilja eller neka medgivande för appen att få åtkomst till resurser för deras räkning.
* Möjliggör för administratörer att i slutänden bestämma vilka appar som tillåts göra vad och vilka användare som kan använda specifika appar samt hur åtkomsten till katalogresurserna går till.

I Microsoft Identity Platform beskriver ett [program objekt](developer-glossary.md#application-object) ett program. Vid distributions tillfället använder Microsoft Identity Platform programobjektet som en skiss för att skapa ett [huvud namn för tjänsten](developer-glossary.md#service-principal-object)som representerar en konkret instans av ett program i en katalog eller klient organisation. Tjänstens huvud namn definierar vad appen faktiskt kan göra i en speciell mål katalog, som kan använda den, vilka resurser den har åtkomst till och så vidare. Microsoft Identity Platform skapar ett huvud namn för tjänsten från ett program objekt via **medgivande**.

Följande diagram visar ett förenklat etablerings flöde för Microsoft Identity Platform som drivs av medgivande. Den visar två klienter: *A* och *B*.

* *Klient organisation A* äger programmet.
* *Klient B* instansierar programmet via ett huvud namn för tjänsten.

![Förenklat etableringsflöde som drivs av medgivande](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

I det här etableringsflödet sker följande:

1. En användare från klient B försöker logga in med appen. slut punkten för auktorisering begär en token för programmet.
1. Autentiseringsuppgifterna för användaren förvärvas och verifieras för autentisering.
1. Användaren uppmanas att ange medgivande för appen för att få åtkomst till klient B.
1. Microsoft Identity Platform använder programobjektet i klient organisationen som en skiss för att skapa ett huvud namn för tjänsten i klient B.
1. Användaren får en begärd token.

Du kan upprepa den här processen för ytterligare klienter. Klient organisation A behåller skissen för appen (program objekt). Användare och administratörer för alla andra innehavare där appen ges tillåtelse att behålla kontrollen över vad programmet tillåts att göra via motsvarande tjänst huvud objekt i varje klient organisation. Mer information finns i [program-och tjänst huvud objekt i Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-microsoft-identity-platform"></a>Inloggnings flöde för webbappar med Microsoft Identity Platform

När en användare navigerar i webbläsaren till en webbapp händer följande:

* Webb programmet avgör om användaren är autentiserad.
* Om användaren inte har autentiserats delegerar webbappen till Azure AD för att logga in användaren. Inloggningen är kompatibel med organisationens princip, vilket kan innebära att användaren anger sina autentiseringsuppgifter, använder Multi-Factor Authentication eller inte använder ett lösen ord alls (till exempel med hjälp av Windows Hello).
* Användaren uppmanas att godkänna åtkomsten som klient appen behöver. Detta är anledningen till att klient program måste registreras med Azure AD, så att Microsoft Identity Platform kan leverera tokens som representerar den åtkomst som användaren har samtyckt till.

När användaren har autentiserats:

* Microsoft Identity Platform skickar en token till webbappen.
* En cookie sparas som är kopplad till Azure AD-domänen och som innehåller användarens identitet i webbläsarens jar-jar. Nästa gång en app använder webbläsaren för att navigera till Microsoft Identity Platform Authorization-slutpunkten presenterar webbläsaren cookien så att användaren inte behöver logga in igen. Detta är också det sätt på vilket SSO uppnås. Cookien skapas av Azure AD och kan bara tolkas av Azure AD.
* Webbappen validerar sedan token. Om verifieringen lyckas visas den skyddade sidan i webbappen och en sessions-cookie sparas i webbläsarens Jar-Jar-form. När användaren navigerar till en annan sida vet webbappen att användaren är autentiserad baserat på sessionens cookie.

Följande sekvensdiagram sammanfattar interaktionen:

![autentisering av webbapp](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Hur en webbapp fastställer om användaren är autentiserad

Utvecklare av webbappar kan ange om alla eller endast vissa sidor kräver autentisering. I ASP.NET/ASP.NET Core görs detta genom att `[Authorize]` attributet läggs till i styrenhets åtgärderna.

Det här attributet gör att ASP.NET söker efter en sessions-cookie som innehåller användarens identitet. Om en cookie inte finns omdirigerar ASP.NET autentiseringen till den angivna identitets leverantören. Om identitets leverantören är Azure AD omdirigerar webbappen autentiseringen till `https://login.microsoftonline.com`, som visar en dialog ruta för inloggning.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Hur en webbapp delegerar inloggning till Microsoft Identity Platform och erhåller en token

Användarautentisering sker via webbläsaren. OpenID-protokollet använder vanliga HTTP-protokoll meddelanden.

* Webbappen skickar en HTTP 302 (Omdirigerad) till webbläsaren för att använda Microsoft Identity Platform.
* När användaren autentiseras skickar Microsoft Identity Platform token till webbappen med hjälp av en omdirigering via webbläsaren.
* Omdirigeringen tillhandahålls av webb programmet i form av en omdirigerings-URI. Den här omdirigerings-URI: n har registrerats med objektet Azure AD-program. Det kan finnas flera omdirigerings-URI: er eftersom programmet kan distribueras på flera URL: er. Webbappen måste också ange den omdirigerings-URI som ska användas.
* Azure AD kontrollerar att den omdirigerings-URI som skickas av webbappen är en av de registrerade omdirigerings-URI: erna för appen.

## <a name="desktop-and-mobile-app-sign-in-flow-with-microsoft-identity-platform"></a>Skriv bords-och mobilappar med Microsoft Identity Platform

Flödet som beskrivs ovan gäller, med små skillnader, för Station ära och mobila program.

Skriv bords-och mobil program kan använda en inbäddad webb kontroll eller en system webbläsare för autentisering. Följande diagram visar hur en stationär eller mobilapp använder Microsoft Authentication Library (MSAL) för att hämta åtkomsttoken och anropa webb-API: er.

![Desktop-appen hur det verkar vara](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL använder en webbläsare för att hämta tokens. Precis som med Web Apps delegeras autentiseringen till Microsoft Identity Platform.

Eftersom Azure AD sparar samma identitets-cookie i webbläsaren som den gör för webbappar, kommer den interna eller mobilappen att använda system läsaren omedelbart för att få enkel inloggning med motsvarande webbapp.

Som standard använder MSAL system webbläsare. Undantaget är .NET Framework Skriv bords program där en inbäddad kontroll används för att ge en mer integrerad användar upplevelse.

## <a name="next-steps"></a>Nästa steg

* Se [ord listan för Microsoft Identity Platform Developer](developer-glossary.md) för att bekanta dig med vanliga villkor.
* Mer information om andra scenarier för autentisering av användare som stöds av Microsoft Identity Platform finns i [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md) .
* Se [MSAL-bibliotek](msal-overview.md) för att lära dig om de Microsoft-bibliotek som hjälper dig att utveckla program som fungerar med Microsoft-konton, Azure AD-konton och Azure AD B2C användare i en enda, strömlinjeformad programmerings modell.
* Mer information om hur du konfigurerar autentisering för din App Service-app finns i [integrera App Service med Microsoft Identity Platform](/azure/app-service/configure-authentication-provider-aad) .
