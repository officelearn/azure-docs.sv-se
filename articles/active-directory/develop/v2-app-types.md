---
title: Programtyper för v2.0 | Azure
description: Typer av appar och scenarier som stöds av Azure Active Directory v2.0-slutpunkten.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 94a072d3c8bde9e4fb16342db1e0705bfc22cf77
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539593"
---
# <a name="application-types-for-v20"></a>Programtyper för v2.0

V2.0-slutpunkten Azure Active Directory (Azure AD) stöder autentisering för en mängd olika moderna apparkitekturer dem utifrån branschstandardprotokollen alla [OAuth 2.0 eller OpenID Connect](active-directory-v2-protocols.md). Den här artikeln beskrivs vilka typer av appar som du kan skapa med hjälp av Azure AD v2.0, oavsett dina önskade språk eller plattform. Informationen i den här artikeln är utformad för att hjälpa dig att förstå övergripande scenarierna innan du [börjar arbeta med kod](v2-overview.md#getting-started).

> [!NOTE]
> V2.0-slutpunkten stöder inte alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Grunderna

Du måste registrera varje app som använder v2.0-slutpunkten i den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com). Registreringsprocessen samlar in och tilldelar värdena för din app:

* En **program-ID** som unikt identifierar din app
* En **omdirigerings-URI** som du kan använda för att dirigera svar tillbaka till din app
* Några andra scenariespecifika värden

Mer information lär du dig hur du [registrera en app](quickstart-v2-register-an-app.md).

När appen har registrerats kan kommunicerar appen med Azure AD genom att skicka förfrågningar till Azure AD v2.0-slutpunkten. Vi tillhandahåller öppen källkod, ramverk och bibliotek som hanterar information om dessa begäranden. Du har också möjlighet att implementera autentiseringslogiken själv genom att skapa begäranden till dessa slutpunkter:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Enkelsidiga appar (JavaScript)

Många moderna appar innehåller en ensidesapp klientdel som främst är skriven i JavaScript. De skrivs ofta med hjälp av ett ramverk som AngularJS, Ember.js eller Durandal.js. Azure AD v2.0-slutpunkten har stöd för dessa appar med hjälp av den [OAuth 2.0 implicit flöde](v2-oauth2-implicit-grant-flow.md).

I det här flödet appen tar emot token direkt från v2.0 tillåta slutpunkt utan några server-till-server-utbyten. Placera helt i JavaScript-klient, utan extra sidomdirigeringar alla autentiseringslogiken och hantering av tar-session.

![Implicit autentiseringsflödet](./media/v2-app-types/convergence_scenarios_implicit.png)

