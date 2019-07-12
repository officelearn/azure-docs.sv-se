---
title: Autentiseringsflöden (Microsoft Authentication Library) | Azure
description: Läs mer om autentiseringsflöden och beviljar som används av Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ba6ae188c098e85573503a1518ba65480d713a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807213"
---
# <a name="authentication-flows"></a>Autentiseringsflöden

Den här artikeln beskrivs de olika autentiseringsflöden som tillhandahålls av Microsoft Authentication Library (MSAL).  Dessa flöden kan användas i en mängd olika Programscenarier.

| Flöde | Beskrivning | Används i|  
| ---- | ----------- | ------- | 
| [Interaktiv](#interactive) | Hämtar en token via en interaktiv process som frågar användaren om autentiseringsuppgifter via en webbläsare eller popup-fönstret. | [Skrivbordsappar](scenario-desktop-overview.md), [mobilappar](scenario-mobile-overview.md) |
| [Implicit beviljande](#implicit-grant) | Tillåter appen att hämta token utan att utföra en autentiseringsutbyte för backend-server. På så sätt kan appen att logga in användaren, hålla sessionen aktiv och hämta token till andra webb-API: er, allt inom klienten JavaScript-kod.| [Enkelsidigt program (SPA)](scenario-spa-overview.md) |
| [auktoriseringskod](#authorization-code) | Används i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. På så sätt kan du lägga till in- och API-åtkomst till dina appar och program. | [Skrivbordsappar](scenario-desktop-overview.md), [mobilappar](scenario-mobile-overview.md), [webbappar](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Ett program anropar en tjänst eller ett webb-API, som i sin tur måste anropa en annan tjänst eller webb-API. Tanken är att sprida delegerade användaren identitets- och behörigheter genom begärandekedjan. | [Webb-API:er](scenario-web-api-call-api-overview.md) |
| [Klientautentiseringsuppgifter](#client-credentials) | Ger dig tillgång till web-värdbaserade resurser med hjälp av identiteten för ett program. Ofta används för server-till-server-interaktioner som måste köras i bakgrunden utan direkt interaktion med en användare. | [Daemon-appar](scenario-daemon-overview.md) |
| [Kod för enhet](#device-code) | Tillåter användare att logga in på indata begränsad enheter, till exempel en smart-TV, IoT-enheter och skrivare. | [Desktop/mobilappar](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Integrerad Windows-autentisering](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Gör att program på domänen eller Azure Active Directory (Azure AD) anslutna datorer att hämta en token för tyst installation (utan en UI-interaktion från användaren).| [Desktop/mobilappar](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Användarnamn/lösenord](scenario-desktop-acquire-token.md#username--password) | Gör att program att logga in användaren genom att direkt hantera sitt lösenord. Det här flödet rekommenderas inte. | [Desktop/mobilappar](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interaktiv
MSAL stöder möjligheten att interaktivt uppmana användaren att ange sina autentiseringsuppgifter för att logga in och få en token med hjälp av autentiseringsuppgifterna.

![Diagram över interaktiva flöde](media/msal-authentication-flows/interactive.png)

Mer information om med hjälp av MSAL.NET interaktivt hämta token på vissa plattformar finns:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Universal Windows Platform](msal-net-uwp-considerations.md)

Läs mer på interaktiva anrop i MSAL.js [fråga beteende i MSAL.js interaktiva begäranden](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Implicit beviljande

MSAL stöder den [flöde beviljat med OAuth 2 implicit](v2-oauth2-implicit-grant-flow.md), vilket tillåter appen att hämta token från Microsoft identity-plattformen utan att utföra en backend-server autentiseringsutbyte. På så sätt kan appen att logga in användaren, hålla sessionen aktiv och hämta token till andra webb-API: er, allt inom klienten JavaScript-kod.

![Diagram över implicit beviljande av flöde](media/msal-authentication-flows/implicit-grant.svg)

Många moderna webbprogram skapas med på klientsidan, en enda sida program, som är skrivna med hjälp av JavaScript- eller en SPA-ramverk som Angular och Vue.js React.js. Dessa program körs i en webbläsare och har olika egenskaper än traditionella serversidan webbprogram. Microsoft identity-plattformen gör det möjligt för en sida-program för att logga in användare och hämta token för åtkomst till backend-tjänster eller webb-API: er, med hjälp av implicit beviljande av flödet. Det implicita flödet kan programmet för att hämta ID-token som representerar den autentiserade användaren och även åtkomsttoken krävs för att anropa API: er för skyddade.

Det här autentiseringsflödet omfattar inte Programscenarier som använder JavaScript-ramverk för flera plattformar, till exempel Electron och React Native, eftersom de kräver att ytterligare funktioner för interaktion med de inbyggda plattformarna.

## <a name="authorization-code"></a>auktoriseringskod
MSAL stöder den [OAuth 2 beviljande via auktoriseringskod](v2-oauth2-auth-code-flow.md). Det här beviljandet kan användas i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. På så sätt kan du lägga till in- och API-åtkomst till dina appar och program. 

När användare loggar in till webbprogram (webbplatser), webbprogrammet tar emot en auktoriseringskod.  Auktoriseringskoden har löst in för att hämta en token för att anropa webb-API: er. I ASP.NET och ASP.NET Core web apps, endast målet med `AcquireTokenByAuthorizationCode` är att lägga till en token token-cache. Token kan sedan användas av programmet (vanligtvis i styrenheterna, som bara hämta en token för ett API med hjälp av `AcquireTokenSilent`).

![Diagram över auktoriseringskodflöde](media/msal-authentication-flows/authorization-code.png)

I det föregående diagrammet programmet:

1. Begär en auktoriseringskod som har löst in för en åtkomsttoken.
2. Använder åtkomsttoken för att anropa ett webb-API.

### <a name="considerations"></a>Överväganden
- Du kan använda Auktoriseringskoden bara en gång för att lösa in en token. Försök inte att hämta en token flera gånger med samma Auktoriseringskoden (det är uttryckligen förbjuden enligt standard protokollspecifikation). Om du har löst in koden flera gånger avsiktligt, eller eftersom du inte är medveten om att ett ramverk också gör det åt dig, får du följande fel: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Detta kan inträffa om du inte se ramverket som du redan har utnyttjat Auktoriseringskoden om du skriver ett ASP.NET eller ASP.NET Core-program. För detta, måste du anropa den `context.HandleCodeRedemption()` -metoden för den `AuthorizationCodeReceived` händelsehanterare.

- Undvik att dela den åtkomst-token med ASP.NET, vilket kan medföra att inkrementella medgivande sker korrekt. Mer information finns i [utfärda #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

MSAL stöder den [OAuth 2 on-behalf-of-autentiseringsflödet](v2-oauth2-on-behalf-of-flow.md).  Det här flödet används när ett program anropar en tjänst eller ett webb-API, som i sin tur måste anropa en annan tjänst eller webb-API. Tanken är att sprida delegerade användaren identitets- och behörigheter genom begärandekedjan. För mellannivå-tjänsten ska göra autentiserade begäranden till den underordnade tjänsten, måste den säkra en åtkomsttoken från Microsoft identity-plattformen för användarens räkning.

![Diagram över on-behalf-of-flöde](media/msal-authentication-flows/on-behalf-of.png)

I föregående diagram:

1. Programmet hämtar en åtkomsttoken för webb-API.
2. En klient (webb, skrivbord, mobilt eller enkelsidigt program) anropar ett skyddat webb-API, att lägga till åtkomsttoken som en ägartoken i autentiseringshuvud för HTTP-begäran. Webb-API autentiserar användaren.
3. När klienten anropar webb-API, begär webb-API: en annan token on-behalf-of användaren.  
4. Skyddade webb-API använder denna token för att anropa ett webb-API som är underordnade on-behalf-of användaren.  Webb-API kan också senare begära token för andra underordnade API: er (men fortfarande för samma användare).

## <a name="client-credentials"></a>Klientautentiseringsuppgifter

MSAL stöder den [OAuth 2 flödet för klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md). Det här flödet kan du få åtkomst till webb-värdbaserade resurser med hjälp av identiteten för ett program. Den här typen av bevilja används ofta för server-till-server-interaktioner som måste köras i bakgrunden utan direkt interaktion med en användare. Dessa typer av program är ofta kallas Daemon eller tjänstkonton. 

Med klientens autentiseringsuppgifter ge flow tillåter en webbtjänst (en konfidentiell klient) för att använda sina egna autentiseringsuppgifter, i stället för att personifiera en användare för att autentisera vid anrop av en annan webbtjänst. I det här scenariot är klienten vanligtvis en webbtjänst på mellannivå, en daemontjänst eller en webbplats. Microsoft identity-plattformen kan också anropa tjänsten att använda ett certifikat (i stället för en delad hemlighet) som en autentiseringsuppgift för en högre säkerhetsnivå.

> [!NOTE]
> Konfidentiell klientflödet är inte tillgängligt på de mobila plattformarna (UWP Xamarin.iOS och Xamarin.Android), eftersom dessa endast stöd för offentliga klientprogram. Offentliga klientprogram vet inte hur man verifiera programmets identitet till identitetsleverantören. En säker anslutning kan ske på webbapp eller webb-API tillbaka slutar genom att distribuera ett certifikat.

MSAL.NET stöder två typer av klientens autentiseringsuppgifter. Dessa klientautentiseringsuppgifter måste vara registrerad med Azure AD. Autentiseringsuppgifterna skickas i till konstruktorerna i konfidentiell klientprogrammet i din kod.

### <a name="application-secrets"></a>Programhemligheter 

![Diagram över konfidentiell klient med lösenord](media/msal-authentication-flows/confidential-client-password.png)

I det föregående diagrammet programmet:

1. Hämtar en token med hjälp av autentiseringsuppgifter för hemlighet eller ett lösenord.
2. Använder token för att göra förfrågningar från resursen.

### <a name="certificates"></a>Certifikat 

![Diagram över konfidentiell klient med certifikat](media/msal-authentication-flows/confidential-client-certificate.png)

I det föregående diagrammet programmet:

1. Hämtar en token med hjälp av autentiseringsuppgifter för certifikat.
2. Använder token för att göra förfrågningar från resursen.

Dessa klientautentiseringsuppgifter måste vara:
- Registrerad med Azure AD.
- Tidsstämpelsträng vid konstruktion av konfidentiell klient-programmet i din kod.


## <a name="device-code"></a>Kod för enhet
MSAL stöder den [kodflöde för OAuth 2 enheten](v2-oauth2-device-code.md), vilket gör att användare att logga in på indata begränsad enheter, till exempel en smart-TV, IoT-enheter och skrivare. Interaktiv autentisering med Azure AD kräver en webbläsare. Kodflöde för enheten kan användaren använda en annan enhet (till exempel en annan dator eller en mobiltelefon) för att logga in interaktivt, där enheter eller operativsystem inte tillhandahåller en webbläsare.

Med hjälp av kodflöde för enheten, hämtar programmet token via en tvåstegsprocess som särskilt utformats för dessa enheter eller operativsystem. Exempel på sådana program är de som körs på IoT-enheter eller kommandoradsverktyg (CLI). 

![Diagram över kodflöde för enhet](media/msal-authentication-flows/device-code.png)

I föregående diagram:

1. När autentisering av användare krävs appen innehåller en kod och uppmanar användaren att använda en annan enhet (till exempel en internet-anslutna smartphone) för att gå till en URL (till exempel https://microsoft.com/devicelogin). Användaren uppmanas sedan att ange koden och fortsätter genom en normal autentiseringsupplevelse, inklusive medgivande och Multi-Factor authentication vid behov.

2. Efter lyckad autentisering kommandoradsappen tar emot de nödvändiga token via en tillbaka-kanal och använder dem för att utföra webb-API-anrop som behövs.

### <a name="constraints"></a>Villkor

- Kodflöde för enheten är bara tillgängligt på offentliga klientprogram.
- Behörighet när konstruera offentliga klientprogrammet måste vara något av följande:
  - Backups (i formatet `https://login.microsoftonline.com/{tenant}/` där `{tenant}` är antingen GUID som representerar klient-ID eller en domän som är associerade med klient).
  - för alla arbets- och skolkonton (`https://login.microsoftonline.com/organizations/`).
- Personliga Microsoft-konton stöds inte ännu av Azure AD v2.0-slutpunkten (du kan inte använda den `/common` eller `/consumers` klienter).

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering
MSAL stöder integrerad Windows autentisering (IWA) för desktop eller mobila program som körs på en domänansluten eller Azure AD-anslutna Windows-dator. Använda IWA kan kan dessa program hämta en token tyst installation (utan en UI-interaktion från användaren). 

![Diagram över integrerad Windows-autentisering](media/msal-authentication-flows/integrated-windows-authentication.png)

I det föregående diagrammet programmet:

1. Hämtar en token med hjälp av integrerad Windows-autentisering.
2. Använder token för att göra förfrågningar från resursen.

### <a name="constraints"></a>Villkor

IWA har stöd för federerade användare, vilket innebär att användare skapas i Active Directory och backas upp av Azure AD. Användare som skapas direkt i Azure AD utan Active Directory säkerhetskopiering (hanterade användare) kan inte använda det här autentiseringsflödet. Den här begränsningen inte påverkar den [användarnamn/lösenord flow](#usernamepassword).

IWA är för appar som är skrivna för .NET Framework och .NET Core Universal Windows Platform-plattformar.

IWA inte koppla förbi Multi-Factor authentication. Om multifaktorautentisering har konfigurerats, kan IWA misslyckas om en multifaktorautentisering utmaning krävs. Multifaktorautentisering kräver interaktion från användaren.

Du inte har kontroll över när identitetsprovidern begär tvåfaktorsautentisering som ska utföras. Administratör tillåter. Vanligtvis krävs tvåfaktorsautentisering när du loggar in från ett annat land, när du inte är ansluten via VPN till ett företagsnätverk och ibland även när du är ansluten via VPN. Azure AD använder AI att kontinuerligt lära dig om tvåfaktorsautentisering krävs. Om det inte går att IWA, du bör växla över till en [interaktiv användare fråga] (#interactive).

Behörighet när konstruera offentliga klientprogrammet måste vara något av följande:
- Backups (i formatet `https://login.microsoftonline.com/{tenant}/` där `tenant` är antingen guid som representerar klient-ID eller en domän som är associerade med klient).
- för alla arbets- och skolkonton (`https://login.microsoftonline.com/organizations/`). Personliga Microsoft-konton stöds inte (du kan inte använda `/common` eller `/consumers` klienter).

Eftersom IWA är ett tyst flöde kan måste något av följande vara uppfyllda:
- användare av ditt program måste ha tidigare godkänt att använda programmet. 
- Administratör måste tidigare har samtyckt till alla användare i klienten att använda programmet.

Det innebär att något av följande är sant:
- Du som utvecklare har valt **bevilja** på Azure-portalen för dig själv.
- Innehavaradministratör har valts **Grant/revoke administratörens godkännande för {}** i den **API-behörigheter** fliken registreringen för programmet (se [Lägg till behörigheter för att få åtkomst till webb-API: er ](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Du har angett ett sätt för användarna att godkänna att programmet (se [begär enskilda användargodkännande](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Du har angett ett sätt för administratör att ge samtycke för programmet (se [administratörsmedgivande](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

IWA flödet har aktiverats för .NET skrivbords-, .NET Core- och Windows Universal Platform-appar. Endast överlagringen tar användarnamnet är tillgängligt på .NET Core. .NET Core-plattformen kan be användarnamnet för operativsystemet.
  
Mer information om medgivande finns [v2.0 behörigheter och samtycke](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Användarnamn/lösenord 
MSAL stöder den [OAuth 2 resource resursägarens lösenordsautentiseringsuppgifter](v2-oauth-ropc.md), vilket gör att ett program för att logga in användaren genom att direkt hantera sitt lösenord. Du kan använda användarnamn/lösenord flödet för att hämta en token tyst i din skrivbordsprogram. Något användargränssnitt krävs vid användning av programmet.

![Diagram över flödet användarnamn/lösenord](media/msal-authentication-flows/username-password.png)

I det föregående diagrammet programmet:

1. Hämtar en token genom att skicka det användarnamn och lösenord till identitetsleverantören.
2. Anropar ett webb-API genom att använda token.

> [!WARNING]
> Det här flödet rekommenderas inte. Det kräver en hög grad av förtroende och användaren exponering.  Du bör bara använda det här flödet när andra och säkrare, flöden inte kan användas. Mer information finns i [vad är lösningen på växande problemet av lösenord?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Prioriterade flödet för att skaffa en token tyst på Windows-domänanslutna datorer är [integrerad Windows-autentisering](#integrated-windows-authentication). Annars kan du också använda [kodflöde för enheten](#device-code).

Även om detta är användbart i vissa fall (DevOps-scenario), om du vill använda användarnamn/lösenord i interaktiva scenarier där du anger ditt eget användargränssnitt, kan du försöka undvika den. Med hjälp av användarnamn/lösenord:
- Användare som behöver genomföra Multi-Factor authentication kan inte logga in (eftersom det finns inga åtgärder från).
- Användare kommer inte att kunna enkel inloggning.

### <a name="constraints"></a>Villkor

Förutom den [integrerad Windows-autentisering begränsningar](#integrated-windows-authentication), gäller även följande begränsningar:

- Flödet användarnamn/lösenord är inte kompatibelt med villkorlig åtkomst och multifaktorautentisering. Om din app körs i en Azure AD-klient där administratör kräver Multi-Factor authentication kan använda du följaktligen kommer inte det här flödet. Många företag gör det.
- Det fungerar endast för arbets- och skolkonton konton (inte Microsoft-konton).
- Flödet är tillgänglig på .NET desktop- och .NET Core, men inte på Universal Windows Platform.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C-specifika

Mer information om hur du använder MSAL.NET och Azure AD B2C finns i [med ROPC med Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
