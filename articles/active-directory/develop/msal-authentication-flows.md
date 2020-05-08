---
title: Flöden för MSAL-autentisering | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om de autentiserings flöden och-anslag som används av Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: db9937d87692a1221d72bd27cfd653d803b9a1c6
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883251"
---
# <a name="authentication-flows"></a>Autentiserings flöden

I den här artikeln beskrivs de olika autentiserings flöden som tillhandahålls av Microsoft Authentication Library (MSAL).  Dessa flöden kan användas i olika program scenarier.

| Flöde | Beskrivning | Används i|  
| ---- | ----------- | ------- | 
| [Ej](#interactive) | Hämtar token via en interaktiv process som efterfrågar användaren om autentiseringsuppgifter via en webbläsare eller popup-fönster. | [Skrivbordsappar](scenario-desktop-overview.md), [mobilappar](scenario-mobile-overview.md) |
| [Implicit beviljande](#implicit-grant) | Tillåter appen att hämta tokens utan att säkerhetskopiera autentiseringsuppgifter för backend-servern. Detta gör att appen kan logga in användaren, underhålla sessionen och hämta token till andra webb-API: er i klientens JavaScript-kod.| [Enkels Ides program (SPA)](scenario-spa-overview.md) |
| [Authorization code (Auktoriseringskod)](#authorization-code) | Används i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. På så sätt kan du lägga till inloggnings-och API-åtkomst till dina mobila och Station ära appar. | [Skrivbordsappar](scenario-desktop-overview.md), [mobilappar](scenario-mobile-overview.md), [Web Apps](scenario-web-app-call-api-overview.md) | 
| [På uppdrag av](#on-behalf-of) | Ett program anropar en tjänst eller ett webb-API, som i sin tur måste anropa en annan tjänst eller ett webb-API. Tanken är att sprida den delegerade användar identiteten och behörigheterna via begär ande kedjan. | [Webb-API:er](scenario-web-api-call-api-overview.md) |
| [Klientautentiseringsuppgifter](#client-credentials) | Gör att du kan komma åt webb värd resurser genom att använda identiteten för ett program. Används ofta för server-till-Server-interaktioner som måste köras i bakgrunden, utan omedelbar interaktion med en användare. | [Daemon-appar](scenario-daemon-overview.md) |
| [Enhets kod](#device-code) | Tillåter att användare loggar in på inmatade enheter, till exempel en smart TV, IoT-enhet eller skrivare. | [Desktop/Mobile-appar](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Integrerad Windows-autentisering](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Gör det möjligt för program på domän-eller Azure Active Directory (Azure AD) anslutna datorer att hämta en token tyst (utan någon användar GRÄNSSNITTs interaktion från användaren).| [Desktop/Mobile-appar](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Användarnamn/lösenord](scenario-desktop-acquire-token.md#username-and-password) | Gör att ett program kan logga in användaren genom att direkt hantera lösen ordet. Det här flödet rekommenderas inte. | [Desktop/Mobile-appar](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hur varje flöde avger tokens och koder
 
Beroende på hur din klient har skapats kan den använda en (eller flera) av de autentiserings flöden som stöds av Microsoft Identity Platform.  Dessa flöden kan skapa en mängd olika tokens (id_tokens, uppdatera tokens, åtkomsttoken) och auktoriseringsregler, och kräver olika token för att de ska fungera. Det här diagrammet innehåller en översikt:
 
|Flöde | Innebär | id_token | åtkomsttoken | uppdatera token | auktoriseringskod | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Flöde för auktoriseringskod](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicit flöde](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrid OIDC-flöde](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Uppdatera token-inlösen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | uppdatera token | x | x | x| |
|[On-Behalf-Of-flöde](v2-oauth2-on-behalf-of-flow.md) | åtkomsttoken| x| x| x| |
|[Enhets kod flöde](v2-oauth2-device-code.md) | | x| x| x| |
|[Klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) | | | x (endast app-only)| | |
 
Token som utfärdas via det implicita läget har en längd begränsning på grund av att de skickas tillbaka till webbläsaren via URL `response_mode` : `query` en `fragment`(där är eller).  Vissa webbläsare har en gräns för storleken på URL: en som kan placeras i webbläsarens fält och inte fungerar när den är för lång.  Detta innebär att dessa tokens inte har eller `groups` `wids` är anspråk.

## <a name="interactive"></a>Interaktiv

MSAL stöder möjligheten att interaktivt fråga användaren om sina autentiseringsuppgifter för att logga in och hämta en token genom att använda dessa autentiseringsuppgifter.

![Diagram över interaktivt flöde](media/msal-authentication-flows/interactive.png)

Mer information om hur du använder MSAL.NET för att interaktivt Hämta token på vissa plattformar finns i:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Universell Windows-plattform](msal-net-uwp-considerations.md)

Mer information om interaktiva anrop i MSAL. js finns i [fråga om beteende i interaktiva begär anden i MSAL. js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicit beviljande

MSAL stöder det [implicita tilldelnings flödet för OAuth 2](v2-oauth2-implicit-grant-flow.md), vilket gör att appen kan hämta tokens från Microsoft Identity Platform utan att utföra en server för autentiseringsuppgifter för backend-servern. Detta gör att appen kan logga in användaren, underhålla sessionen och hämta token till andra webb-API: er i klientens JavaScript-kod.

![Diagram över implicit beviljande av flöde](media/msal-authentication-flows/implicit-grant.svg)

Många moderna webb program skapas som program på klient sidan, med en sida, skrivna med hjälp av Java Script eller ett SPA-ramverk, till exempel vinkel, Vue. js och reagerar. js. Dessa program körs i en webbläsare och har olika egenskaper för autentisering än traditionella webb program på Server sidan. Microsoft Identity Platform gör det möjligt för program att logga in användare och hämta token för att få åtkomst till backend-tjänster eller webb-API: er med hjälp av det implicita tilldelnings flödet. Det implicita flödet gör att programmet kan hämta ID-token för att representera den autentiserade användaren och även få åtkomst till tokens som krävs för att anropa skyddade API: er.

Det här autentiseringsschemat omfattar inte program scenarier som använder plattforms oberoende JavaScript-ramverk, till exempel Electron och reagerar-Native, eftersom de kräver ytterligare funktioner för interaktion med de ursprungliga plattformarna.

## <a name="authorization-code"></a>Authorization code (Auktoriseringskod)

MSAL stöder [utfärdande av OAuth 2-auktoriseringskod](v2-oauth2-auth-code-flow.md). Det här bidraget kan användas i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. På så sätt kan du lägga till inloggnings-och API-åtkomst till dina mobila och Station ära appar. 

När användarna loggar in på webb program (webbplatser) får webb programmet en auktoriseringskod.  Auktoriseringskod löses för att hämta en token för att anropa webb-API: er. I ASP.NET och ASP.NET Core webbappar `AcquireTokenByAuthorizationCode` är det enda målet att lägga till en token i token cache. Token kan sedan användas av programmet (vanligt vis i kontrollanter som bara får en token för ett API med hjälp `AcquireTokenSilent`av).

![Diagram över flöde för auktoriseringskod](media/msal-authentication-flows/authorization-code.png)

I föregående diagram är programmet:

1. Begär en auktoriseringskod som löses in för en åtkomsttoken.
2. Använder åtkomsttoken för att anropa ett webb-API.

### <a name="considerations"></a>Överväganden

- Du kan bara använda auktoriseringskod en gång för att lösa in en token. Försök inte att hämta en token flera gånger med samma auktoriseringskod (den uttryckligen förbjuds av protokoll standard specifikationen). Om du löser in koden flera gånger avsiktligt, eller om du inte är medveten om att ett ramverk också gör det, får du följande fel meddelande:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Om du skriver ett ASP.NET-eller ASP.NET Core-program kan detta inträffa om du inte talar om för ramverket att du redan har löst in auktoriserings koden. För detta måste du anropa `context.HandleCodeRedemption()` metoden för `AuthorizationCodeReceived` händelse hanteraren.

- Undvik att dela åtkomsttoken med ASP.NET, vilket kan förhindra att ett stegvist godkännande sker korrekt. Mer information finns i avsnittet om [problem #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>På uppdrag av

MSAL har stöd för [OAuth 2 på uppdrag av autentiseringspaket](v2-oauth2-on-behalf-of-flow.md).  Det här flödet används när ett program anropar en tjänst eller ett webb-API, vilket i sin tur måste anropa en annan tjänst eller ett webb-API. Tanken är att sprida den delegerade användar identiteten och behörigheterna via begär ande kedjan. För att mellanskikts tjänsten ska kunna göra autentiserade begär anden till den underordnade tjänsten måste den skydda en åtkomsttoken från Microsoft Identity Platform, å användarens vägnar.

![Diagram över flöde på samma vägnar](media/msal-authentication-flows/on-behalf-of.png)

I diagrammet ovan:

1. Programmet hämtar en åtkomsttoken för webb-API: et.
2. En klient (webb-, skriv bords-, mobil-eller Enkels Ides program) anropar ett skyddat webb-API och lägger till åtkomsttoken som en Bearer-token i HTTP-begärans Authentication-huvud. Webb-API: et autentiserar användaren.
3. När klienten anropar webb-API: et begär webb-API en annan token för användaren.  
4. Det skyddade webb-API: et använder denna token för att anropa ett underordnat webb-API för användaren.  Webb-API: et kan också senare begära token för andra underordnade API: er (men fortfarande på uppdrag av samma användare).

## <a name="client-credentials"></a>Klientautentiseringsuppgifter

MSAL stöder [flödet för OAuth 2-klientens autentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md). Med det här flödet kan du komma åt webb värd resurser genom att använda identiteten för ett program. Den här typen av tilldelning används ofta för server-till-Server-interaktioner som måste köras i bakgrunden, utan omedelbar interaktion med en användare. Dessa typer av program kallas ofta för daemon eller tjänst konton. 

Med det beviljande flödet för klientautentiseringsuppgifter tillåts en webb tjänst (en konfidentiell klient) att använda sina egna autentiseringsuppgifter, i stället för att personifiera en användare, för att autentisera vid anrop till en annan webb tjänst. I det här scenariot är klienten vanligt vis en webb tjänst på mellan nivå, en daemon-tjänst eller en webbplats. För en högre säkerhets nivå tillåter Microsofts identitets plattform också att anrops tjänsten använder ett certifikat (i stället för en delad hemlighet) som autentiseringsuppgift.

> [!NOTE]
> Det konfidentiella klient flödet är inte tillgängligt på de mobila plattformarna (UWP, Xamarin. iOS och Xamarin. Android), eftersom dessa endast stöder offentliga klient program. Offentliga klient program vet inte hur man kan bevisa programmets identitet för identitets leverantören. En säker anslutning kan uppnås på webbappen eller webb-API-backend genom att distribuera ett certifikat.

MSAL.NET stöder två typer av klientautentiseringsuppgifter. Dessa klientautentiseringsuppgifter måste registreras med Azure AD. Autentiseringsuppgifterna skickas till konstruktörerna för det konfidentiella klient programmet i din kod.

### <a name="application-secrets"></a>Program hemligheter

![Diagram över konfidentiell klient med lösen ord](media/msal-authentication-flows/confidential-client-password.png)

I föregående diagram är programmet:

1. Hämtar en token med hjälp av autentiseringsuppgifter för program hemlighet eller lösen ord.
2. Använder token för att göra begär Anden för resursen.

### <a name="certificates"></a>Certifikat

![Diagram över konfidentiell klient med certifikat](media/msal-authentication-flows/confidential-client-certificate.png)

I föregående diagram är programmet:

1. Hämtar en token med hjälp av autentiseringsuppgifter för certifikatet.
2. Använder token för att göra begär Anden för resursen.

Dessa klientautentiseringsuppgifter måste vara:
- Registrerad med Azure AD.
- Skickas till det konfidentiella klient programmet i din kod.

## <a name="device-code"></a>Enhets kod

MSAL stöder [OAuth 2-enhetens kod flöde](v2-oauth2-device-code.md), vilket gör att användarna kan logga in på indata-begränsade enheter, till exempel en smart TV, IoT-enhet eller skrivare. Interaktiv autentisering med Azure AD kräver en webbläsare. Med enhets kod flödet kan användaren använda en annan enhet (till exempel en annan dator eller en mobil telefon) för att logga in interaktivt, där enheten eller operativ systemet inte tillhandahåller någon webbläsare.

Genom att använda enhets kod flödet hämtar programmet token via en två stegs process som är särskilt utformad för dessa enheter eller operativ system. Exempel på sådana program är sådana som körs på IoT-enheter eller kommando rads verktyg (CLI). 

![Diagram över enhets kod flöde](media/msal-authentication-flows/device-code.png)

I diagrammet ovan:

1. När användarautentisering krävs ger appen en kod och ber användaren att använda en annan enhet (till exempel en Internet-ansluten smartphone) för att gå till en URL (t. ex. `https://microsoft.com/devicelogin`). Användaren uppmanas sedan att ange koden och fortsätter med en normal autentisering, inklusive medgivande-prompter och Multi-Factor Authentication om det behövs.

2. Vid lyckad autentisering tar kommando rads appen emot de begärda token via en återställnings kanal och använder dem för att utföra de webb-API-anrop som krävs.

### <a name="constraints"></a>Villkor

- Enhets kod flödet är bara tillgängligt för offentliga klient program.
- Den auktoritet som skickades när du konstruerade det offentliga klient programmet måste vara något av följande:
  - Tenant (av formuläret `https://login.microsoftonline.com/{tenant}/` där `{tenant}` är antingen det GUID som representerar klient-ID: t eller en domän som är associerad med klienten).
  - För arbets-och skol konton (`https://login.microsoftonline.com/organizations/`).
- Microsoft personliga konton stöds ännu inte av Azure AD v 2.0-slut punkten (du kan inte `/common` använda `/consumers` -klient organisationer).

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

MSAL stöder integrerad Windows-autentisering (IWA) för Station ära eller mobila program som körs på en domänansluten eller Azure AD-ansluten Windows-dator. Med hjälp av IWA kan dessa program hämta en token tyst (utan någon användar GRÄNSSNITTs interaktion från användaren). 

![Diagram över integrerad Windows-autentisering](media/msal-authentication-flows/integrated-windows-authentication.png)

I föregående diagram är programmet:

1. Hämtar en token med hjälp av integrerad Windows-autentisering.
2. Använder token för att göra begär Anden för resursen.

### <a name="constraints"></a>Villkor

IWA stöder enbart federerade användare, vilket innebär att användare som skapats i Active Directory och som backas upp av Azure AD. Användare som skapats direkt i Azure AD, utan Active Directory säkerhets kopiering (hanterade användare) kan inte använda det här autentiseringsschemat. Den här begränsningen påverkar inte [flödet av användar namn/lösen ord](#usernamepassword).

IWA är avsedd för appar som är skrivna för .NET Framework, .NET Core och Universell Windows-plattform plattformar.

IWA kringgår inte Multi-Factor Authentication. Om Multi-Factor Authentication har kon figurer ATS kan IWA Miss förväntat om en Multi-Factor Authentication-utmaning krävs. Multi-Factor Authentication kräver användar interaktion.

Du styr inte när identitets leverantören begär tvåfaktorautentisering som ska utföras. Klient organisationens administratör gör. Normalt krävs tvåfaktorautentisering när du loggar in från ett annat land, när du inte är ansluten via VPN till ett företags nätverk och ibland även när du är ansluten via VPN. Azure AD använder AI för att kontinuerligt lära sig om tvåfaktorautentisering krävs. Om IWA Miss lyckas bör du gå tillbaka till en [interaktiv användar varning] (#interactive).

Den auktoritet som skickades när du konstruerade det offentliga klient programmet måste vara något av följande:
- Tenant (av formuläret `https://login.microsoftonline.com/{tenant}/` där `tenant` är antingen det GUID som representerar klient-ID: t eller en domän som är associerad med klienten).
- För arbets-och skol konton (`https://login.microsoftonline.com/organizations/`). Microsoft-personliga konton stöds inte (du kan inte `/common` använda `/consumers` eller klienter).

Eftersom IWA är ett tyst flöde måste något av följande vara sant:
- Användaren av ditt program måste tidigare ha samtyckt till att använda programmet. 
- Klient organisationens administratör måste tidigare ha samtyckt till alla användare i klienten för att kunna använda programmet.

Det innebär att något av följande stämmer:
- Du som utvecklare har valt att **bevilja** Azure Portal själv.
- En innehavaradministratör har valt **beviljande/återkalla administratörs medgivande för {klient domän}** på fliken **API-behörigheter** i registreringen för programmet (se [lägga till behörigheter för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Du har angett ett sätt för användare att samtycka till programmet (se [begära individuell användar medgivande](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Du har angett ett sätt för klient administratören att godkänna programmet (se [administrativt medgivande](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

IWA-flödet har Aktiver ATS för .NET Desktop-, .NET Core-och Windows Universal Platform-appar. På .NET Core måste du ange användar namnet för IWA, eftersom .NET Core inte kan hämta användar namn från operativ systemet.
  
Mer information om medgivande finns i [v 2.0-behörigheter och medgivande](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Användarnamn/lösenord

MSAL stöder [tilldelning av autentiseringsuppgifter för OAuth 2-resurs ägare](v2-oauth-ropc.md), vilket gör att ett program kan logga in användaren genom att direkt hantera lösen ordet. I ditt Skriv bords program kan du använda flödet för användar namn/lösen ord för att hämta en token i bakgrunden. Inget användar gränssnitt krävs när programmet används.

![Diagram över flödet för användar namn/lösen ord](media/msal-authentication-flows/username-password.png)

I föregående diagram är programmet:

1. Hämtar en token genom att skicka användar namnet och lösen ordet till identitets leverantören.
2. Anropar ett webb-API med hjälp av token.

> [!WARNING]
> Det här flödet rekommenderas inte. Den kräver en hög grad av förtroende och användar exponering.  Du bör endast använda det här flödet när andra, säkrare och flöden inte kan användas. Mer information finns i [Vad är lösningen på det växande problemet med lösen ord?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Det prioriterade flödet för att hämta en token tyst på domänanslutna Windows-datorer är [integrerad Windows-autentisering](#integrated-windows-authentication). Annars kan du också använda [enhets kod flödet](#device-code).

Även om detta är användbart i vissa fall (DevOps-scenarier), om du vill använda användar namn/lösen ord i interaktiva scenarier där du anger ditt eget användar gränssnitt, kan du försöka undvika det. Med användar namn/lösen ord:
- Användare som behöver utföra Multi-Factor Authentication kan inte logga in (eftersom det inte finns någon interaktion).
- Användare kan inte utföra enkel inloggning.

### <a name="constraints"></a>Villkor

Utöver de [integrerade begränsningarna för Windows-autentisering](#integrated-windows-authentication)gäller även följande begränsningar:

- Användar namnet/lösen ordet är inte kompatibelt med villkorlig åtkomst och Multi-Factor Authentication. Det innebär att om din app körs i en Azure AD-klient där klient organisationen kräver Multi-Factor Authentication, kan du inte använda det här flödet. Många organisationer gör det.
- Det fungerar bara för arbets-och skol konton (inte Microsoft-konton).
- Flödet är tillgängligt på .NET Desktop och .NET Core, men inte på Universell Windows-plattform.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C information

Mer information om hur du använder ROPC i MSAL.NET och Azure AD B2C finns i [using ROPC with Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).