Om du vill se det här scenariot fungerar i praktiken kan du prova någon av ensidesapp kodexemplen i den [v2.0 har börjat](v2-overview.md#getting-started) avsnittet.

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

Ytterligare information om olika typer av token som används i v2.0-slutpunkten är tillgängliga i den [åtkomsttoken](access-tokens.md) referens och [ `id_token` referens](id-tokens.md)

I web server-appar utförs logga in autentiseringsflödet följande övergripande steg:

![Autentiseringsflödet för Web app](./media/v2-app-types/convergence_scenarios_webapp.png)

Du kan kontrollera användarens identitet genom att verifiera ID-token med en offentlig signeringsnyckel som fås från v2.0-slutpunkten. En sessionscookie har angetts som kan användas för att identifiera användaren vid efterföljande sidförfrågningar.

Om du vill se det här scenariot fungerar i praktiken kan du prova någon av web app-inloggningen kodexemplen i den [v2.0 har börjat](v2-overview.md#getting-started) avsnittet.

Förutom att enkel inloggning, kan en behöva åtkomst till en annan webbtjänst, till exempel ett REST-API. I det här fallet server webbappen bedriver ett kombinerade OpenID Connect och OAuth 2.0-flöde med hjälp av den [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols.md). Mer information om det här scenariot finns i avsnittet om [komma igång med web apps och Web API: erna](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webb-API:er

Du kan använda v2.0-slutpunkten för att skydda webbtjänster, till exempel appens RESTful webb-API. I stället för ID-token och sessionscookies använder ett webb-API en OAuth 2.0-åtkomsttoken för att skydda sina data och autentiserar inkommande begäranden. Anroparen av ett webb-API lägger till en åtkomst-token i auktoriseringshuvudet för en HTTP-begäran, så här:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API använder åtkomsttoken för att verifiera API-Anroparens identitet och extrahera information om anroparen från anspråk som kodas i åtkomsttoken. Ytterligare information om olika typer av token som används i v2.0-slutpunkten är tillgängliga i den [åtkomsttoken](access-tokens.md) referens och [ `id_token` referens](id-tokens.md)

Ett webb-API kan ge användarna kraften att anmäla eller avanmäla dig från funktioner eller data genom att exponera behörigheter, även kallat [scope](v2-permissions-and-consent.md). För en anropande app att få behörighet att ett omfång måste användaren samtyckta till i omfånget under ett flöde. V2.0-slutpunkten frågar användaren om behörighet och registrerar sedan behörigheter i alla åtkomsttoken som tar emot webb-API. Webb-API verifierar åtkomsttoken tas emot på varje anrop och utför auktoriseringskontroller.

Ett webb-API kan ta emot åtkomsttoken från alla typer av appar, inklusive web server-appar, desktop och mobilappar, enkelsidiga appar, server-deamon och andra webb-API: er. Övergripande flödet för en webb-API som ser ut så här:

![Autentiseringsflödet för webb-API](./media/v2-app-types/convergence_scenarios_webapi.png)

Om du vill lära dig att skydda ett webb-API med hjälp av OAuth2-åtkomsttoken, Kolla in webb-API-kodexempel i den [v2.0 har börjat](v2-overview.md#getting-started) avsnittet.

I många fall webb API: er måste också göra utgående begäranden till andra underordnade webb-API: er som skyddas av Azure Active Directory. Om du vill göra det web API: er kan dra nytta av Azure Active Directorys **på uppdrag av** flödet, vilket gör att ditt webb-API att byta ut en inkommande åtkomsttoken för en annan åtkomsttoken som ska användas i utgående begäranden. V2.0 slutpunkten för flödet beskrivs i [information här](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobila och interna appar

Enhet-installerade appar, till exempel appar och program behöver ofta åtkomst till backend-tjänster eller webb-API: er som lagrar data och utför funktioner för en användares räkning. De här apparna kan lägga till inloggning och auktorisering till backend-tjänster med hjälp av den [OAuth 2.0-auktoriseringskodflödet](v2-oauth2-auth-code-flow.md).

I det här flödet får appen en auktoriseringskod från v2.0-slutpunkten när användaren loggar in. Auktoriseringskoden representerar appens behörighet att anropa backend-tjänster som är inloggade användarens räkning. Appen kan byta auktoriseringskod i bakgrunden för en OAuth 2.0-åtkomsttoken och en uppdateringstoken. Appen kan använda åtkomsttoken för autentisering till webb-API: er i HTTP-begäranden och använda uppdateringstoken för att hämta nya åtkomsttoken när äldre åtkomsttoken upphör att gälla.

![Autentiseringsflödet för inbyggd app](./media/v2-app-types/convergence_scenarios_native.png)

## <a name="daemons-and-server-side-apps"></a>Daemon- och serversidan appar

Appar som har tidskrävande processer eller som fungerar utan interaktion med en användare måste också ett sätt att komma åt skyddade resurser, till exempel webb-API: er. De här apparna kan autentisera och hämta token genom att använda appens identitet, snarare än en användares delegerade identitet med hjälp av OAuth 2.0 flödet. Du kan bekräfta appens identitet med hjälp av en klienthemlighet eller ett certifikat. Mer information finns i [autentiserar till Azure AD i daemon-appar med certifikat](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

I det här flödet appen interagerar direkt med den `/token` slutpunkt för att hämta slutpunkter:

![Autentiseringsflödet för Daemon-app](./media/v2-app-types/convergence_scenarios_daemon.png)

Om du vill skapa en daemon-app, se den [klientautentiseringsuppgifter dokumentation](v2-oauth2-client-creds-grant-flow.md), eller försök en [.NET-exempelapp](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
