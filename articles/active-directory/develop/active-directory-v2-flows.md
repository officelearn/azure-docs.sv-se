---
title: Apptyper för Azure Active Directory v2.0-slutpunkten | Microsoft Docs
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
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 134199c879f6793cb4ed0a88cf0593786341f6d8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Apptyper för Azure Active Directory v2.0-slutpunkten
Azure Active Directory (AD Azure) v2.0-slutpunkten stöder autentisering för en rad olika moderna apparkitekturer alla baserat på branschstandardprotokollen [OAuth 2.0- eller OpenID Connect](active-directory-v2-protocols.md). Den här artikeln beskriver typerna av appar som du kan skapa med hjälp av Azure AD v2.0, oavsett din önskat språk eller en plattform. Informationen i den här artikeln är utformat för att hjälpa dig att förstå övergripande scenarierna innan du [börjar arbeta med kod](active-directory-appmodel-v2-overview.md#getting-started).

> [!NOTE]
> V2.0-slutpunkten stöder inte alla Azure Active Directory-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

## <a name="the-basics"></a>Grunderna
Du måste registrera varje app som använder v2.0-slutpunkten i den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com). Registreringsprocessen samlar in och tilldelar dessa värden för din app:

* En **program-ID** som unikt identifierar din app
* En **omdirigerings-URI** som du kan använda för att dirigera svar tillbaka till din app
* Några andra scenariespecifika värden

Mer information lär du dig hur du [registrera en app](active-directory-v2-app-registration.md).

När appen har registrerats kommunicerar appen med Azure AD genom att skicka förfrågningar till Azure AD v2.0-slutpunkten. Vi ger öppen källkod ramverk och bibliotek som ska hantera information om dessa begäranden. Du har också möjlighet att implementera autentiseringslogiken själv genom att skapa begäranden till dessa slutpunkter:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Webbappar
Du kan använda för webbprogram (.NET, PHP, Java, Ruby, Python, nod) som användaren kommer åt via en webbläsare, [OpenID Connect](active-directory-v2-protocols.md) för användarinloggning. Tar emot en ID-token i OpenID Connect webbprogrammet. Ett ID-token är en säkerhetstoken som verifierar användarens identitet och innehåller information om användaren i form av anspråk:

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

Du kan lära dig om alla typer av token och anspråk som är tillgängliga för en app i den [v2.0 tokens referens](active-directory-v2-tokens.md).

I web server apps utförs inloggning autentiseringsflödet följande övergripande steg:

