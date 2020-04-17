---
title: Autentiseringsflöden för Microsoft-identitetsplattformar & appscenarier | Azure
description: Lär dig mer om programscenarier för Microsofts identitetsplattform, inklusive autentisering av identiteter, indataidentier och anropa skyddade API:er.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: bf47a352542be0ad4467ed420cc38f62da0d47dc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534915"
---
# <a name="authentication-flows-and-application-scenarios"></a>Autentiseringsflöden och programscenarier

Slutpunkten microsoft identity platform (v2.0) stöder autentisering för olika typer av moderna programarkitekturer. Alla arkitekturer är baserade på branschstandardprotokollen [OAuth 2.0 och OpenID Connect](active-directory-v2-protocols.md).  Med hjälp av [Microsofts identitetsplattformsautentiseringsbibliotek](reference-v2-libraries.md)autentiserar program identiteter och hämtar token för att komma åt skyddade API:er.

I den här artikeln beskrivs autentiseringsflöden och de programscenarier som de används i:

- [Programscenarier och autentiseringsflöden som stöds](#scenarios-and-supported-authentication-flows).
- [Programscenarier och plattformar och språk som stöds](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Programkategorier

Tokens kan hämtas från flera typer av program, inklusive:

- Webbappar
- Mobilappar
- Skrivbordsappar
- Webb-API:er

Token kan också hämtas från appar som körs på enheter som inte har en webbläsare eller körs på IoT.

Program kan kategoriseras som i följande lista:

- [Skyddade resurser jämfört med klientprogram:](#protected-resources-vs-client-applications)Vissa scenarier handlar om att skydda resurser som webbappar eller webb-API:er. Andra scenarier handlar om att skaffa en säkerhetstoken för att anropa ett skyddat webb-API.
- [Med användare eller utan användare:](#with-users-or-without-users)Vissa scenarier innebär en inloggad användare, men andra, som demonscenarier, involverar inte en användare.
- [Ensidiga, offentliga klienter och konfidentiella klientprogram](#single-page-public-client-and-confidential-client-applications): Dessa typer är tre stora kategorier av program. Var och en används med olika bibliotek och objekt.
- [Inloggningsmålgrupp](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): De tillgängliga autentiseringsflödena varierar beroende på inloggningspubliken. Vissa flöden är endast tillgängliga för arbets- eller skolkonton. Och vissa är tillgängliga både för arbets- eller skolkonton och för personliga Microsoft-konton. Den tillåtna målgruppen beror på autentiseringsflödena.
- [OAuth 2.0-flöden som stöds:](#scenarios-and-supported-authentication-flows)Autentiseringsflöden används för att implementera programscenarier som begär token. Det finns inte en 1:1-mappning mellan programscenarier och autentiseringsflöden.
- [Plattformar som stöds](#scenarios-and-supported-platforms-and-languages): Alla programscenarier är inte tillgängliga för alla plattformar.

### <a name="protected-resources-vs-client-applications"></a>Skyddade resurser jämfört med klientprogram

Autentiseringsscenarier omfattar två aktiviteter:

- **Hämta säkerhetstoken för ett skyddat webb-API**: Vi rekommenderar att du använder [Microsoft-stödda klientbibliotek](reference-v2-libraries.md#microsoft-supported-client-libraries) för att hämta token, särskilt MSAL-familjen (Microsoft Authentication Library).
- **Skydda ett webb-API eller en webbapp**: En utmaning att skydda ett webb-API eller en webbappresurs är att validera säkerhetstoken. På vissa plattformar erbjuder Microsoft [middleware-bibliotek](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Med användare eller utan användare

De flesta autentiseringsscenarier hämtar token för inloggade användare.

![Scenarier med användare](media/scenarios/scenarios-with-users.svg)

Det finns dock också daemon-app scenarier, där program förvärva token på uppdrag av sig själva utan användare.

![Scenarier med daemonappar](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Ensidiga, offentliga klienter och konfidentiella klientprogram

Säkerhetstoken kan hämtas från flera typer av program. Dessa tillämpningar tenderar att delas in i tre kategorier:

- **Ensidiga program:** Även känd som SPA, dessa program är webbappar där token förvärvas från en JavaScript eller TypeScript-app som körs i webbläsaren. Många moderna appar har en enda sida ansökan frontend som främst är skriven i JavaScript. Programmet använder ofta ett ramverk som Angular, React eller Vue. MSAL.js är det enda Microsoft-autentiseringsbibliotek som stöder ensidiga program.

- **Offentliga klientprogram**: Dessa program loggar alltid in användare:
  - Skrivbordsappar som anropar webb-API:er för den inloggade användaren
  - Mobilappar
  - Appar som körs på enheter som inte har en webbläsare, som de som körs på iOT

  Dessa appar representeras av klassen MSAL [PublicClientApplication.](/dotnet/api/microsoft.identity.client.publicclientapplication) Mer information finns i [Offentliga klient- och konfidentiella klientprogram](msal-client-applications.md).

- **Konfidentiella klientprogram:**
  - Webbappar som anropar ett webb-API
  - Webb-API:er som anropar ett webb-API
  - Daemon apps, även när de genomförs som en konsol tjänst som en Linux demon eller en Windows-tjänst

  Dessa typer av appar använder klassen [ConfidentialClientApplication.](/dotnet/api/microsoft.identity.client.confidentialclientapplication) Mer information finns i [Offentliga klient- och konfidentiella klientprogram](msal-client-applications.md).

## <a name="application-scenarios"></a>Programscenarier

Slutpunkten för Microsoft identity platform stöder autentisering för olika apparkitekturer:

- Enkelsidiga appar
- Webbappar
- Webb-API:er
- Mobilappar
- Inbyggda appar
- Daemon-appar
- Appar på serversidan

Program använder de olika autentiseringsflödena för att logga in användare och hämta token för att anropa skyddade API:er.

### <a name="a-single-page-application"></a>Ett ensidigt program

Många moderna webbappar är byggda som ensidiga program på klientsidan. Dessa program som använder JavaScript eller ett ensidigt programramverk som Angular, Vue.js och React.js. Dessa program körs i en webbläsare.

Deras autentiseringsegenskaper skiljer sig från traditionella webbappar på serversidan. Genom att använda Microsofts identitetsplattform kan ensidiga program logga in användare och hämta token för att komma åt backend-tjänster eller webb-API:er.

![Ett ensidigt program](media/scenarios/spa-app.svg)

Mer information finns i [Program med en sida](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>En webbapp som loggar in en användare

![En webbapp som loggar in en användare](media/scenarios/scenario-webapp-signs-in-users.svg)

Så här skyddar du en webbapp som loggar in en användare:

- Om du utvecklar i .NET använder du ASP.NET eller ASP.NET Core med ASP.NET Open ID Connect mellanprogram. Att skydda en resurs innebär att säkerhetstoken valideras, vilket görs av [IdentityModel-tilläggen för .NET-bibliotek](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) och inte MSAL-bibliotek.

- Om du utvecklas i Node.js använder du Passport.js.

Mer information finns [i Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>En webbapp som loggar in en användare och anropar ett webb-API för användarens räkning

![En webbapp som anropar webb-API:er](media/scenarios/web-app.svg)

Om du vill anropa ett webb-API från en webbapp för en användares räkning använder du klassen MSAL **ConfidentialClientApplication.** Du använder auktoriseringskodflödet och lagrar de förvärvade token i tokencachen. Vid behov uppdaterar MSAL token och styrenheten hämtar tyst token från cacheminnet.

Mer information finns i [En webbapp som anropar webb-API:er](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>En skrivbordsapp som anropar ett webb-API för en inloggad användare

Om en skrivbordsapp ska anropa ett webb-API som loggar in användare använder du de interaktiva tokeninhämtningsmetoderna i klassen MSAL **PublicClientApplication.** Med dessa interaktiva metoder kan du styra inloggningsgränssnittsupplevelsen. MSAL använder en webbläsare för den här interaktionen.

![En skrivbordsapp som anropar ett webb-API](media/scenarios/desktop-app.svg)

Det finns en annan möjlighet för Windows-värdprogram på datorer som är anslutna antingen till en Windows-domän eller av Azure Active Directory (Azure AD). Dessa program kan tyst hämta en token med hjälp av [integrerad Windows-autentisering](https://aka.ms/msal-net-iwa).

Program som körs på en enhet utan webbläsare kan fortfarande anropa ett API för en användares räkning. För att autentisera måste användaren logga in på en annan enhet som har en webbläsare. Det här scenariot kräver att du använder [enhetskodflödet](https://aka.ms/msal-net-device-code-flow).

![Flöde av enhetskod](media/scenarios/device-code-flow-app.svg)

Även om vi inte rekommenderar att du använder det är [flödet Användarnamn/lösenord](https://aka.ms/msal-net-up) tillgängligt i offentliga klientprogram. Det här flödet behövs fortfarande i vissa scenarier som DevOps.

Men att använda det här flödet begränsar dina program. Program kan till exempel inte logga in en användare som behöver använda multifaktorautentisering eller villkorlig åtkomst. Dina program drar inte heller nytta av enkel inloggning.

Autentisering med flödet Användarnamn/Lösenord strider mot principerna för modern autentisering och tillhandahålls endast av äldre skäl.

Om du vill att tokencachen ska finnas kvar i skrivbordsappar anpassar du [serialiseringen tokencache](https://aka.ms/msal-net-token-cache-serialization). Genom att implementera [serialisering av dual token cache](https://aka.ms/msal-net-dual-cache-serialization)kan du använda bakåtkompatibla och framåtkompatibla tokencacheminnen. Dessa token stöder tidigare generationer av autentiseringsbibliotek. Specifika bibliotek inkluderar Azure AD Authentication Library för .NET (ADAL.NET) version 3 och version 4.

Mer information finns i [Skrivbordsapp som anropar webb-API:er](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>En mobilapp som anropar ett webb-API för en interaktiv användares räkning

I likhet med en skrivbordsapp anropar en mobilapp de interaktiva tokeninhämtningsmetoderna i klassen MSAL **PublicClientApplication** för att hämta en token för att anropa ett webb-API.

![En mobilapp som anropar ett webb-API](media/scenarios/mobile-app.svg)

MSAL iOS och MSAL Android använder systemets webbläsare som standard. Du kan dock styra dem att använda den inbäddade webbvyn i stället. Det finns specifika effekter som beror på den mobila plattformen: Universal Windows Platform (UWP), iOS eller Android.

Vissa scenarier, till exempel de som innebär villkorlig åtkomst som är relaterade till ett enhets-ID eller en enhetsregistrering, kräver att en mäklare installeras på enheten. Exempel på mäklare är Microsoft Company Portal på Android och Microsoft Authenticator på Android och iOS. MSAL kan nu interagera med mäklare. Mer information finns i [Utnyttja mäklare på Android och iOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Mer information finns i [Mobilapp som anropar webb-API:er](scenario-mobile-overview.md).

> [!NOTE]
> Din mobilapp som använder MSAL.iOS, MSAL. Android eller MSAL.NET på Xamarin kan ha appskyddsprinciper tillämpade på den. Principerna kan till exempel hindra en användare från att kopiera skyddad text. Mobilappen hanteras av Intune och känns igen av Intune som en hanterad app. Mer information finns i [Översikt över Microsoft Intune App SDK](https://docs.microsoft.com/intune/app-sdk).
>
> [Intune App SDK](https://docs.microsoft.com/intune/app-sdk-get-started) är separat från MSAL-bibliotek och interagerar med Azure AD på egen hand.

### <a name="a-protected-web-api"></a>Ett skyddat webb-API

Du kan använda slutpunkten för Microsofts identitetsplattform för att skydda webbtjänster som appens RESTful-webb-API. Ett skyddat webb-API anropas med hjälp av en åtkomsttoken. Token skyddar API:ets data och autentiserar inkommande begäranden. Anroparen av ett webb-API lägger till en åtkomsttoken i auktoriseringshuvudet för en HTTP-begäran.

Om du vill skydda ditt ASP.NET eller ASP.NET Core-webb-API måste du validera åtkomsttoken. För den här valideringen använder du ASP.NET JWT middleware. Valideringen görs av [IdentityModel-tilläggen för .NET-biblioteket](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) och inte av MSAL.NET.

Mer information finns i [Skyddat webb-API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Ett webb-API som anropar ett annat webb-API för en användares räkning

För att ditt ASP.NET eller ASP.NET Core-skyddade webb-API:et ska anropa ett annat webb-API för en användares räkning måste appen skaffa en token för det nedströms webb-API:et. Om du vill hämta en token anropar appen klassen **ConfidentialClientApplication's** [AcquireTokenOnBehalfOf-metod.](https://aka.ms/msal-net-on-behalf-of) Sådana samtal kallas också *service-till-tjänster-samtal.* Webb-API:erna som anropar andra webb-API:er måste tillhandahålla anpassad cacheseriering.

  ![Ett webb-API som anropar ett annat webb-API](media/scenarios/web-api.svg)

Mer information finns i [Webb-API som anropar webb-API:er](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>En daemon app som anropar ett webb-API i demonens namn

Appar som har tidskrävande processer eller som fungerar utan användarinteraktion behöver också ett sätt att komma åt säkra webb-API:er. En sådan app kan autentisera och hämta token med hjälp av appens identitet. Appen bevisar sin identitet med hjälp av en klienthemlighet eller ett certifikat.

Du kan skriva sådana daemon-appar som hämtar en token för den anropande appen med hjälp av klassen MSAL **ConfidentialClientApplications** [klientautentiseringsuppgifter.](https://aka.ms/msal-net-client-credentials) Dessa metoder kräver att den anropande appen har registrerat en hemlighet med Azure AD. Appen delar sedan hemligheten med den kallade demonen. Exempel på sådana hemligheter är programlösenord, certifikatpåstående eller klientpåstående.

![En daemonapp som anropas av andra appar och API:er](media/scenarios/daemon-app.svg)

Mer information finns i [Daemon-programmet som anropar webb-API:er](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenarier och autentiseringsflöden som stöds

Scenarier som innebär att du hämtar token mappas också till OAuth 2.0-autentiseringsflöden. Mer information finns i [OAuth 2.0- och OpenID Connect-protokoll på Microsofts identitetsplattform](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scenario</th> <th>Detaljerad genomgång av scenario</th> <th>OAuth 2.0 flöde och bidrag</th> <th>Målgrupp</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Ensidesapp</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicit</a></td>
   <td>Arbets- eller skolkonton, personliga konton och Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">En webbapp som loggar in användare</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Auktoriseringskod</a></td>
   <td>Arbets- eller skolkonton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">En webbapp som anropar webb-API:er</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Auktoriseringskod</a></td>
   <td>Arbets- eller skolkonton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">En skrivbordsapp som anropar webb-API:er</a></td>
   <td>Interaktiv med hjälp av <a href="v2-oauth2-auth-code-flow.md">auktoriseringskod</a> med PKCE</td>
   <td>Arbets- eller skolkonton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td>Integrerad Windows-Auth</td>
   <td>Arbets- eller skolkonton</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Lösenord för resursägare</a></td>
   <td>Arbets- eller skolkonton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Enhetskod</a></td>
   <td>Arbets- eller skolkonton</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">En mobilapp som anropar webb-API:er</a></td>
   <td>Interaktiv med hjälp av <a href="v2-oauth2-auth-code-flow.md">auktoriseringskod</a> med PKCE</td>
   <td>Arbets- eller skolkonton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Lösenord för resursägare</a></td>
   <td>Arbets- eller skolkonton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">En daemonapp som anropar webb-API:er</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Klientautentiseringsuppgifter</a></td>
   <td>Endast appbehörigheter utan användare och används endast i Azure AD-organisationer</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Ett webb-API som anropar webb-API:er</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">På uppdrag av</a></td>
   <td>Arbets- eller skolkonton och personliga konton</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenarier och plattformar och språk som stöds

Microsoft Authentication-bibliotek stöder flera plattformar:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- Inbyggt iOS
- macOS
- Inbyggd Android
- Java
- Python

Du kan också använda olika språk för att skapa dina program.

> [!NOTE]
> Vissa programtyper är inte tillgängliga på alla plattformar.

I kolumnen Windows i följande tabell, varje gång .NET Core nämns, är .NET Framework också möjligt. Det senare utelämnas för att undvika att tabellen belamrats.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Ensidesapp](scenario-spa-overview.md) <br/>[![Ensidig app](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md) <br/>[![Webbapp som loggar in användare](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Webbapp som anropar webb-API:er](scenario-web-app-call-api-overview.md) <br/> <br/>[![Webbapp som anropar webb-API:er](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL Python
| [Datorprogram som anropar webb-API:er](scenario-desktop-overview.md) <br/> <br/>Skrivbordsapp som anropar webb-API:er [ ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![Enhetskodflöde](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS / Mål C eller snabb](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Mobilapp att anropar webb-API:er](scenario-mobile-overview.md) <br/> [![Mobilapp att anropar webb-API:er](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Mål C eller snabb](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon-app](scenario-daemon-overview.md) <br/> [![Daemon-app](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Webb-API som anropar webb-API:er](scenario-web-api-call-api-overview.md) <br/><br/> [![Webb-API som anropar webb-API:er](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Mer information finns i [Microsoft-stödda bibliotek efter OPERATIVSYSTEM/språk](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [grunderna i autentisering](authentication-scenarios.md) och [microsoft-åtkomsttoken för identitetsplattform .](access-tokens.md)
* Läs mer om [att skydda åtkomsten till IoT-appar](/azure/architecture/example-scenario/iot-aad/iot-aad).
