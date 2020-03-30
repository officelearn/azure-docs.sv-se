---
title: MSAL-autentiseringsflöden | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om autentiseringsflöden och bidrag som används av Microsoft Authentication Library (MSAL).
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
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050517"
---
# <a name="authentication-flows"></a>Autentiseringsflöden

I den här artikeln beskrivs de olika autentiseringsflöden som tillhandahålls av Microsoft Authentication Library (MSAL).  Dessa flöden kan användas i en mängd olika programscenarier.

| Flöde | Beskrivning | Används i|  
| ---- | ----------- | ------- | 
| [Interaktiva](#interactive) | Hämtar token genom en interaktiv process som uppmanar användaren att ange autentiseringsuppgifter via en webbläsare eller ett popup-fönster. | [Skrivbordsappar](scenario-desktop-overview.md), [mobilappar](scenario-mobile-overview.md) |
| [Implicit bidrag](#implicit-grant) | Gör att appen kan hämta token utan att utföra ett backend-serverautentiseringsutbyte. På så sätt kan appen logga in användaren, underhålla sessionen och hämta token till andra webb-API:er, allt inom klientens JavaScript-kod.| [Ensidiga program (SPA)](scenario-spa-overview.md) |
| [Authorization code (Auktoriseringskod)](#authorization-code) | Används i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API:er. På så sätt kan du lägga till inloggning och API-åtkomst till dina mobil- och skrivbordsappar. | [Skrivbordsappar](scenario-desktop-overview.md), [mobilappar,](scenario-mobile-overview.md) [webbappar](scenario-web-app-call-api-overview.md) | 
| [På uppdrag av](#on-behalf-of) | Ett program anropar en tjänst eller ett webb-API, som i sin tur måste anropa en annan tjänst eller webb-API. Tanken är att sprida den delegerade användaridentiteten och behörigheterna via förfråkomstkedjan. | [Webb-API:er](scenario-web-api-call-api-overview.md) |
| [Klientautentiseringsuppgifter](#client-credentials) | Gör att du kan komma åt webbaserade resurser med hjälp av ett programs identitet. Används ofta för server-till-server-interaktioner som måste köras i bakgrunden, utan omedelbar interaktion med en användare. | [Daemon-appar](scenario-daemon-overview.md) |
| [Enhetskod](#device-code) | Gör det möjligt för användare att logga in på indatabegränsade enheter, till exempel en smart-TV, IoT-enhet eller skrivare. | [Skrivbords-/mobilappar](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Integrerad Windows-autentisering](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Tillåter att program på domän eller Azure Active Directory (Azure AD) gick till datorer för att hämta en token tyst (utan gränssnittsinteraktion från användaren).| [Skrivbords-/mobilappar](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Användarnamn/lösenord](scenario-desktop-acquire-token.md#username-and-password) | Tillåter ett program att logga in användaren genom att direkt hantera sitt lösenord. Det här flödet rekommenderas inte. | [Skrivbords-/mobilappar](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hur varje flöde avger token och koder
 
Beroende på hur klienten är byggd kan den använda en (eller flera) av de autentiseringsflöden som stöds av Microsofts identitetsplattform.  Dessa flöden kan producera en mängd olika token (id_tokens, uppdatera token, åtkomsttoken) samt auktoriseringskoder och kräva olika token för att få dem att fungera. Det här diagrammet innehåller en översikt:
 
|Flöde | Kräver | id_token | åtkomsttoken | uppdatera token | auktoriseringskod | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Flöde av auktoriseringskod](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicit flöde](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrid OIDC-flöde](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Uppdatera tokeninlösen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | uppdatera token | x | x | x| |
|[On-Behalf-Of-flöde](v2-oauth2-on-behalf-of-flow.md) | åtkomsttoken| x| x| x| |
|[Flöde av enhetskod](v2-oauth2-device-code.md) | | x| x| x| |
|[Klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) | | | x (endast app)| | |
 
Tokens som utfärdas via implicit läge har en längdbegränsning på grund av `response_mode` `query` att `fragment`de skickas tillbaka till webbläsaren via webbadressen (var är eller ).  Vissa webbläsare har en gräns för storleken på webbadressen som kan placeras i webbläsarfältet och misslyckas när den är för lång.  Dessa tokens har `groups` eller `wids` gör anspråk.

## <a name="interactive"></a>Interaktiv

MSAL stöder möjligheten att interaktivt uppmana användaren att logga in och hämta en token med hjälp av dessa autentiseringsuppgifter.

![Diagram över interaktivt flöde](media/msal-authentication-flows/interactive.png)

Mer information om hur du använder MSAL.NET för att interaktivt hämta token på specifika plattformar finns i:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Universal Windows Platform](msal-net-uwp-considerations.md)

Mer information om interaktiva samtal i MSAL.js finns [i Snabbt beteende i MSAL.js interaktiva begäranden](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicit bidrag

MSAL stöder [OAuth 2 implicit beviljande flöde](v2-oauth2-implicit-grant-flow.md), vilket gör att appen för att få token från Microsoft identitetsplattform utan att utföra en back-end server autentiseringsuppgifter utbyte. På så sätt kan appen logga in användaren, underhålla sessionen och hämta token till andra webb-API:er, allt inom klientens JavaScript-kod.

![Diagram över implicit bidragsflöde](media/msal-authentication-flows/implicit-grant.svg)

Många moderna webbapplikationer är byggda som klient-side, single page-program, skrivna med javascript eller ett SPA-ramverk som Angular, Vue.js och React.js. Dessa program körs i en webbläsare och har andra autentiseringsegenskaper än traditionella webbprogram på serversidan. Microsofts identitetsplattform gör det möjligt för program med en sida att logga in användare och få token för att komma åt backend-tjänster eller webb-API:er med hjälp av det implicita bidragsflödet. Det implicita flödet gör att programmet kan hämta ID-token för att representera den autentiserade användaren och även komma åt token som behövs för att anropa skyddade API:er.

Det här autentiseringsflödet innehåller inte programscenarier som använder JavaScript-ramverk över flera plattformar, till exempel Elektron och React-Native, eftersom de kräver ytterligare funktioner för interaktion med de inbyggda plattformarna.

## <a name="authorization-code"></a>Authorization code (Auktoriseringskod)

MSAL stöder [tillståndskodsbidraget OAuth 2](v2-oauth2-auth-code-flow.md). Det här bidraget kan användas i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API:er. På så sätt kan du lägga till inloggning och API-åtkomst till dina mobil- och skrivbordsappar. 

När användare loggar in på webbprogram (webbplatser) får webbprogrammet en auktoriseringskod.  Auktoriseringskoden löses in för att hämta en token för att anropa webb-API:er. I ASP.NET och ASP.NET Core-webbappar `AcquireTokenByAuthorizationCode` är det enda målet med att lägga till en token i tokencachen. Token kan sedan användas av programmet (vanligtvis i styrenheterna, som bara `AcquireTokenSilent`får en token för ett API med hjälp av ).

![Diagram över auktoriseringskodflödet](media/msal-authentication-flows/authorization-code.png)

I föregående diagram visas följande:

1. Begär en auktoriseringskod som löses in för en åtkomsttoken.
2. Använder åtkomsttoken för att anropa ett webb-API.

### <a name="considerations"></a>Överväganden

- Du kan bara använda auktoriseringskoden en gång för att lösa in en token. Försök inte att skaffa en token flera gånger med samma auktoriseringskod (det är uttryckligen förbjudet enligt protokollstandardspecifikationen). Om du löser in koden flera gånger avsiktligt, eller om du inte är medveten om att ett ramverk också gör det åt dig, får du följande felmeddelande:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Om du skriver ett ASP.NET eller ASP.NET Core-program kan detta inträffa om du inte talar om för ramverket att du redan har löst in auktoriseringskoden. För detta måste du `context.HandleCodeRedemption()` anropa metoden `AuthorizationCodeReceived` för händelsehanteraren.

- Undvik att dela åtkomsttoken med ASP.NET, vilket kan förhindra att inkrementellt medgivande sker korrekt. Mer information finns i [problem #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>På uppdrag av

MSAL stöder [OAuth 2 för autentiseringsflödet](v2-oauth2-on-behalf-of-flow.md).  Det här flödet används när ett program anropar en tjänst eller ett webb-API, som i sin tur måste anropa en annan tjänst eller webb-API. Tanken är att sprida den delegerade användaridentiteten och behörigheterna via förfråkomstkedjan. För att tjänsten på mellannivå ska kunna göra autentiserade begäranden till tjänsten nedströms måste den skydda en åtkomsttoken från Microsofts identitetsplattform för användarens räkning.

![Diagram över flödet för den skull](media/msal-authentication-flows/on-behalf-of.png)

I diagrammet ovan:

1. Programmet hämtar en åtkomsttoken för webb-API:et.
2. En klient (webb-, skrivbords-, mobil- eller ensidigt program) anropar ett skyddat webb-API och lägger till åtkomsttoken som innehavartoken i autentiseringshuvudet för HTTP-begäran. Webb-API:et autentiserar användaren.
3. När klienten anropar webb-API:et begär webb-API:et en annan token för användarens räkning.  
4. Det skyddade webb-API:et använder den här token för att anropa ett nedströms webb-API för användarens räkning.  Webb-API:et kan också senare begära token för andra underordnade API:er (men fortfarande för samma användare).

## <a name="client-credentials"></a>Klientautentiseringsuppgifter

MSAL stöder [OAuth 2-klientautentiseringsflödet](v2-oauth2-client-creds-grant-flow.md). Med det här flödet kan du komma åt webbbaserade resurser med hjälp av ett programs identitet. Den här typen av bidrag används ofta för server-till-server-interaktioner som måste köras i bakgrunden, utan omedelbar interaktion med en användare. Dessa typer av program kallas ofta demoner eller tjänstkonton. 

Tilldelningsflödet för klientuppgifter gör det möjligt för en webbtjänst (en konfidentiell klient) att använda sina egna autentiseringsuppgifter, i stället för att utge sig för att vara en användare, för att autentisera när du anropar en annan webbtjänst. I det här fallet är klienten vanligtvis en mellannivåwebbtjänst, en daemontjänst eller en webbplats. För en högre säkerhetsnivå gör Microsofts identitetsplattform det också möjligt för den anropande tjänsten att använda ett certifikat (i stället för en delad hemlighet) som en autentiseringstjänst.

> [!NOTE]
> Det konfidentiella klientflödet är inte tillgängligt på de mobila plattformarna (UWP, Xamarin.iOS och Xamarin.Android), eftersom dessa endast stöder offentliga klientprogram. Offentliga klientprogram vet inte hur du bevisar programmets identitet för identitetsprovidern. En säker anslutning kan uppnås på webbapp eller webb-API-tillbaka slutar genom att distribuera ett certifikat.

MSAL.NET stöder två typer av klientautentiseringsuppgifter. Dessa klientuppgifter måste registreras med Azure AD. Autentiseringsuppgifterna skickas till konstruktörerna av det konfidentiella klientprogrammet i koden.

### <a name="application-secrets"></a>Ansökan hemligheter

![Diagram över konfidentiell klient med lösenord](media/msal-authentication-flows/confidential-client-password.png)

I föregående diagram visas följande:

1. Hämtar en token med hjälp av programhemlighets- eller lösenordsuppgifter.
2. Använder token för att göra begäranden av resursen.

### <a name="certificates"></a>Certifikat

![Diagram över konfidentiell klient med certifikat](media/msal-authentication-flows/confidential-client-certificate.png)

I föregående diagram visas följande:

1. Hämtar en token med hjälp av certifikatautentiseringsuppgifter.
2. Använder token för att göra begäranden av resursen.

Dessa klientuppgifter måste vara:
- Registrerad hos Azure AD.
- Skickas in vid byggandet av den konfidentiella klientansökan i din kod.

## <a name="device-code"></a>Enhetskod

MSAL stöder [OAuth 2-enhetskodflödet](v2-oauth2-device-code.md), som gör det möjligt för användare att logga in på indatabegränsade enheter, till exempel en smart-TV, IoT-enhet eller skrivare. Interaktiv autentisering med Azure AD kräver en webbläsare. Med enhetskodflödet kan användaren använda en annan enhet (till exempel en annan dator eller en mobiltelefon) för att logga in interaktivt, där enheten eller operativsystemet inte tillhandahåller en webbläsare.

Genom att använda enhetskodflödet hämtar programmet token genom en tvåstegsprocess som är särskilt utformad för dessa enheter eller operativsystem. Exempel på sådana program är de som körs på IoT-enheter eller kommandoradsverktyg (CLI). 

![Diagram över enhetskodflödet](media/msal-authentication-flows/device-code.png)

I diagrammet ovan:

1. När användarautentisering krävs tillhandahåller appen en kod och ber användaren att använda en annan enhet (till exempel en `https://microsoft.com/devicelogin`internetansluten smartphone) för att gå till en webbadress (till exempel). Användaren uppmanas sedan att ange koden och fortsätter genom en normal autentiseringsupplevelse, inklusive samtyckesuppmaningar och multifaktorautentisering om det behövs.

2. När autentiseringen har slutförts tar kommandoradsappen emot de token som krävs via en bakåtkanal och använder dem för att utföra de webb-API-anrop som behövs.

### <a name="constraints"></a>Villkor

- Enhetskodflödet är endast tillgängligt för offentliga klientprogram.
- Den myndighet som skickas in vid konstruktionen av den offentliga klientansökan måste vara något av följande:
  - Klientad (i `https://login.microsoftonline.com/{tenant}/` formuläret `{tenant}` där är antingen DET GUID som representerar klient-ID eller en domän som är associerad med klienten).
  - För alla arbets-`https://login.microsoftonline.com/organizations/`och skolkonton ( ).
- Microsofts personliga konton stöds ännu inte av Slutpunkten för Azure AD v2.0 (du kan inte använda `/common` eller `/consumers` klienter).

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

MSAL stöder integrerad Windows-autentisering (IWA) för stationära eller mobila program som körs på en domänanserad eller Azure AD-ansluten Windows-dator. Med IWA kan dessa program hämta en token tyst (utan gränssnittsinteraktion från användaren). 

![Diagram över integrerad Windows-autentisering](media/msal-authentication-flows/integrated-windows-authentication.png)

I föregående diagram visas följande:

1. Hämtar en token med integrerad Windows-autentisering.
2. Använder token för att göra begäranden av resursen.

### <a name="constraints"></a>Villkor

IWA stöder endast federerade användare, vilket innebär att användare som skapats i Active Directory och backas upp av Azure AD. Användare som skapats direkt i Azure AD, utan Active Directory-stöd (hanterade användare) kan inte använda det här autentiseringsflödet. Den här begränsningen påverkar inte [flödet användarnamn/lösenord](#usernamepassword).

IWA är för appar som är skrivna för plattformarna .NET Framework, .NET Core och Universal Windows Platform.

IWA kringgår inte multifaktorautentisering. Om multifaktorautentisering har konfigurerats kan IWA misslyckas om en multifaktorautentiseringsutmaning krävs. Multifaktorautentisering kräver användarinteraktion.

Du styr inte när identitetsprovidern begär tvåfaktorsautentisering som ska utföras. Klientadministratören gör det. Vanligtvis krävs tvåfaktorsautentisering när du loggar in från ett annat land, när du inte är ansluten via VPN till ett företagsnätverk och ibland även när du är ansluten via VPN. Azure AD använder AI för att kontinuerligt ta reda på om tvåfaktorsautentisering krävs. Om IWA misslyckas bör du återgå till en [interaktiv användarprompt] (#interactive).

Den myndighet som skickas in vid konstruktionen av den offentliga klientansökan måste vara något av följande:
- Klientad (i `https://login.microsoftonline.com/{tenant}/` formuläret `tenant` där är antingen guid som representerar klient-ID eller en domän som är associerad med klienten).
- För alla arbets-`https://login.microsoftonline.com/organizations/`och skolkonton ( ). Microsofts personliga konton stöds inte (du kan inte använda `/common` eller `/consumers` klienter).

Eftersom IWA är ett tyst flöde måste ett av följande vara sant:
- Användaren av ditt program måste tidigare ha samtyckt till att använda programmet. 
- Klientadministratören måste tidigare ha samtyckt till alla användare i klienten för att kunna använda programmet.

Detta innebär att ett av följande är sant:
- Du som utvecklare har själv valt **Grant** på Azure-portalen.
- En klientadministratör har valt **Bevilja/återkalla administratörsmedgivande för {klientdomän}** på fliken **API-behörigheter** för registreringen för programmet (se [Lägg till behörigheter för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Du har lämnat ett sätt för användare att samtycka till programmet (se [Begäran om individuellt användarmedgivande).](v2-permissions-and-consent.md#requesting-individual-user-consent)
- Du har lämnat ett sätt för klientadministratören att godkänna programmet (se [administratörssamtycke](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

IWA-flödet är aktiverat för .NET-skrivbords-, .NET Core- och Windows Universal Platform-appar. På .NET Core måste du ange användarnamnet till IWA, eftersom .NET Core inte kan hämta användarnamn från operativsystemet.
  
Mer information om samtycke finns i [v2.0-behörigheter och samtycke](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Användarnamn/lösenord

MSAL stöder beviljande av [OAuth 2-resursägarelösenord](v2-oauth-ropc.md), vilket gör att ett program kan logga in användaren genom att direkt hantera deras lösenord. I ditt skrivbordsprogram kan du använda flödet användarnamn/lösenord för att hämta en token tyst. Inget användargränssnitt krävs när du använder programmet.

![Diagram över användarnamnet/lösenordsflödet](media/msal-authentication-flows/username-password.png)

I föregående diagram visas följande:

1. Hämtar en token genom att skicka användarnamnet och lösenordet till identitetsleverantören.
2. Anropar ett webb-API med hjälp av token.

> [!WARNING]
> Det här flödet rekommenderas inte. Det kräver en hög grad av förtroende och användarexponering.  Du bör bara använda det här flödet när andra, säkrare, flöden inte kan användas. Mer information finns i [Vad är lösningen på det växande problemet med lösenord?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Det önskade flödet för att hämta en token tyst på Windows-domänanslutna datorer är [integrerad Windows-autentisering](#integrated-windows-authentication). Annars kan du också använda [Enhetskodflödet](#device-code).

Även om detta är användbart i vissa fall (DevOps-scenarier), försök att undvika det om du vill använda användarnamn/lösenord i interaktiva scenarier där du tillhandahåller ett eget användargränssnitt. Genom att använda användarnamn/lösenord:
- Användare som behöver multifaktorautentisering kan inte logga in (eftersom det inte finns någon interaktion).
- Användarna kan inte göra enkel inloggning.

### <a name="constraints"></a>Villkor

Förutom de [integrerade windows-autentiseringsbegränsningarna](#integrated-windows-authentication)gäller även följande begränsningar:

- Flödet för användarnamn/lösenord är inte kompatibelt med villkorlig åtkomst och multifaktorautentisering. Om din app körs i en Azure AD-klient där klientadministratören kräver multifaktorautentisering kan du därför inte använda det här flödet. Många organisationer gör det.
- Det fungerar bara för arbets- och skolkonton (inte Microsoft-konton).
- Flödet är tillgängligt på .NET-skrivbordet och .NET Core, men inte på Universell Windows-plattform.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C-specifika

Mer information om hur du använder MSAL.NET och Azure AD B2C finns i [Använda ROPC med Azure AD B2C (MSAL.NET).](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)
