---
title: 'Azure Active Directory B2C: Autentiseringsprotokoll | Microsoft Docs'
description: "Hur man skapar appar direkt med hjälp av protokoll som stöds av Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5e407d0a-73a2-4d74-ac81-3aa6c31ddcee
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.openlocfilehash: 8e7e7bc7633370057f8dc596ad04a3f1d796a7d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# Azure AD B2C: Autentiseringsprotokoll
Azure Active Directory B2C (Azure AD B2C) tillhandahåller som en tjänst för dina appar genom att stödja två standardprotokollen: OpenID Connect och OAuth 2.0. Tjänsten är standardkompatibel, men två implementeringar av dessa protokoll kan ha vissa skillnader. 

Informationen i den här guiden är användbart om du kan skriva koden genom att skicka direkt och HTTP-begäranden, i stället för med hjälp av ett bibliotek med öppen källkod. Vi rekommenderar att du läser den här sidan innan du fördjupa dig i information om varje specifik protokoll. Men om du redan är bekant med Azure AD B2C kan du gå direkt till [protokollet referens guider](#protocols).

<!-- TODO: Need link to libraries above -->

## Grunderna
Alla appar som använder Azure AD B2C måste registreras i din B2C-katalog i den [Azure-portalen](https://portal.azure.com). Registreringsprocessen samlar in och tilldelar några värden till din app:

* Ett **program-ID** som identifierar din app unikt.
* En **omdirigerings-URI** eller **paketet identifierare** som kan användas för att dirigera svar tillbaka till din app.
* Några andra scenariespecifika-värden. Mer information lär du dig [att registrera ditt program](active-directory-b2c-app-registration.md).

När du har registrerat appen kommunicerar med Azure Active Directory (AD Azure) genom att skicka förfrågningar till slutpunkten:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

I nästan alla OAuth och OpenID Connect flöden ingår fyra parter i exchange:

![OAuth 2.0-roller](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* Den **auktorisering server** är Azure AD-slutpunkt. Den hanterar på ett säkert sätt allt som rör användarinformation och åtkomst till. Den hanterar också förtroenderelationer mellan parterna i ett flöde. Den ansvarar för att verifiera användarens identitet, bevilja och återkalla åtkomst till resurser och utfärdar token. Det är även känd som identitetsleverantören.

* Den **resursägare** är vanligtvis användaren. Det är den part som äger datan och har möjlighet att tillåta att tredje part att komma åt data eller resurs.

* Den **OAuth-klient** är din app. Den identifieras av dess program-ID. Det är vanligtvis den part som slutanvändarna interagera med. Begär den också token från servern auktorisering. Resursägare måste ge klienten åtkomstbehörighet till resursen.

* Den **resursservern** är där resursen och data finns. Litar auktorisering servern för att autentisera och auktorisera OAuth-klient på ett säkert sätt. Dessutom används ägar åtkomst-token så att du kan få åtkomst till en resurs.

## Principer
Azure AD B2C principer är tvekan, de viktigaste funktionerna i tjänsten. Azure AD B2C utökar OAuth 2.0 och OpenID Connect standardprotokoll genom att introducera principer. Dessa gör det möjligt för Azure AD B2C att utföra mycket mer än enkel autentisering och auktorisering. 

Principer fullständigt beskriver konsumenten identitetsupplevelser, inklusive registrering, inloggning, och redigera-profilen. Principer kan definieras i en administrativa Gränssnittet. De kan köras med hjälp av en särskild frågeparameter i HTTP-begäranden om autentisering. 

Principer är inte standardfunktioner i OAuth 2.0 och OpenID Connect, så du bör ta reda på dem. Mer information finns i [referenshandbok för Azure AD B2C princip](active-directory-b2c-reference-policies.md).

## Token
Azure AD B2C-implementering av OAuth 2.0 och OpenID Connect gör ägar-token, inklusive ägar-token som visas i form av JSON web token (JWTs) används. Ett ägartoken är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs.

Innehavaren är någon part som kan ge token. Azure AD måste de först autentisera en part innan det kan ta emot ett ägartoken. Men om de nödvändiga stegen inte vidtas för att skydda token i överföringen och lagringen, den kan snappas upp och används av en oavsiktlig part.

Vissa säkerhetstoken har inbyggda mekanismer som förhindrar att obehöriga personer använder dem, men ägar-token har inte den här mekanismen. De måste transporteras i en säker kanal, till exempel en transport layer security (HTTPS). 

Om ett ägartoken överförs utanför en säker kanal, kan en skadlig part använda en man-in-the-middle-attack och hämta token som du kan använda den för att få obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper tillämpas när ägar-token lagras eller cachelagras för senare användning. Se alltid till att appen skickar och lagrar ägar-token på ett säkert sätt.

Ytterligare ägar-token säkerheten finns [RFC 6750 avsnitt 5](http://tools.ietf.org/html/rfc6750).

Mer information om olika typer av token som används i Azure AD B2C finns i [Azure AD-tokenreferens](active-directory-b2c-reference-tokens.md).

## Protokoll
När du är redo att granska vissa exempel förfrågningar kan starta du med något av följande kurser. Var motsvarar en viss autentiseringsscenariot. Om du behöver hjälp med att fastställa vilka flödet är rätt för dig, kolla [typerna av appar som du kan skapa med hjälp av Azure AD B2C](active-directory-b2c-apps.md).

* [Skapa mobila och interna program med hjälp av OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Skapa webbappar med OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Skapa en sida appar med hjälp av det implicita flödet för OAuth 2.0](active-directory-b2c-reference-spa.md)

