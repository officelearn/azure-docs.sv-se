---
title: Klientprogram (Microsoft Authentication Library) | Azure
description: Läs mer om offentlig klient och konfidentiell klient program i Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 096aa5e5ce2f33467457cef22220f338ae49b708
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139103"
---
# <a name="authentication-flows"></a>Autentiseringsflöden

Den här artikeln beskrivs de olika autentiseringsflöden som tillhandahålls av Microsoft Authentication Library (MSAL).  Dessa flöden kan användas i en mängd olika Programscenarier.

| Flöde | Beskrivning | Används i|  
| ---- | ----------- | ------- | 
| [Implicit beviljande](#implicit-grant) | Tillåter appen att hämta token utan att utföra en autentiseringsutbyte för backend-server. På så sätt kan appen att logga in användaren, hålla sessionen aktiv och hämta token till andra webb-API: er inom klienten JavaScript-kod.| [Enkelsidigt program (SPA)](scenario-spa-overview.md) |
| [auktoriseringskod](#authorization-code) | Används i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. På så sätt kan du lägga till logga in och API-åtkomst till dina appar och program. | [Skrivbordsappar](scenario-desktop-overview.md), [mobilappar](scenario-mobile-overview.md), [Web Apps](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Ett program anropar en tjänst/webb-API, som i sin tur måste anropa en annan tjänst/webb-API. Tanken är att sprida delegerade användaren identitets- och behörigheter genom begärandekedjan. | [Webb-API:er](scenario-web-api-call-api-overview.md) |
| [Klientautentiseringsuppgifter](#client-credentials) | Ger dig tillgång till web-värdbaserade resurser med hjälp av identiteten för ett program. Ofta används för server-till-server-interaktioner som måste köras i bakgrunden utan direkt interaktion med en användare. | [Daemon-appar](scenario-daemon-overview.md) |
| [Kod för enhet](#device-code) | Tillåter användare att logga in på indata begränsad enheter, till exempel en smart-TV, IoT-enheter och skrivare. | [Desktop/Mobile apps](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Integrerad Windows-autentisering](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Gör att program på domänen eller Azure AD domänanslutna datorer för att hämta en token för tyst installation (utan en UI-interaktion från användaren).| [Desktop/Mobile apps](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Användarnamn/lösenord](scenario-desktop-acquire-token.md#username--password) | Gör att program att logga in användaren genom att direkt hantera sitt lösenord. Det här flödet rekommenderas inte. | [Desktop/mobilappar](scenario-desktop-acquire-token.md#username--password) | 


## <a name="implicit-grant"></a>Implicit beviljande

MSAL stöder den [flöde beviljat med OAuth 2 implicit](v2-oauth2-implicit-grant-flow.md), vilket tillåter appen att hämta token från Microsoft identity-plattformen utan att utföra en backend-servern autentiseringsutbyte. På så sätt kan appen att logga in användaren, hålla sessionen aktiv och hämta token till andra webb-API: er inom klienten JavaScript-kod.

![Implicit beviljande av flöde](media/msal-authentication-flows/implicit-grant.svg)

Många moderna webbprogram skapas med en sida för klientsidan program som skrivits med JavaScript- eller en SPA-ramverk som Angular och Vue.js React.js. Dessa program körs i en webbläsare och har olika egenskaper än traditionella serversidan webbprogram. Microsoft identity-plattformen gör det möjligt för en sida-program för att logga in användare och hämta token för åtkomst till backend-tjänster eller webb-API: er med implicit beviljande av flödet. Det implicita flödet kan programmet för att hämta ID-token som representerar den autentiserade användaren och även åtkomsttoken krävs för att anropa API: er för skyddade.

Det här autentiseringsflödet inkluderar inte Programscenarier med hjälp av JavaScript-ramverk för flera plattformar, till exempel Electron och React Native, eftersom de vill kräva ytterligare funktioner för interaktion med de inbyggda plattformarna.

## <a name="authorization-code"></a>Auktoriseringskod
MSAL stöder den [OAuth 2 beviljande via auktoriseringskod](v2-oauth2-auth-code-flow.md), som kan användas i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. På så sätt kan du lägga till logga in och API-åtkomst till dina appar och program. 

När användare loggar in till webbprogram (webbplatser), webbprogrammet tar emot en auktoriseringskod.  Auktoriseringskoden har löst in för att hämta en token för att anropa webb-API: er. I ASP.NET / ASP.NET core web apps och det enda syftet med `AcquireTokenByAuthorizationCode` är att lägga till en token tokens cacheminne, så att den kan sedan användas av programmet (vanligtvis i styrenheterna) som bara hämta en token för ett API med hjälp av `AcquireTokenSilent`.

![-Auktoriseringskodflöde](media/msal-authentication-flows/authorization-code.png)

### <a name="considerations"></a>Överväganden
- Auktoriseringskoden är användbar bara en gång att lösa in en token. Försök inte att hämta en token flera gånger med samma Auktoriseringskoden (det är uttryckligen förbjuden enligt standard protokollspecifikation). Om du har löst in koden flera gånger avsiktligt, eller eftersom du inte är medveten om att ett ramverk också gör det åt dig, får du ett fel: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Detta kan inträffa om du inte se ASP.NET/Core ramverk som du har redan utnyttjat Auktoriseringskoden om du skriver ett ASP.NET/ASP.NET Core-program. För detta, måste du anropa `context.HandleCodeRedemption()` -metoden för den `AuthorizationCodeReceived` händelsehanterare.

- Undvik att dela den åtkomst-token med ASP.NET, vilket kan medföra att inkrementella medgivande sker korrekt.  Mer information finns i [utfärda #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

MSAL stöder den [OAuth 2 on-behalf-of-autentiseringsflödet](v2-oauth2-on-behalf-of-flow.md).  Det här flödet används när ett program anropar en tjänst/webb-API, som i sin tur måste anropa en annan tjänst/webb-API. Tanken är att sprida delegerade användaren identitets- och behörigheter genom begärandekedjan. För mellannivå-tjänsten ska göra autentiserade begäranden till den underordnade tjänsten, måste den säkra en åtkomsttoken från Microsoft identity-plattformen för användarens räkning.

![On-Behalf-Of-flöde](media/msal-authentication-flows/on-behalf-of.png)

1. Hämtar en åtkomsttoken för webb-API
2. En klient (webb, desktop, mobil, Single-page application) anropar en skyddad webb-API, som att lägga till åtkomsttoken som en ägartoken i autentiseringshuvud för HTTP-begäran. Webb-API autentiserar användaren.
3. När klienten anropar webb-API, begär webb-API för en annan token on-behalf-of användaren.  
4. Skyddade webb-API använder denna token för att anropa en underordnad webb-API on-behalf-of användaren.  Webb-API kan också senare att begära token för andra underordnade API: er (men fortfarande för samma användare).

## <a name="client-credentials"></a>Klientautentiseringsuppgifter

MSAL stöder den [OAuth 2 flödet för klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md). Det här flödet kan du få åtkomst till webb-värdbaserade resurser med hjälp av identiteten för ett program. Den här typen av bevilja används ofta för server-till-server-interaktioner som måste köras i bakgrunden utan direkt interaktion med en användare. Dessa typer av program är ofta kallas Daemon eller tjänstkonton. 

Med klientens autentiseringsuppgifter ge flow tillåter en webbtjänst (konfidentiell klient) för att använda sina egna autentiseringsuppgifter, i stället för att personifiera en användare för att autentisera vid anrop av en annan webbtjänst. I det här scenariot är klienten vanligtvis en webbtjänst på mellannivå, en daemontjänst eller en webbplats. Microsoft identity-plattformen kan också anropa tjänsten att använda ett certifikat (i stället för en delad hemlighet) som en autentiseringsuppgift för en högre säkerhetsnivå.

> [!NOTE]
> Konfidentiell klientflödet är inte tillgänglig på de mobila plattformarna (UWP Xamarin.iOS och Xamarin.Android), eftersom dessa endast stöder offentliga klientprogram.  Offentliga klientprogram vet inte hur man verifiera programmets identitet till identitetsleverantören. En säker anslutning kan ske på webbapp eller webb-API-servrar genom att distribuera ett certifikat.

MSAL.NET stöder tre typer av klientens autentiseringsuppgifter:

- Programhemligheter <BR>![Konfidentiell klient med lösenord](media/msal-authentication-flows/confidential-client-password.png)
- Certifikat <BR>![Konfidentiell klient med certifikat](media/msal-authentication-flows/confidential-client-certificate.png)
- Optimerad klienten intyg<BR>![Konfidentiell klient med intyg](media/msal-authentication-flows/confidential-client-assertions.png)

Dessa klientautentiseringsuppgifter måste vara:

- Registrerad med Azure AD.
- Tidsstämpelsträng vid konstruktion av konfidentiell klient-programmet i din kod.


## <a name="device-code"></a>Kod för enhet
MSAL stöder den [kodflöde för OAuth 2 enheten](v2-oauth2-device-code.md), vilket gör att användare att logga in på indata begränsad enheter, till exempel en smart-TV, IoT-enheter och skrivare. Interaktiv autentisering med Azure AD kräver en webbläsare. Kodflöde för enheten kan användaren använda en annan enhet (till exempel en annan dator eller en mobiltelefon) för att logga in interaktivt där enheten eller operativsystemet inte tillhandahåller en webbläsare.

Programmet hämtar token via en tvåstegsprocess som särskilt utformats för dessa enheter/OS med hjälp av kodflöde för enheten. Exempel på sådana program är program som körs på iOT-enheter eller kommandoradsverktyg (CLI). 

![Enhetskodflöde](media/msal-authentication-flows/device-code.png)

1. När autentisering av användare krävs appen innehåller en kod och uppmanar användaren att använda en annan enhet (till exempel en internet-anslutna smartphone) för att navigera till en URL (till exempel http://microsoft.com/devicelogin), där användaren uppmanas att ange koden. Att klar sidan kommer att ge användaren genom en normal autentiseringsupplevelse, inklusive medgivande och multifaktorautentisering om det behövs.

2. Efter lyckad autentisering kommandoradsappen får de nödvändiga token via en tillbaka-kanal och använda den för att utföra webb-API-anrop som behövs.

### <a name="constraints"></a>Villkor

- Kodflöde för enheten är bara tillgängligt på offentliga klientprogram.
- Utfärdaren skickas när konstruera offentliga klientprogrammet måste vara:
  - Backups (i formatet `https://login.microsoftonline.com/{tenant}/` där `{tenant}` är antingen GUID som representerar klient-ID eller en domän som är kopplade till klienten.
  - eller eventuella arbets- och skolkonton konton (`https://login.microsoftonline.com/organizations/`).
- Personliga Microsoft-konton är ännu inte stöds av Azure AD v2.0-slutpunkten (du kan inte använda den `/common` eller `/consumers` klienter).

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering
MSAL stöder integrerad Windows autentisering (IWA) för desktop eller mobila program som körs på en domänansluten eller Azure AD-anslutna Windows-dator. Använda IWA kan kan dessa program hämta en token tyst installation (utan en UI-interaktion från användaren). 

![Integrerad Windows-autentisering](media/msal-authentication-flows/integrated-windows-authentication.png)

### <a name="constraints"></a>Villkor

Har stöd för IWA **federerade** endast användare.  Användare skapas i en Active Directory och backas upp av Azure Active Directory. Användare som skapas direkt i Azure AD utan AD säkerhetskopiering (**hanteras** användare) kan inte använda det här autentiseringsflödet. Den här begränsningen inte påverkar den [användarnamn/lösenord flow](#usernamepassword).

IWA är för appar som är skrivna för .NET Framework och .NET Core Universal Windows Platform-plattformar.

IWA kringgås inte MFA (multifaktorautentisering). Om MFA har konfigurerats, kan IWA misslyckas om en MFA-kontrollen är nödvändigt eftersom MFA kräver interaktion från användaren. Det är svårt att den här. IWA är icke-interaktivt, men tvåfaktors-auktorisering (2FA) kräver användarinteraktion. Du kan inte styra när identitetsprovidern begär 2FA som ska utföras, administratör har. Från våra observationer krävs 2FA när du loggar in från ett annat land när inte ansluten via VPN till ett företagsnätverk, och ibland även när ansluten via VPN. Förväntar sig inte en deterministisk uppsättning regler, Azure Active Directory använder AI att kontinuerligt lära dig om 2FA krävs. Bör du återgå till en användaruppmaning (https://aka.ms/msal-net-interactive) om IWA misslyckas.

Utfärdaren skickas när konstruera offentliga klientprogrammet måste vara:
- Backups (i formatet `https://login.microsoftonline.com/{tenant}/` där `tenant` är antingen guid som representerar klient-ID eller en domän som är kopplade till klienten.
- för alla arbets- och skolkonton (`https://login.microsoftonline.com/organizations/`). Personliga Microsoft-konton stöds inte (du kan inte använda `/common` eller `/consumers` klienter).

Eftersom IWA är ett tyst flöde:
- användare av ditt program måste ha tidigare godkänt att använda programmet. 
- eller administratör måste tidigare har samtyckt till alla användare i klienten att använda programmet.
- Detta innebär att:
    - antingen du som utvecklare har tryckt på **bevilja** knappen på Azure portal för dig själv 
    - eller en klientadministratör har tryckts ned den **Grant/revoke administratörens godkännande för {}** knappen i den **API-behörigheter** fliken registreringen för programmet (se [Lägg till behörigheter till åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))
    - eller, du har angett ett sätt för användarna att godkänna att programmet (se [begär enskilda användargodkännande](v2-permissions-and-consent.md#requesting-individual-user-consent))
    - eller, du har angett ett sätt för administratör att ge samtycke för programmet (se [administratörsmedgivande](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))

IWA flödet har aktiverats för .NET skrivbords-, .NET Core- och Windows Universal Platform-appar. Endast överlagringen tar användarnamnet är tillgängligt på .NET Core som .NET Core-plattformen inte kan ställa användarnamnet till Operativsystemet.
  
Mer information om medgivande finns [v2.0 behörigheter och samtycke](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Användarnamn/lösenord 
MSAL stöder den [OAuth 2 resource resursägarens lösenordsautentiseringsuppgifter](v2-oauth-ropc.md), vilket gör att ett program för att logga in användaren genom att direkt hantera sitt lösenord. Du kan använda användarnamn/lösenord flödet för att hämta en token tyst i din skrivbordsprogram. Något användargränssnitt krävs vid användning av programmet.

![Användarnamn/lösenord flöde](media/msal-authentication-flows/username-password.png)

> [!WARNING]
> Det här flödet är **rekommenderas inte** eftersom den kräver en hög grad av förtroende och användaren exponering.  Du bör bara använda det här flödet när andra och säkrare, flöden inte kan användas. Mer information om det här problemet finns i [i den här artikeln](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Prioriterade flödet för att skaffa en token tyst på Windows-domänanslutna datorer är [integrerad Windows-autentisering](#integrated-windows-authentication). Annars kan du också använda [kodflöde för enhet](#device-code)

Även om detta är användbart i vissa fall (DevOps-scenario), om du vill använda användarnamn/lösenord i interaktiva scenarier där du anger ditt eget användargränssnitt, bör du tycker om hur du flyttar från den. Genom att använda användarnamn/lösenord kan du ge upp ett antal saker:
- viktiga innehavare av moderna identitet: lösenord hämtar fiskas återupprepas. Eftersom vi har detta begrepp av en resurs-hemlighet som kan fångas.
Det här är inte kompatibel med lösenordslös.
- användare som behöver du MFA kommer inte kunna logga in (eftersom det finns inga åtgärder från)
- Användare kommer inte att kunna enkel inloggning

### <a name="constraints"></a>Villkor

Förutom den [integrerad Windows-autentisering begränsningar](#integrated-windows-authentication), gäller även följande begränsningar:

- Flödet användarnamn/lösenord är inte kompatibel med villkorlig åtkomst och autentisering med flera faktorer: Om din app körs i en Azure AD-klient där administratör kräver Multi-Factor authentication kan använda du följaktligen kommer inte det här flödet. Många företag gör det.
- Det fungerar endast för arbete och skola konton (inte MSA)
- Flödet är tillgänglig på .NET desktop- och .NET core, men inte på Universal Windows Platform.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C-specifika

Mer information om hur du använder MSAL.NET och Azure AD B2C [med ROPC med Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).