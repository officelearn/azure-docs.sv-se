---
title: Programtyper för Microsoft identity-plattformen | Azure
description: Typer av appar och scenarier som stöds av Microsoft identity-plattformen (v2.0) slutpunkt.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: celested
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b45f30bf057feba38a716e040ab2efc15da90
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283332"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Programtyper för Microsoft identity-plattformen

Microsoft identity-plattformen (v2.0) slutpunkt stöder autentisering för en mängd olika moderna apparkitekturer dem utifrån branschstandardprotokollen alla [OAuth 2.0 eller OpenID Connect](active-directory-v2-protocols.md). Den här artikeln beskrivs vilka typer av appar som du kan skapa med hjälp av Microsoft identity-plattformen, oavsett dina önskade språk eller plattform. Informationen är utformad för att hjälpa dig att förstå övergripande scenarierna innan du [börjar arbeta med kod](v2-overview.md#getting-started).

> [!NOTE]
> Microsoft identity-plattformen slutpunkt stöder inte alla Azure Active Directory (Azure AD)-scenarier och funktioner. Läs mer om för att avgöra om du ska använda Microsoft identity-plattformen endpoint, [plattformsbegränsningar för Microsoft identity](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Grunderna

Du måste registrera varje app som använder Microsoft identity-plattformen slutpunkt i den nya [App-registreringar portal](https://go.microsoft.com/fwlink/?linkid=2083908). Registreringsprocessen samlar in och tilldelar värdena för din app:

* En **(klient)-ID: T** som unikt identifierar din app
* En **omdirigerings-URI** som du kan använda för att dirigera svar tillbaka till din app
* Några andra scenariespecifika värden som stöds kontotyper

Mer information lär du dig hur du [registrera en app](quickstart-register-app.md).

När appen har registrerats kan kommunicerar appen med Microsoft identity-plattformen genom att skicka förfrågningar till slutpunkten. Vi tillhandahåller öppen källkod, ramverk och bibliotek som hanterar information om dessa begäranden. Du har också möjlighet att implementera autentiseringslogiken själv genom att skapa begäranden till dessa slutpunkter:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Enkelsidiga appar (JavaScript)

Många moderna appar innehåller en ensidesapp klientdel som främst är skriven i JavaScript. De skrivs ofta med hjälp av ett ramverk som AngularJS, Ember.js eller Durandal.js. Microsoft identity-plattformen slutpunkten har stöd för dessa appar med hjälp av den [OAuth 2.0 implicit flöde](v2-oauth2-implicit-grant-flow.md).

I det här flödet appen tar emot token direkt från Microsoft identity-plattformen tillåta slutpunkt utan några server-till-server-utbyten. Placera helt i JavaScript-klient, utan extra sidomdirigeringar alla autentiseringslogiken och hantering av tar-session.

![Implicit autentiseringsflödet](./media/v2-app-types/convergence-scenarios-implicit.svg)

Om du vill se det här scenariot fungerar i praktiken kan du prova någon av ensidesapp kodexemplen i den [Microsoft identity-plattformen har börjat](v2-overview.md#getting-started) avsnittet.

## <a name="web-apps"></a>Webbappar

Du kan använda för web apps (.NET, PHP, Java, Ruby, Python, Node) som användaren har åtkomst via en webbläsare, [OpenID Connect](active-directory-v2-protocols.md) för användarinloggning. Webbappen tar emot ett ID-token i OpenID Connect. Ett ID-token är en säkerhetstoken som verifierar användarens identitet och innehåller information om användaren i form av anspråk:

```
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

Det finns mer information om olika typer av token som används i Microsoft identity-plattformen slutpunkten i den [åtkomsttoken](access-tokens.md) referens och [id_token referens](id-tokens.md)

I web server-appar utförs logga in autentiseringsflödet följande övergripande steg:

![Autentiseringsflödet för Web app](./media/v2-app-types/convergence-scenarios-webapp.svg)

Du kan kontrollera användarens identitet genom att verifiera ID-token med en offentlig signeringsnyckel som fås från Microsoft identity-plattformen slutpunkten. En sessionscookie har angetts som kan användas för att identifiera användaren vid efterföljande sidförfrågningar.

Om du vill se det här scenariot fungerar i praktiken kan du prova någon av web app-inloggningen kodexemplen i den [Microsoft identity-plattformen har börjat](v2-overview.md#getting-started) avsnittet.

Förutom att enkel inloggning, kan en behöva åtkomst till en annan webbtjänst, till exempel ett REST-API. I det här fallet server webbappen bedriver ett kombinerade OpenID Connect och OAuth 2.0-flöde med hjälp av den [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols.md). Mer information om det här scenariot finns i avsnittet om [komma igång med web apps och Web API: erna](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webb-API:er

Du kan använda Microsoft identity-plattformen slutpunkten för att skydda webbtjänster, till exempel appens RESTful webb-API. I stället för ID-token och sessionscookies använder ett webb-API en OAuth 2.0-åtkomsttoken för att skydda sina data och autentiserar inkommande begäranden. Anroparen av ett webb-API lägger till en åtkomst-token i auktoriseringshuvudet för en HTTP-begäran, så här:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API använder åtkomsttoken för att verifiera API-Anroparens identitet och extrahera information om anroparen från anspråk som kodas i åtkomsttoken. Det finns mer information om olika typer av token som används i Microsoft identity-plattformen slutpunkten i den [åtkomsttoken](access-tokens.md) referens och [id_token referens](id-tokens.md)

Ett webb-API kan ge användarna kraften att anmäla eller avanmäla dig från funktioner eller data genom att exponera behörigheter, även kallat [scope](v2-permissions-and-consent.md). För en anropande app att få behörighet att ett omfång måste användaren samtyckta till i omfånget under ett flöde. Microsoft identity-plattformen endpoint frågar användaren om behörighet och registrerar sedan behörigheter i alla åtkomsttoken som tar emot webb-API. Webb-API verifierar åtkomsttoken tas emot på varje anrop och utför auktoriseringskontroller.

Ett webb-API kan ta emot åtkomsttoken från alla typer av appar, inklusive web server-appar, desktop och mobilappar, enkelsidiga appar, server-deamon och andra webb-API: er. Övergripande flödet för en webb-API som ser ut så här:

![Autentiseringsflödet för webb-API](./media/v2-app-types/convergence-scenarios-webapi.svg)

Om du vill lära dig att skydda ett webb-API med hjälp av OAuth2-åtkomsttoken, Kolla in webb-API-kodexempel i den [Microsoft identity-plattformen har börjat](v2-overview.md#getting-started) avsnittet.

I många fall webb API: er måste också göra utgående begäranden till andra underordnade webb-API: er som skyddas av Microsoft identity-plattformen. Du vill göra det web API: er kan dra nytta av den **On-Behalf-Of** flödet, vilket gör att ditt webb-API att byta ut en inkommande åtkomsttoken för en annan åtkomsttoken som ska användas i utgående begäranden. Mer information finns i [Microsoft identity-plattformen och OAuth 2.0 Behalf flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobila och interna appar

Enhet-installerade appar, till exempel appar och program behöver ofta åtkomst till backend-tjänster eller webb-API: er som lagrar data och utför funktioner för en användares räkning. De här apparna kan lägga till inloggning och auktorisering till backend-tjänster med hjälp av den [OAuth 2.0-auktoriseringskodflödet](v2-oauth2-auth-code-flow.md).

I det här flödet får appen en auktoriseringskod från Microsoft identity-plattformen slutpunkten när användaren loggar in. Auktoriseringskoden representerar appens behörighet att anropa backend-tjänster som är inloggade användarens räkning. Appen kan byta auktoriseringskod i bakgrunden för en OAuth 2.0-åtkomsttoken och en uppdateringstoken. Appen kan använda åtkomsttoken för autentisering till webb-API: er i HTTP-begäranden och använda uppdateringstoken för att hämta nya åtkomsttoken när äldre åtkomsttoken upphör att gälla.

![Autentiseringsflödet för inbyggd app](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemon- och serversidan appar

Appar som har tidskrävande processer eller som fungerar utan interaktion med en användare måste också ett sätt att komma åt skyddade resurser, till exempel webb-API: er. De här apparna kan autentisera och hämta token genom att använda appens identitet, snarare än en användares delegerade identitet med hjälp av OAuth 2.0 flödet. Du kan bekräfta appens identitet med hjälp av en klienthemlighet eller ett certifikat. Mer information finns i [autentisering till Microsoft identity-plattformen i daemon-appar med certifikat](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

I det här flödet appen interagerar direkt med den `/token` slutpunkt för att få åtkomst:

![Autentiseringsflödet för Daemon-app](./media/v2-app-types/convergence-scenarios-daemon.svg)

Om du vill skapa en daemon-app, se den [klientautentiseringsuppgifter dokumentation](v2-oauth2-client-creds-grant-flow.md), eller försök en [.NET-exempelapp](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
