---
title: Program typer för Microsoft Identity Platform | Azure
description: De typer av appar och scenarier som stöds av Microsoft Identity Platform (v 2.0) slut punkten.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 94cddf097f2a9e51f061909f6bdd3dcd82f18bfe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262534"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Program typer för Microsoft Identity Platform

Slut punkten för Microsoft Identity Platform (v 2.0) stöder autentisering för en mängd moderna app-arkitekturer, som alla baseras på bransch standard protokollen [OAuth 2,0 eller OpenID Connect](active-directory-v2-protocols.md). Den här artikeln beskriver de typer av appar som du kan bygga med hjälp av Microsoft Identity Platform, oavsett vilket språk eller vilken plattform du föredrar. Informationen är utformad för att hjälpa dig att förstå scenarier på hög nivå innan du [börjar arbeta med koden](v2-overview.md#getting-started).

> [!NOTE]
> Slut punkten för Microsoft Identity Platform stöder inte alla scenarier och funktioner i Azure Active Directory (Azure AD). För att avgöra om du ska använda Microsoft Identity Platform-slutpunkten läser du om [begränsningar för Microsoft Identity Platform](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Grunderna

Du måste registrera varje app som använder Microsoft Identity Platform-slutpunkten på den nya [Appregistreringar portalen](https://go.microsoft.com/fwlink/?linkid=2083908). Registrerings processen för appen samlar in och tilldelar dessa värden för din app:

* Ett **program (klient) ID** som unikt identifierar din app
* En **omdirigerings-URI** som du kan använda för att dirigera svar tillbaka till appen
* Några andra scenario-speciella värden, till exempel typer av konto typer som stöds

Mer information finns i så här [registrerar du en app](quickstart-register-app.md).

När appen har registrerats kommunicerar appen med Microsoft Identity Platform genom att skicka begär anden till slut punkten. Vi tillhandahåller ramverk för öppen källkod och bibliotek som hanterar information om dessa förfrågningar. Du kan också välja att implementera autentiseringsmetoden själv genom att skapa begär anden till dessa slut punkter:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Appar med en sida (Java Script)

Många moderna appar har en app-klient med en enda sida som främst är skriven i Java Script. De skrivs ofta med hjälp av ett ramverk som till exempel vinkel, reagera eller Vue. Slut punkten för Microsoft Identity Platform stöder de här apparna med hjälp av det [implicita flödet för OAuth 2,0](v2-oauth2-implicit-grant-flow.md).

I det här flödet tar appen emot token direkt från Microsoft Identity Platform reservering-slutpunkten utan server-till-Server-utbyten. All autentisering för autentisering och hantering av sessioner sker helt i JavaScript-klienten, utan extra sid omdirigeringar.

![Visar flödet för implicit autentisering](./media/v2-app-types/convergence-scenarios-implicit.svg)

Om du vill se hur det här scenariot fungerar kan du prova något av kod exemplen för en enda sida i avsnittet [komma igång med Microsoft Identity Platform](v2-overview.md#getting-started) .

## <a name="web-apps"></a>Webbappar

För webb program (.NET, PHP, Java, ruby, python, Node) som användaren har åtkomst till via en webbläsare kan du använda [OpenID Connect](active-directory-v2-protocols.md) för användar inloggning. I OpenID Connect tar webbappen emot en ID-token. En ID-token är en säkerhetstoken som verifierar användarens identitet och ger information om användaren i form av anspråk:

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

Mer information om olika typer av token som används i Microsoft Identity Platform-slutpunkten finns i referens för [åtkomsttoken](access-tokens.md) och [id_token referens](id-tokens.md)

I Web Server-appar använder inloggnings flödet följande steg på hög nivå:

![Visar verifierings flödet för webbappar](./media/v2-app-types/convergence-scenarios-webapp.svg)

Du kan se till att användarens identitet genom att verifiera ID-token med en offentlig signerings nyckel som tas emot från Microsoft Identity Platform-slutpunkten. En sessions-cookie har angetts, som kan användas för att identifiera användaren på efterföljande sid begär Anden.

Om du vill se det här scenariot kan du prova någon av inloggnings kod exemplen för webbappar i avsnittet [komma igång med Microsoft Identity Platform](v2-overview.md#getting-started) .

Förutom enkel inloggning kan en webbapp ha åtkomst till en annan webb tjänst, till exempel en REST API. I det här fallet deltar webb Server appen i en kombinerad OpenID Connect-och OAuth 2,0-flöde genom att använda [kod flödet för oauth 2,0-auktorisering](active-directory-v2-protocols.md). Mer information om det här scenariot finns [i komma igång med webbappar och webb-API: er](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webb-API:er

Du kan använda Microsoft Identity Platform-slutpunkten för att skydda webb tjänster, till exempel appens RESTful-webb-API. Webb-API: er kan implementeras på flera olika plattformar och på olika språk. De kan också implementeras med HTTP-utlösare i Azure Functions. I stället för ID-tokens och sessionscookies använder ett webb-API en OAuth 2,0-åtkomsttoken för att skydda dess data och för att autentisera inkommande begär Anden. Anroparen för ett webb-API lägger till en åtkomsttoken i Authorization-huvudet för en HTTP-begäran, så här:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API: et använder åtkomsttoken för att verifiera API-anroparens identitet och för att extrahera information om anroparen från anspråk som kodas i åtkomsttoken. Mer information om olika typer av token som används i Microsoft Identity Platform-slutpunkten finns i referens för [åtkomsttoken](access-tokens.md) och [id_token](id-tokens.md) referens.

Ett webb-API kan ge användare möjlighet att välja eller välja ut vissa funktioner eller data genom att exponera behörigheter, även kallade [omfång](v2-permissions-and-consent.md). För att en anropande app ska kunna erhålla behörighet till ett omfång måste användaren godkänna omfånget under ett flöde. Slut punkten för Microsoft Identity Platform ber användaren om behörighet och registrerar sedan behörigheter i alla åtkomsttoken som webb-API: et tar emot. Webb-API: et verifierar de åtkomsttoken den tar emot vid varje anrop och utför verifierings kontroller.

Ett webb-API kan ta emot åtkomsttoken från alla typer av appar, inklusive Web Server-appar, Desktop-och mobilappar, appar med en sida, daemon på Server sidan och även andra webb-API: er. Hög nivå flödet för ett webb-API ser ut så här:

![Visar autentiserings flödet för webb-API](./media/v2-app-types/convergence-scenarios-webapi.svg)

Om du vill lära dig att skydda ett webb-API med hjälp av OAuth2-åtkomsttoken kan du titta närmare på webb-API-kod exemplen i avsnittet [komma igång med Microsoft Identity Platform](v2-overview.md#getting-started) .

I många fall behöver webb-API: er också göra utgående förfrågningar till andra underordnade webb-API: er som skyddas av Microsoft Identity Platform. För att göra det kan webb-API: er dra nytta av flödet **på olika** sätt, vilket gör det möjligt för webb-API: et att utväxla en inkommande åtkomsttoken för en annan åtkomsttoken som ska användas i utgående begär Anden. Mer information finns i [Microsoft Identity Platform och OAuth 2,0 på uppdrag av Flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobila och interna appar

Enhets installerade appar, till exempel mobilappar och skrivbordsappar, behöver ofta åtkomst till backend-tjänster eller webb-API: er som lagrar data och utför funktioner för en användares räkning. De här apparna kan lägga till inloggning och auktorisering till backend-tjänster med hjälp av [OAuth 2,0 Authorization Code Flow](v2-oauth2-auth-code-flow.md).

I det här flödet tar appen emot en auktoriseringskod från slut punkten för Microsoft Identity Platform när användaren loggar in. Auktoriseringskod representerar appens behörighet att anropa backend-tjänster åt den användare som är inloggad. Appen kan utbyta auktoriseringskod i bakgrunden för en OAuth 2,0-åtkomsttoken och en uppdateringstoken. Appen kan använda åtkomsttoken för att autentisera till webb-API: er i HTTP-begäranden och använda uppdateringstoken för att hämta nya åtkomsttoken när äldre åtkomsttoken upphör att gälla.

![Visar det inbyggda flödet för app-autentisering](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemon och appar på Server Sidan

Appar som har långvariga processer eller som fungerar utan interaktion med en användare behöver också ett sätt att komma åt skyddade resurser, till exempel webb-API: er. Dessa appar kan autentisera och hämta token genom att använda appens identitet, i stället för en användares delegerade identitet, med flödet OAuth 2,0-klientautentiseringsuppgifter. Du kan bevisa appens identitet med hjälp av en klient hemlighet eller ett certifikat. Mer information finns i [.net Core daemon-konsolens program med hjälp av Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

I det här flödet samverkar appen direkt med `/token` slut punkten för att få åtkomst:

![Visar daemon-flödet för app-autentisering](./media/v2-app-types/convergence-scenarios-daemon.svg)

Om du vill bygga en daemon-app läser du [dokumentationen för klient information](v2-oauth2-client-creds-grant-flow.md)eller försöker med en [.net-exempel App](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
