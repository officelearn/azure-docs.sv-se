---
title: Lär dig mer om auktoriseringsprotokoll som stöds av Microsoft identity-plattformen | Azure
description: En guide till OAuth 2.0 och OpenID Connect-protokoll som stöds av Microsoft identity-plattformen slutpunkten.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d509e9f3cabcd588d539d42b8cce022e17a1222
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139197"
---
# <a name="microsoft-identity-platform-protocols"></a>Microsoft identity-plattformen protokoll

Microsoft identity-plattformen slutpunkten för identity-as-a-service med branschens standardprotokoll, OpenID Connect och OAuth 2.0. Tjänsten är standardkompatibla, kan det finnas skillnader mellan två implementeringar av dessa protokoll. Information om här är praktiskt om du väljer att skriva din kod genom att skicka och hantering av HTTP-begäranden direkt eller använda en open source-bibliotek från tredje part i stället för att använda någon av våra [bibliotek med öppen källkod](reference-v2-libraries.md).

> [!NOTE]
> Inte alla Azure AD-scenarier och funktioner stöds av Microsoft identity-plattformen slutpunkten. Läs mer om för att avgöra om du ska använda Microsoft identity-plattformen endpoint, [plattformsbegränsningar för Microsoft identity](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Grunderna

I nästan alla flöden som OAuth 2.0 och OpenID Connect finns det fyra parter som ingår i exchange:

![OAuth 2.0-roller](./media/active-directory-v2-flows/protocols-roles.svg)

* Den **Auktoriseringsservern** är Microsoft identity-plattformen slutpunkt och ansvarar för att användarens identitet, bevilja och återkalla åtkomst till resurser och utfärda token. Auktoriseringsservern även känt som identitetsprovider - den på ett säkert sätt hanterar något att göra med information om användaren, deras åtkomst och förtroenderelationer mellan parterna i ett flöde.
* Den **Resursägaren** är vanligtvis användaren. Det är den part som äger data och har rätt att tillåta tredje part att få åtkomst till den data- eller resursen.
* Den **OAuth klienten** använder din app som identifieras av dess program-ID. OAuth-klienten är vanligtvis den part som användaren interagerar med och begär token från auktoriseringsservern. Klienten måste beviljas behörighet att komma åt resursen som ägare till resursen.
* Den **resursservern** är där resursen eller data finns. Den litar på Auktoriseringsservern för att på ett säkert sätt autentisera och auktorisera OAuth-klienten och använder ägar åtkomst-token så att du kan få åtkomst till en resurs.

## <a name="app-registration"></a>Appregistrering

Varje app som vill acceptera både personliga- och arbets-eller skolkonto måste registreras via den **appregistreringar** användarupplevelsen i den [Azure-portalen](https://aka.ms/appregistrations) innan den kan logga dessa användare i med hjälp av OAuth 2.0 eller OpenID Connect. Registreringsprocessen att samla in och tilldelar några värden till din app:

* En **program-ID** som unikt identifierar din app
* En **omdirigerings-URI** eller **Paketidentifierare** som kan användas för att dirigera svar tillbaka till din app
* Några andra scenariespecifika-värden.

Om du vill ha mer information lär du dig hur du [registrerar en app](quickstart-register-app.md).

## <a name="endpoints"></a>Slutpunkter

När registrerats är kommunicerar appen med Microsoft identity-plattformen genom att skicka förfrågningar till slutpunkten:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Där den `{tenant}` kan ha något av fyra olika värden:

| Värde | Beskrivning |
| --- | --- |
| `common` | Gör att användare med både personliga Microsoft-konton och arbets-/ skolkonton från Azure AD för att logga in i programmet. |
| `organizations` | Tillåter endast användare med arbets-/ skolkonton från Azure AD för att logga in i programmet. |
| `consumers` | Kan endast användare med personliga Microsoft-konton (MSA) för att logga in i programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` | Kan endast användare med arbets-/ skolkonton från en viss Azure AD-klient för att logga in i programmet. Du kan använda antingen det egna domännamnet i Azure AD-klient eller klientens GUID-identifierare. |

Om du vill veta hur du interagerar med de här slutpunkterna, väljer du en viss app-typ i den [protokoll](#protocols) avsnittet och följ länkarna för mer information.

> [!TIP]
> Alla appar som är registrerade i Azure AD kan använda Microsoft identity-plattformen slutpunkten, även om de inte loggar in personliga konton.  På så sätt kan du migrera befintliga program till Microsoft identity-plattformen och [MSAL](reference-v2-libraries.md) utan att återskapa ditt program.  

## <a name="tokens"></a>Token

Microsoft identity-plattformen implementeringen av OAuth 2.0 och OpenID Connect gör omfattande användning av ägar-token, inklusive ägar-token som JWTs. En ägartoken är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs. I detta avseende är ”ägar” en part som kan utgöra en token. Även om en part måste först autentisera med Microsoft identity-plattformen för att ta emot ägartoken, om steg som krävs inte vidtas för att skydda token i överföring och lagring, kan den fångas upp och används av en oavsiktlig part. Även om vissa säkerhetstoken har en inbyggd mekanism för att förhindra obehöriga personer från att använda dem, ägar-token har inte den här mekanismen och måste transporteras i en säker kanal, till exempel transport layer security (HTTPS). Om en ägartoken skickas i klartext, kan en obehörig part använda en man-in-the-middle-attack att hämta token och använda den för obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper gäller när du lagrar eller cachelagring ägar-token för senare användning. Se alltid till att appen överför och lagrar ägar-token på ett säkert sätt. Mer säkerheten på ägartoken, finns [RFC 6750 avsnitt 5](https://tools.ietf.org/html/rfc6750).

Mer information om olika typer av token som används i Microsoft identity-plattformen slutpunkten är tillgänglig i [Microsoft identity-plattformen endpoint token reference](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoll

Om du är redo att se vissa exempel begäranden, Kom igång med någon av de nedan självstudier. Var och en motsvarar ett visst autentiseringsscenario. Om du behöver hjälp med att fastställa som är rätt för dig kan du kolla [typer av appar som du kan skapa med Microsofts identitetsplattform](v2-app-types.md).

* [Skapa mobila och interna program med OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Skapa webbappar med OpenID Connect](v2-protocols-oidc.md)
* [Skapa enkelsidiga appar med den OAuth 2.0 Implicit flöde](v2-oauth2-implicit-grant-flow.md)
* [Skapa Daemon- eller serversidan processer med OAuth 2.0 flödet](v2-oauth2-client-creds-grant-flow.md)
* [Hämta token i en webb-API med OAuth 2.0 on-behalf-of-flöde](v2-oauth2-on-behalf-of-flow.md)
