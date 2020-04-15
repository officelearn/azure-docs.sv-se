---
title: Programtyper för Microsoft identity platform | Azure
description: De typer av appar och scenarier som stöds av Microsoft identity platform (v2.0)-slutpunkten.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 143a2ec0bfbcc6997eb6d8b2599b848a509ee773
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309500"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Programtyper för Microsoft identity-plattform

Slutpunkten för Microsoft identity platform (v2.0) stöder autentisering för en mängd olika moderna apparkitekturer, alla baserade på branschstandardprotokollen [OAuth 2.0 eller OpenID Connect](active-directory-v2-protocols.md). I den här artikeln beskrivs vilka typer av appar du kan skapa med hjälp av Microsofts identitetsplattform, oavsett vilket språk eller plattform du föredrar. Informationen är utformad för att hjälpa dig att förstå scenarier på hög nivå innan du [börjar arbeta med koden](v2-overview.md#getting-started).

> [!NOTE]
> Slutpunkten för Microsoft-identitetsplattform stöder inte alla Azure Active Directory-scenarier och -funktioner (Azure AD). För att avgöra om du ska använda slutpunkten för Microsoft identity platform läser du om begränsningar av [Microsofts identitetsplattform](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Grunderna

Du måste registrera varje app som använder slutpunkten för Microsoft identity platform i den nya [appregistreringsportalen](https://go.microsoft.com/fwlink/?linkid=2083908). Appregistreringsprocessen samlar in och tilldelar dessa värden för din app:

* Ett **program -ID (klient)** som unikt identifierar din app
* En **Omdirigera URI** som du kan använda för att rikta svar tillbaka till din app
* Några andra scenariospecifika värden, till exempel kontotyper som stöds

Mer information finns i hur du [registrerar en app](quickstart-register-app.md).

När appen har registrerats kommunicerar appen med Microsofts identitetsplattform genom att skicka begäranden till slutpunkten. Vi tillhandahåller ramverk och bibliotek med öppen källkod som hanterar information om dessa begäranden. Du har också möjlighet att implementera autentiseringslogiken själv genom att skapa begäranden till dessa slutpunkter:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Ensidiga appar (JavaScript)

Många moderna appar har en ensidig app front end som främst är skriven i JavaScript. Ofta är det skrivet med hjälp av ett ramverk som Angular, React eller Vue. Slutpunkten för Microsoft-identitetsplattform stöder dessa appar med hjälp av [det implicita flödet OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

I det här flödet tar appen emot token direkt från Microsoft identity-plattformen auktorisera slutpunkt, utan några server-till-server-utbyten. All autentiseringslogik och sessionshantering sker helt i JavaScript-klienten, utan extra sidomdirigeringar.

![Visar det implicita autentiseringsflödet](./media/v2-app-types/convergence-scenarios-implicit.svg)

Om du vill se det här scenariot i praktiken kan du prova ett av de ensidiga appkodexemplen i [avsnittet Komma igång med Microsofts identitetsplattform.](v2-overview.md#getting-started)

## <a name="web-apps"></a>Webbappar

För webbappar (.NET, PHP, Java, Ruby, Python, Node) som användaren kommer åt via en webbläsare kan du använda [OpenID Connect](active-directory-v2-protocols.md) för användarloggning. I OpenID Connect får webbappen en ID-token. En ID-token är en säkerhetstoken som verifierar användarens identitet och ger information om användaren i form av anspråk:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Mer information om olika typer av token som används i slutpunkten för Microsoft-identitetsplattform finns i [referensen för åtkomsttoken](access-tokens.md) och [id_token referens](id-tokens.md)

I webbserverappar tar inloggningsautentiseringsflödet dessa steg på hög nivå:

![Visar autentiseringsflödet för webbappar](./media/v2-app-types/convergence-scenarios-webapp.svg)

Du kan säkerställa användarens identitet genom att validera ID-token med en offentlig signeringsnyckel som tas emot från slutpunkten för Microsoft-identitetsplattformen. En sessionscookie ställs in, som kan användas för att identifiera användaren på efterföljande sidförfrågningar.

Om du vill se det här scenariot i praktiken kan du prova ett av inloggningskodexemplen för webbappar i avsnittet [Komma igång med Microsofts identitetsplattform.](v2-overview.md#getting-started)

Förutom enkel inloggning kan en webbserverapp behöva åtkomst till en annan webbtjänst, till exempel ett REST API. I det här fallet ägnar webbserverappen in ett kombinerat OpenID Connect- och OAuth 2.0-flöde med hjälp av [Auktoriseringskodflödet OAuth 2.0](active-directory-v2-protocols.md). Mer information om det här scenariot finns i information om [hur du kommer igång med webbappar och webb-API:er](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webb-API:er

Du kan använda slutpunkten för Microsofts identitetsplattform för att skydda webbtjänster, till exempel appens RESTful Web API. Webb-API:er kan implementeras på många plattformar och språk. De kan också implementeras med HTTP-utlösare i Azure Functions. I stället för ID-token och sessionscookies använder ett webb-API en OAuth 2.0-åtkomsttoken för att skydda sina data och autentisera inkommande begäranden. Anroparen av ett webb-API lägger till en åtkomsttoken i auktoriseringshuvudet för en HTTP-begäran, så här:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API:et använder åtkomsttoken för att verifiera API-anroparens identitet och för att extrahera information om anroparen från anspråk som kodas i åtkomsttoken. Mer information om olika typer av token som används i slutpunkten för Microsoft-identitetsplattform finns i [referensen för åtkomsttoken](access-tokens.md) och [id_token](id-tokens.md) referens.

Ett webb-API kan ge användarna möjlighet att välja eller välja bort specifika funktioner eller data genom att exponera behörigheter, även kallade [scope](v2-permissions-and-consent.md). För att en uppringande app ska få behörighet till ett scope måste användaren samtycka till omfånget under ett flöde. Slutpunkten för Microsoft-identitetsplattform ber användaren om tillstånd och registrerar sedan behörigheter i alla åtkomsttoken som webb-API:et tar emot. Webb-API:et validerar de åtkomsttoken som tas emot för varje anrop och utför auktoriseringskontroller.

Ett webb-API kan ta emot åtkomsttoken från alla typer av appar, inklusive webbserverappar, skrivbords- och mobilappar, ensidiga appar, daemons på serversidan och även andra webb-API:er. Det högnivåflöde för ett webb-API ser ut så här:

![Visar webb-API-autentiseringsflödet](./media/v2-app-types/convergence-scenarios-webapi.svg)

Om du vill veta hur du skyddar ett webb-API med hjälp av OAuth2-åtkomsttoken finns i exemplen på webb-API-kod i avsnittet [Komma igång med Microsoft identity-plattformen.](v2-overview.md#getting-started)

I många fall måste webb-API:er också göra utgående begäranden till andra underordnade webb-API:er som skyddas av Microsofts identitetsplattform. För att göra det kan webb-API:er dra nytta av flödet **On-Behalf-Of,** som gör att webb-API:et kan utbyta en inkommande åtkomsttoken för en annan åtkomsttoken som ska användas i utgående begäranden. Mer information finns i [Microsoft identity platform och OAuth 2.0 on-Behalf-Of flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobila och interna appar

Enhetsinstallerade appar, till exempel mobila appar och skrivbordsappar, behöver ofta komma åt backend-tjänster eller webb-API:er som lagrar data och utför funktioner för en användares räkning. Dessa appar kan lägga till inloggning och auktorisering till backend-tjänster med hjälp av [OAuth 2.0 auktoriseringskodflödet](v2-oauth2-auth-code-flow.md).

I det här flödet får appen en auktoriseringskod från Microsoft identity platform slutpunkt när användaren loggar in. Auktoriseringskoden representerar appens behörighet att anropa backend-tjänster för den användare som är inloggad. Appen kan byta auktoriseringskoden i bakgrunden mot en OAuth 2.0-åtkomsttoken och en uppdateringstoken. Appen kan använda åtkomsttoken för att autentisera till webb-API:er i HTTP-begäranden och använda uppdateringstoken för att hämta nya åtkomsttoken när äldre åtkomsttoken upphör att gälla.

![Visar det inbyggda flödet för appautentisering](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Demoner och appar på serversidan

Appar som har tidskrävande processer eller som fungerar utan interaktion med en användare behöver också ett sätt att komma åt säkra resurser, till exempel webb-API:er. Dessa appar kan autentisera och hämta token med hjälp av appens identitet, i stället för en användares delegerade identitet, med OAuth 2.0-klientautentiseringsflödet. Du kan bevisa appens identitet med hjälp av en klienthemlighet eller ett certifikat. Mer information finns i [.NET Core daemon console application using Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

I det här flödet interagerar `/token` appen direkt med slutpunkten för att få åtkomst:

![Visar autentiseringsflödet för daemonappen](./media/v2-app-types/convergence-scenarios-daemon.svg)

Om du vill skapa en daemon-app läser du [dokumentationen för klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md)eller provar en [.NET-exempelapp](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
