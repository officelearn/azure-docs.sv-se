---
title: Lär dig mer om auktorisering-protokoll som stöds av Azure AD v2.0 | Microsoft Docs
description: En guide till protokoll som stöds av Azure AD v2.0-slutpunkten.
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
ms.date: 04/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 7c6031bb135c48a8d58f61c3c96bf18e817809ba
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="v20-protocols---oauth-20--openid-connect"></a>v2.0 protokoll - OAuth 2.0 & OpenID Connect
V2.0-slutpunkten kan använda Azure AD identity-as-a-service med standardprotokollen, OpenID Connect och OAuth 2.0. När tjänsten är standardkompatibel kan finnas det skillnader mellan två implementeringar av dessa protokoll. Informationen här kan vara användbart om du väljer att skriva koden genom att skicka direkt & hanterar HTTP-begäranden eller använda ett 3 part öppen källkod bibliotek i stället för att använda någon av våra [öppna bibliotek källkod](active-directory-v2-libraries.md).

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
>
>

## <a name="the-basics"></a>Grunderna
I nästan alla OAuth & OpenID Connect flöden finns det fyra parter ingår i exchange:

![OAuth 2.0-roller](../../media/active-directory-v2-flows/protocols_roles.png)

* Den **auktorisering Server** är v2.0-slutpunkten. Den ansvarar för att säkerställa användarens identitet, bevilja och återkalla åtkomst till resurser och utfärdar token. Den kallas även identitetsleverantören. - den hanterar ingenting att göra med information om användaren, deras åtkomst och förtroenderelationer mellan parterna i ett flöde på ett säkert sätt.
* Den **resursägare** är vanligtvis slutanvändaren. Det är den part som äger data och har rätt att tillåta att tredje part att komma åt data, eller resurs.
* Den **OAuth-klient** är din app som identifieras av ett program-Id. Det är vanligtvis den part som slutanvändaren samverkar med och begär token från servern auktorisering. Klienten måste ha behörighet att komma åt resursen av resursägare.
* Den **resursservern** är där resursen och data finns. Den förtroenden auktorisering servern för att autentisera och auktorisera OAuth-klient på ett säkert sätt och använder ägar access_tokens så att du kan få åtkomst till en resurs.

## <a name="app-registration"></a>App-registrering
Alla appar som använder v2.0-slutpunkten måste vara registrerade med [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) innan det kan interagera med hjälp av OAuth eller OpenID Connect. Registreringsprocessen kommer samla in & tilldelar några värden till din app:

* En **program-Id** som unikt identifierar din app
* En **omdirigerings-URI** eller **Paketidentifierare** som kan användas för att dirigera svar tillbaka till din app
* Några andra scenariespecifika-värden.

Om du vill ha mer information lär du dig hur du [registrerar en app](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Slutpunkter
När registreringen är klar kommunicerar appen med Azure AD genom att skicka förfrågningar till v2.0-slutpunkten:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Där den `{tenant}` kan vidta någon av fyra olika värden:

| Värde | Beskrivning |
| --- | --- |
| `common` |Gör att användare med både personliga Microsoft-konton och arbete/skolkonton från Azure Active Directory för att logga in på programmet. |
| `organizations` |Kan endast användare med arbete/skolkonton från Azure Active Directory för att logga in på programmet. |
| `consumers` |Kan endast användare med personliga Microsoft-konton (MSA) för att logga in på programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` |Kan endast användare med arbete/skolkonton från en viss Azure Active Directory-klient för att logga in på programmet. Kan användas antingen det egna namnet för Azure AD-klient eller klientens guid-identifierare. |

Välj en viss app typ nedan för mer information om hur du interagerar med dessa slutpunkter.

## <a name="tokens"></a>Token
V2.0-implementering av OAuth 2.0 och OpenID Connect utnyttja omfattande ägar-token, inklusive ägar-token som JWTs. Ett ägartoken är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs. På detta sätt är ”innehavaren” någon part som kan ge token. Även om en part måste de först autentisera med Azure AD ta emot ägartoken om de nödvändiga stegen inte vidtas för att skydda token i överföringen och lagringen, kan de snappas upp och används av en oavsiktlig part. Även om vissa säkerhetstoken har en inbyggd mekanism för att förhindra att obehöriga personer använder dem, ägar-token har inte den här mekanismen och måste transporteras i en säker kanal, till exempel transport layer security (HTTPS). Om ett ägartoken skickas i klartext, kan en man-i mitten-attack användas av skadliga part att hämta token och använda den för en obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper tillämpas när lagring eller cachelagring ägar-token för senare användning. Se alltid till att appen skickar och lagrar ägar-token på ett säkert sätt. Flera säkerheten på ägar-token finns [RFC 6750 avsnitt 5](http://tools.ietf.org/html/rfc6750).

Mer information om olika typer av token som används i v2.0-slutpunkten är tillgänglig i [v2.0-slutpunkten tokenreferens](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protokoll
Om du är redo att visa vissa exempel begäranden komma igång med en av de nedan självstudier. Var och en motsvarar en viss autentiseringsscenariot. Om du behöver hjälp med att fastställa vilket är rätt flödet för du kolla [typerna av appar som du kan skapa med v2.0](active-directory-v2-flows.md).

* [Skapa mobila och interna program med OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
* [Skapa Web Apps med öppna ID Connect](active-directory-v2-protocols-oidc.md)
* [Skapa Ensidesappar med det implicita flödet för OAuth 2.0](active-directory-v2-protocols-implicit.md)
* [Build-Daemon eller Server Side processer med OAuth 2.0 klientens autentiseringsuppgifter flöda](active-directory-v2-protocols-oauth-client-creds.md)
* [Hämta token i Web API med OAuth 2.0 för flöda](active-directory-v2-protocols-oauth-on-behalf-of.md)
