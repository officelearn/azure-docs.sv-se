---
title: Autentiserings scenarier för Microsoft Identity Platform | Azure
description: 'Lär dig mer om autentiserings flöden och program scenarier för Microsoft Identity Platform. Lär dig mer om de olika typerna av program som kan autentisera identiteter, Hämta token och anropa skyddade API: er.'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5330111e5ae56471d26ebc39dca1a036246945e1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348640"
---
# <a name="authentication-flows-and-application-scenarios"></a>Autentiserings flöden och program scenarier

Slut punkten för Microsoft Identity Platform (v 2.0) stöder autentisering för en mängd moderna app-arkitekturer, som alla baseras på bransch standard protokollen [OAuth 2,0 eller OpenID Connect](active-directory-v2-protocols.md).  Med hjälp av [autentiseringsscheman](reference-v2-libraries.md)kan program autentisera identiteter och hämta token för att få åtkomst till skyddade API: er. I den här artikeln beskrivs olika autentiserings flöden och program scenarier som de används i.  Den här artikeln innehåller också listor över [program scenarier och stödda autentiserings flöden](#scenarios-and-supported-authentication-flows) och [program scenarier och plattformar och språk som stöds](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Program kategorier

Du kan hämta token från ett antal program typer: Webb program, mobil-eller Skriv bords program, webb-API: er och program som körs på enheter som inte har någon webbläsare (eller iOT). Program kan kategoriseras med följande:

- [Skyddade resurser jämfört med klient program](#protected-resources-vs-client-applications). Vissa scenarier är om att skydda resurser (Web Apps eller webb-API: er) och andra kommer att förvärva en säkerhetstoken för att anropa ett skyddat webb-API.
- [Med användare eller utan användare](#with-users-or-without-users). Vissa scenarier involverar en inloggad användare, medan andra inte involverar en användare (daemon-scenarier).
- [Program med en sida, offentliga klient program och konfidentiella klient program](#single-page-applications-public-client-applications-and-confidential-client-applications). Detta är tre stora kategorier av program typer. Biblioteken och objekten som används för att manipulera dem är olika.
- [Inloggnings mål grupp](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types). Vissa autentiserings flöden är inte tillgängliga för vissa inloggnings grupper. Vissa flöden är bara tillgängliga för arbets-eller skol konton och vissa är tillgängliga för både arbets-eller skol konton och personliga Microsoft-konton. Den tillåtna mål gruppen beror på autentiserings flöden.
- [OAuth 2,0-flöden som stöds](#scenarios-and-supported-authentication-flows).  Autentiserings flöden används för att implementera program scenarier som begär token.  Det finns ingen en-till-en-mappning mellan program scenarier och autentiserings flöden.
- [Plattformar som stöds](#scenarios-and-supported-platforms-and-languages). Alla program scenarier är inte tillgängliga för varje plattform.

### <a name="protected-resources-vs-client-applications"></a>Skyddade resurser jämfört med klient program

Autentiserings scenarier omfattar två aktiviteter:

- **Hämtar säkerhetstoken** för ett skyddat webb-API. Microsoft rekommenderar att du använder [autentiseringsscheman](reference-v2-libraries.md#microsoft-supported-client-libraries) för att hämta tokens, i synnerhet Microsoft Authentication libraries-serien (MSAL)
- **Skydda ett webb-API** (eller en webbapp). Ett av utmaningarna med att skydda en resurs (webbapp eller webb-API) är att verifiera säkerhetstoken. Microsoft erbjuder på vissa plattformar, [mellan bibliotek](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Med användare eller utan användare

De flesta autentiseringar hämtar token åt en (inloggad) **användare**.

![scenarier med användare](media/scenarios/scenarios-with-users.svg)

Det finns dock också scenarier (daemon-appar), där program hämtar token åt sig själva (utan användare).

![daemon-appar](media/scenarios/daemon-app.svg)

### <a name="single-page-applications-public-client-applications-and-confidential-client-applications"></a>Program med en sida, offentliga klient program och konfidentiella klient program

Säkerhetstoken kan hämtas från ett antal program typer. Program tenderar att delas upp i tre kategorier:

- **Program med en sida** (SPA) är en form av webb program där tokens hämtas från appen som körs i webbläsaren (skrivet i Java Script eller typescript). Många moderna appar har en app-klient med en enda sida som främst är skriven i Java Script. Appen skrivs ofta med hjälp av ett ramverk som vinkel, reagera eller Vue. MSAL. js är det enda Microsoft Authentication Library som stöder program med en sida.

- **Offentliga klient program** loggar alltid in användare. De här apparna är:
  - Skriv bords program som anropar webb-API: er å den inloggade användarens vägnar.
  - Mobil program.
  - En tredje kategori av program som körs på enheter som inte har en webbläsare (webbläsarbaserade appar som körs på iOT för instansen).

  De representeras av klassen MSAL med namnet [PublicClientApplication](msal-client-applications.md).

- **Konfidentiella klient program**
  - Webb program som anropar ett webb-API
  - Webb-API: er som anropar ett webb-API
  - Daemon-program (även när de implementeras som en konsol tjänst som en daemon på Linux eller en Windows-tjänst)
 
  Dessa typer av appar använder [ConfidentialClientApplication](msal-client-applications.md)

## <a name="application-scenarios"></a>Programscenarier

Slut punkten för Microsoft Identity Platform stöder autentisering för en rad olika app-arkitekturer: appar med en sida, webbappar, webb-API: er, mobila och inbyggda appar samt daemon och appar på Server sidan.  Programmen använder olika autentiserings flöden för att logga in användare och hämta token för att anropa skyddade API: er.

### <a name="single-page-application"></a>Enkelsidig app

Många moderna webb program är skapade för program på klient sidan som skrivits med hjälp av Java Script eller ett SPA-ramverk, till exempel vinkel, Vue. js och reagerar. js. Dessa program körs i en webbläsare och har olika egenskaper för autentisering än traditionella webb program på Server sidan. Microsoft Identity Platform gör det möjligt för program på en sida att logga in användare och hämta token för att få åtkomst till backend-tjänster eller webb-API: er.

![Enkelsidig app](media/scenarios/spa-app.svg)

Mer information finns i [program med en enda sida](scenario-spa-overview.md).

### <a name="web-application-signing-in-a-user"></a>Webb programs signering – i en användare

![Webbapp loggar in användare](media/scenarios/scenario-webapp-signs-in-users.svg)

Om du vill **skydda en webbapp** (logga in användaren) använder du:

- I .NET World, ASP.NET eller ASP.NET Core med ASP.NET Open ID Connect mellan. Skyddet av en resurs innebär att säkerhetstoken verifieras, vilket görs av [IdentityModel-tilläggen för .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -bibliotek, inte MSAL-bibliotek

- Om du utvecklar i Node. js använder du Passport. js.

Mer information hittar du i [webbapp som loggar in användare](scenario-web-app-sign-user-overview.md).

### <a name="web-application-signing-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Webb programs signering – en användare och anropar ett webb-API för användarens räkning

![Webb program anropar webb-API: er](media/scenarios/web-app.svg)

Använd MSAL `ConfidentialClientApplication` från webbappen för att **anropa webb-API: et** för användarens räkning. Du använder Flow-flödet för att lagra den hämtade token i token-cachen. Sedan kommer kontrollanten att hämta token tyst från cachen vid behov. MSAL uppdaterar token vid behov.

Mer information hittar du i [webb-API: er för webb program](scenario-web-app-call-api-overview.md).

### <a name="desktop-application-calling-a-web-api-on-behalf-of-the-signed-in-user"></a>Skriv bords program som anropar ett webb-API å den inloggade användarens vägnar

Om du vill anropa ett webb-API från ett Skriv bords program som loggar in användare använder du MSAL PublicClientApplication's metoder för interaktiv token-hämtning. Med dessa interaktiva metoder kan du styra inloggnings GRÄNSSNITTs upplevelsen. För att aktivera den här interaktionen utnyttjar MSAL en webbläsare.

![Skrivbord](media/scenarios/desktop-app.svg)

För Windows-värdbaserade program som körs på datorer som är anslutna till en Windows-domän eller AAD-ansluten finns det en annan möjlighet. Dessa program kan hämta en token tyst med hjälp av [integrerad Windows-autentisering](https://aka.ms/msal-net-iwa).

Program som körs på en enhet utan webbläsare kommer fortfarande att kunna anropa ett API för en användares räkning. Användaren måste logga in på en annan enhet som har en webbläsare för att autentiseringen ska kunna autentiseras. Om du vill aktivera det här scenariot måste du använda [enhets kod flödet](https://aka.ms/msal-net-device-code-flow)

![Enhetskodflöde](media/scenarios/device-code-flow-app.svg)

Slutligen, även om det inte rekommenderas, kan du använda [användar namn/lösen ord](https://aka.ms/msal-net-up) i offentliga klient program. Det här flödet behövs fortfarande i vissa scenarier (t. ex. DevOps), men tänk på att använda det för att införa begränsningar i ditt program. Till exempel kan appar som använder det här flödet inte logga in en användare som behöver utföra Multi-Factor Authentication (villkorlig åtkomst). Det gör inte att ditt program kan dra nytta av enkel inloggning. Autentisering med användar namn/lösen ord går mot principerna för modern autentisering och tillhandahålls endast av tidigare skäl.

Om du vill att token cache ska vara beständig i Skriv bords program bör du [Anpassa cachelagringen av token](https://aka.ms/msal-net-token-cache-serialization). Du kan även aktivera omvänd och vidarebefordra kompatibla token-cacheminnen med tidigare generationer av autentiseringspaket (ADAL.NET 3. x och 4. x) genom att implementera [serialisering av cachelagring med dubbla token](https://aka.ms/msal-net-dual-cache-serialization).

Mer information finns i [Skriv bords program som anropar webb-API: er](scenario-desktop-overview.md).

### <a name="mobile-application-calling-a-web-api-on-behalf-of-the-user-whos-signed-in-interactively"></a>Mobil program som anropar ett webb-API åt användaren som är inloggad interaktivt

På liknande sätt som Skriv bords program använder ett mobil program MSAL PublicClientApplication's-metoder för interaktiv token för att hämta en token för att anropa ett webb-API.

![mobila](media/scenarios/mobile-app.svg)

MSAL iOS och MSAL Android använder som standard system webbläsare. Du kan dock också dirigera den till att använda den inbäddade webbvy. Det finns olika information beroende på den mobila plattformen: (UWP, iOS, Android).

Vissa scenarier, med villkorlig åtkomst som rör enhets-ID: t eller en enhet som registreras kräver att en [Broker](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) installeras på en enhet. Exempel på utjämnare är Microsofts företags portal (på Android), Microsoft Authenticator (Android och iOS). MSAL kan nu interagera med utjämnare.

> [!NOTE]
> Din mobilapp (med MSAL. iOS, MSAL. Android eller MSAL.NET/Xamarin) kan ha skydds principer för appar tillämpade på den (till exempel förhindra att användaren kopierar viss skyddad text). Detta [hanteras av Intune](https://docs.microsoft.com/intune/app-sdk) och identifieras av Intune som en hanterad app. [INTUNE SDK](https://docs.microsoft.com/intune/app-sdk-get-started) är skilt från MSAL-bibliotek och den pratar med AAD på egen hand.

Mer information hittar du i [mobilapp som anropar webb-API: er](scenario-mobile-overview.md).

### <a name="protected-web-api"></a>Skyddat webb-API

Du kan använda Microsoft Identity Platform-slutpunkten för att skydda webb tjänster, till exempel appens RESTful-webb-API. Ett skyddat webb-API anropas med en åtkomsttoken för att skydda dess data och för att autentisera inkommande begär Anden. Anroparen för ett webb-API lägger till en åtkomsttoken i Authorization-huvudet för en HTTP-begäran. Om du vill skydda ditt ASP.NET-eller ASP.NET Core-webb-API måste du verifiera åtkomst-token. För det här använder du ASP.NET JWT mellanprogram. Verifieringen görs under huven av IdentityModel-tilläggen [för .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -biblioteket, inte MSAL.net

Mer information finns i avsnittet om [skyddad webb-API](scenario-protected-web-api-overview.md).

### <a name="web-api-calling-another-downstream-web-api-on-behalf-of-the-user-for-whom-it-was-called"></a>Webb-API som anropar ett annat underordnat webb-API för den användares räkning som det kallades

Om du dessutom vill att ditt ASP.NET-eller ASP.NET Core-skyddade webb-API ska anropa ett annat webb-API för användarens räkning måste appen Hämta en token för det underordnade webb-API: et genom att använda ConfidentialClientApplication: s metod för att hämta en token [åt en användare](https://aka.ms/msal-net-on-behalf-of). Detta kallas även för tjänst-till-tjänst-anrop.
Webb-API: er som anropar andra webb-API: er måste också tillhandahålla en anpassad cachelagring

  ![Webb-API](media/scenarios/web-api.svg)

Mer information hittar du i [webb-API som anropar webb-API: er](scenario-web-api-call-api-overview.md).

### <a name="desktopservice-or-web-daemon-application-calling-web-api-without-a-user-in-its-own-name"></a>Skriv bord/tjänst eller webbdaemon-program anropar webb-API utan användare (i eget namn)

Appar som har långvariga processer eller som fungerar utan användar interaktion behöver också ett sätt att komma åt säkra webb-API: er. Dessa appar kan autentisera och hämta token genom att använda appens identitet i stället för en användares delegerade identitet. De visar sin identitet med hjälp av en klient hemlighet eller ett certifikat.
Du kan skriva sådana appar (daemon-appen) som hämtar en token för appen överst med MSAL ConfidentialClientApplication's- [klientens autentiseringsuppgifter](https://aka.ms/msal-net-client-credentials) förvärvs metoder. Detta förutsätter att appen tidigare har registrerat en hemlighet (program lösen ord eller certifikat eller klient kontroll) med Azure AD, som den sedan delar med det här anropet.

![Daemon-app](media/scenarios/daemon-app.svg)

Mer information finns i [daemon-programmet som anropar webb-API: er](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenarier och stödda autentiserings flöden

Scenarier som innefattar att förvärva token mappas också till OAuth 2,0-autentiserings flöden som beskrivs i information i [Microsoft Identity Platform-protokoll](active-directory-v2-protocols.md)

<table>
 <thead>
  <tr><th>Scenario</th> <th>Detaljerad genom gång av scenario</th> <th>OAuth 2,0-flöde/-beviljande</th> <th>Målgrupp</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Enkelsidig app</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Uttrycklig</a></td>
   <td>Arbets-eller skol konton och personliga konton, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Webbapp som loggar in användare</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Auktoriseringskod</a></td>
   <td>Arbets-eller skol konton och personliga konton, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Webbapp som anropar webb-API: er</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Auktoriseringskod</a></td>
   <td>Arbets-eller skol konton och personliga konton, B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Datorprogram som anropar webb-API:er</a></td>
   <td>Interaktiv (<a href="v2-oauth2-auth-code-flow.md">auktoriseringskod</a> med PKCE)</td>
   <td>Arbets-eller skol konton och personliga konton, B2C</td>
 </tr>

  <tr>
   <td>Integrerad Windows-autentisering</td>
   <td>Arbets-eller skol konton</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Resurs ägar lösen ord</a></td>
   <td>Arbets-eller skol konton, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Enhets kod</a></td>
   <td>Arbets-eller skol konton *</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Mobilapp som anropar webb-API: er</a></td>
   <td>Interaktiv (<a href="v2-oauth2-auth-code-flow.md">auktoriseringskod</a> med PKCE)</td>
   <td>Arbets-eller skol konton och personliga konton, B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Resurs ägar lösen ord</a></td>
   <td>Arbets-eller skol konton, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls Web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href=scenario-daemon-overview.md">Daemon-app som anropar webb-API: er</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Klientautentiseringsuppgifter</a></td>
   <td>Endast app-behörigheter (ingen användare) i AAD-organisationer</td>
 </tr>

  <tr>
   <td><a href=scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href=scenario-web-api-call-api-overview.md">Webb-API som anropar webb-API: er</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">På uppdrag av</a></td>
   <td>Arbets-eller skol konton och personliga konton</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenarier och plattformar och språk som stöds

Alla program typer är inte tillgängliga på alla plattformar. Du kan också använda olika språk för att bygga dina program. Microsoft Authentication libraries stöder ett antal **plattformar** (java script, .NET Framework, .net Core, Windows 10/UWP, Xamarin. iOS, Xamarin. Android, Native iOS, Mac OS, ursprunglig Android, Java, python). I tabellen nedan, i Windows, är det också möjligt att .NET Framework varje tid i .NET Core.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Enkelsidig app](scenario-spa-overview.md) <br/>[![App med en sida](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md) <br/>[![Webbapp som loggar in användare](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Webbapp som anropar webb-API: er](scenario-web-app-call-api-overview.md) <br/> <br/>[![Webbapp som anropar webb-API: er](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL python
| [Datorprogram som anropar webb-API:er](scenario-desktop-overview.md) <br/> <br/>![ [ Skrivbordsappsomanroparwebb-API:![er](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) enhets kod flöde](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python <br/> MSAL. objc |
| [Mobilapp som anropar webb-API: er](scenario-mobile-overview.md) <br/> [![Mobilapp som anropar webb-API: er](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/mål C eller Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon-app](scenario-daemon-overview.md) <br/> [![Daemon-app](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python
| [Webb-API som anropar webb-API: er](scenario-web-api-call-api-overview.md) <br/><br/> [![Webb-API som anropar webb-API: er](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python

Se även [Microsoft-bibliotek som stöds av OS/språk](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [grundläggande autentisering](authentication-scenarios.md) och [åtkomst](access-tokens.md)-token.