![Autentiseringsflödet för Web app](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Du kan kontrollera användarens identitet genom att verifiera ID-token med en offentlig signeringsnyckel som tas emot från v2.0-slutpunkten. En sessionscookie har angetts som kan användas för att identifiera användaren vid efterföljande sidförfrågningar.

Om du vill se det här scenariot fungerar i praktiken provar du något av web app inloggning kodexemplen i vår v2.0 [komma igång](active-directory-appmodel-v2-overview.md#getting-started) avsnitt.

Förutom enkel inloggning behöver ett webbprogram server komma åt en annan webbtjänst, till exempel en REST-API. I det här fallet server webbprogrammet bedriver ett kombinerade OpenID Connect och OAuth 2.0-flöde med hjälp av den [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols.md). Mer information om det här scenariot finns i avsnittet om [komma igång med webbappar och webb-API: er](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webb-API:er
Du kan använda v2.0-slutpunkten för att skydda webbtjänster, till exempel appens RESTful webb-API. I stället för ID-token och sessionscookies använder webb-API en OAuth 2.0-åtkomsttoken att skydda data och autentisera inkommande begäranden. Anroparen av en webb-API lägger till en åtkomst-token i auktoriseringshuvudet för en HTTP-begäran, så här:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API använder åtkomsttoken att verifiera API-Anroparens identitet och att extrahera information om anroparen från anspråk som kodas i åtkomsttoken. Läs om vilka typer av token och anspråk som är tillgängliga för en app i den [v2.0 tokens referens](active-directory-v2-tokens.md).

En webb-API kan ge användare möjlighet att välja eller välja bort funktioner eller data genom att exponera behörigheter, även kallat [scope](active-directory-v2-scopes.md). För en anropande app att hämta behörighet till ett omfång måste användaren samtyckta till i omfånget under ett flöde. V2.0-slutpunkten frågar användaren om behörighet och sedan registrerar behörigheter i alla åtkomsttoken som tar emot webb-API. Webb-API verifierar åtkomsttoken tas emot på varje anrop och kontrollerar auktorisering.

En webb-API kan få åtkomst-token från alla typer av appar, inklusive server webbprogram, skrivbord och mobila appar, sida appar, server-deamon och även andra webb-API: er. Det övergripande flödet för en webb-API som ser ut så här:

![Autentiseringsflödet för webb-API](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Om du vill veta hur du skyddar ett webb-API med hjälp av OAuth2 åtkomsttoken kolla Web API-kodexempel i vår [komma igång](active-directory-appmodel-v2-overview.md#getting-started) avsnitt.

I många fall web API: er måste också gör utgående förfrågningar till andra underordnade webb-API: er som skyddas av Azure Active Directory. Om du vill göra det, web API: er kan dra nytta av Azure AD **på uppdrag av** flöde, där webb-API att utbyta ett inkommande åtkomst-token för en annan åtkomsttoken som ska användas i utgående förfrågningar. V2.0 slutpunktens uppdrag flödet beskrivs i [detaljerat här](active-directory-v2-protocols-oauth-on-behalf-of.md).

## <a name="mobile-and-native-apps"></a>Mobila och interna appar
Enheten installerade appar, till exempel appar och program behöver ofta åtkomst till backend-tjänster eller webb-API: er som lagrar data och utföra funktioner för en användares räkning. De här apparna kan lägga till inloggning och auktorisering backend-tjänster med hjälp av den [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols-oauth-code.md).

I det här flödet appen tar emot en Auktoriseringskoden från v2.0-slutpunkten när användaren loggar in. Auktoriseringskoden representerar appens behörighet att anropa backend-tjänster för användarens räkning som är inloggad. Appen kan utbyta auktoriseringskod i bakgrunden för en OAuth 2.0-åtkomsttoken och en uppdateringstoken. Appen kan använda åtkomst-token för att autentisera till webb-API: er i HTTP-begäranden och använder uppdateringstoken för att hämta ny åtkomsttoken när äldre åtkomst-token upphör att gälla.

![Autentiseringsflödet för inbyggda appen](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Single-page-appar (JavaScript)
Många moderna appar innehåller en enda sida app-klientdel som främst är skriven i JavaScript. Ofta skrivs med hjälp av ett ramverk som AngularJS, Ember.js eller Durandal.js. Azure AD v2.0-slutpunkten har stöd för dessa appar med hjälp av den [implicita flödet för OAuth 2.0](active-directory-v2-protocols-implicit.md).

I det här flödet appen tar emot token direkt från v2.0 auktorisera slutpunkt, utan några server-till-server-utbyte. Alla autentiseringslogiken och session hantering tar placera helt i JavaScript-klienten utan extra sidomdirigeringar.

![Implicit autentiseringsflödet](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Om du vill se det här scenariot fungerar i praktiken provar du något av kodexemplen sida app i vår [komma igång](active-directory-appmodel-v2-overview.md#getting-started) avsnitt.

## <a name="daemons-and-server-side-apps"></a>Daemon och serversidan appar
Appar som har tidskrävande processer eller som fungerar utan interaktion med användaren måste också ett sätt att komma åt skyddade resurser, till exempel webb-API: er. De här apparna kan autentisera och hämta token genom att använda appens identitet i stället en användares delegerade identitet med hjälp av OAuth 2.0-klientautentiseringsuppgifter.

I det här flödet appen kommunicerar direkt med den `/token` slutpunkten för att hämta slutpunkter:

![Daemon för app-flöde för autentisering](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Om du vill skapa en daemon app finns i [klienten autentiseringsuppgifter dokumentationen](active-directory-v2-protocols-oauth-client-creds.md), eller försök en [.NET sample-appen](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
