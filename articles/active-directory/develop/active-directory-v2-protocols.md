---
title: Lär dig mer om auktoriseringsprotokoll som stöds av Azure AD v2.0 | Microsoft Docs
description: En guide som protokoll som stöds av Azure AD v2.0-slutpunkten.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 57a3d5fc50c2278b34fddbfba61b12b0d81a33ed
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424535"
---
# <a name="v20-protocols---oauth-20--openid-connect"></a>v2.0 protokoll – OAuth 2.0 och OpenID Connect

V2.0-slutpunkten kan använda Azure AD för identitets-as-a-service med branschens standardprotokoll, OpenID Connect och OAuth 2.0. Tjänsten är standardkompatibla, kan det finnas skillnader mellan två implementeringar av dessa protokoll. Information om här är praktiskt om du väljer att skriva din kod genom att skicka direkt och hantering av HTTP-begäranden eller använda ett bibliotek med 3 part öppen källkod i stället för att använda någon av våra [öppna bibliotek källkod](reference-v2-libraries.md).

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Grunderna

I nästan alla flöden som OAuth och OpenID Connect finns det fyra parter som ingår i exchange:

![OAuth 2.0-roller](../../media/active-directory-v2-flows/protocols_roles.png)

* Den **Auktoriseringsservern** är v2.0-slutpunkten. Den är ansvarig för att se till att användarens identitet, bevilja och återkalla åtkomst till resurser och utfärda token. Det är även känd som identitetsprovidern – den hanterar något att göra med information om användaren, deras åtkomst och förtroenderelationer mellan parterna i ett flöde på ett säkert sätt.
* Den **Resursägaren** är vanligtvis slutanvändaren. Det är den part som äger data och har rätt att tillåta tredje part att få åtkomst till den data eller resurs.
* Den **OAuth klienten** använder din app som identifieras av dess program-Id. Det är vanligtvis den part som slutanvändaren interagerar med och begär token från auktoriseringsservern. Klienten måste beviljas behörighet att komma åt resursen som ägare till resursen.
* Den **resursservern** är där resursen eller data finns. Den litar på Auktoriseringsservern för att på ett säkert sätt autentisera och auktorisera OAuth-klienten och använder ägar access_tokens så att du kan få åtkomst till en resurs.

## <a name="app-registration"></a>Appregistrering
Alla appar som använder v2.0-slutpunkten måste vara registrerade med [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) innan den kan interagera med hjälp av OAuth eller OpenID Connect. Registreringsprocessen kommer samla in & tilldela några värden till appen:

* En **program-Id** som unikt identifierar din app
* En **omdirigerings-URI** eller **Paketidentifierare** som kan användas för att dirigera svar tillbaka till din app
* Några andra scenariespecifika-värden.

Om du vill ha mer information lär du dig hur du [registrerar en app](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Slutpunkter

När registrerats är kommunicerar appen med Azure AD genom att skicka förfrågningar till v2.0-slutpunkten:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Där den `{tenant}` kan ha något av fyra olika värden:

| Värde | Beskrivning |
| --- | --- |
| `common` |Låter användare med både personliga Microsoft-konton och arbets-/ skolkonton från Azure Active Directory för att logga in i programmet. |
| `organizations` |Tillåter endast användare med arbets-/ skolkonton från Azure Active Directory för att logga in i programmet. |
| `consumers` |Kan endast användare med personliga Microsoft-konton (MSA) för att logga in i programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` |Tillåter endast användare med arbets-/ skolkonton från en viss Azure Active Directory-klient för att logga in i programmet. Du kan använda antingen det egna domännamnet i Azure AD-klient eller klientens guid-identifierare. |

Välj en viss apptyp nedan för mer information om hur du interagerar med de här slutpunkterna.

## <a name="tokens"></a>Token

V2.0-implementeringen av OAuth 2.0 och OpenID Connect gör omfattande användning av ägar-token, inklusive ägar-token som JWTs. En ägartoken är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs. I detta avseende är ”ägar” en part som kan utgöra en token. Även om en part måste först autentisera med Azure AD för att ta emot ägartoken om steg som krävs inte vidtas för att skydda token i överföring och lagring, kan den fångas upp och används av en oavsiktlig part. Även om vissa säkerhetstoken har en inbyggd mekanism för att förhindra obehöriga personer från att använda dem, ägar-token har inte den här mekanismen och måste transporteras i en säker kanal, till exempel transport layer security (HTTPS). Om en ägartoken skickas i klartext, kan en man-modul i mitten-attack användas av en obehörig part att hämta token och använda den för en obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper gäller när du lagrar eller cachelagring ägar-token för senare användning. Se alltid till att appen överför och lagrar ägar-token på ett säkert sätt. Mer säkerheten på ägartoken, finns [RFC 6750 avsnitt 5](https://tools.ietf.org/html/rfc6750).

Ytterligare information om olika typer av token som används i v2.0-slutpunkten är tillgänglig i [tokenreferens för v2.0-slutpunkten](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoll

Om du är redo att se vissa exempel begäranden, Kom igång med någon av de nedan självstudier. Var och en motsvarar ett visst autentiseringsscenario. Om du behöver hjälp med att fastställa som är rätt för dig kan du kolla [typer av appar som du kan skapa med v2.0](v2-app-types.md).

* [Skapa mobila och interna program med OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Skapa webbappar med öppna ID Connect](v2-protocols-oidc.md)
* [Skapa enkelsidiga appar med den OAuth 2.0 Implicit flöde](v2-oauth2-implicit-grant-flow.md)
* [Skapa Daemon eller server sida processer med flöda till autentiseringsuppgifter för OAuth 2.0-klienten](v2-oauth2-client-creds-grant-flow.md)
* [Hämta token i en webb-API med OAuth 2.0 on-behalf-of-flöde](v2-oauth2-on-behalf-of-flow.md)
