---
title: OAuth 2.0- och OpenID Connect-protokoll – Microsofts identitetsplattform | Azure
description: En guide till OAuth 2.0- och OpenID Connect-protokoll som stöds av slutpunkten för Microsoft-identitetsplattformen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: d7ed4dbbc3fddb2e21ed3cf5292ebd80fe1e3e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263119"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>OAuth 2.0- och OpenID Connect-protokoll på Microsofts identitetsplattform

Slutpunkten för Microsoft-identitetsplattform för identitet som en tjänst med branschstandardprotokoll, OpenID Connect och OAuth 2.0. Även om tjänsten är standardkompatibel kan det finnas subtila skillnader mellan två implementeringar av dessa protokoll. Informationen här är användbar om du väljer att skriva din kod genom att direkt skicka och hantera HTTP-förfrågningar eller använda ett bibliotek med öppen källkod från tredje part, i stället för att använda ett av våra [bibliotek med öppen källkod](reference-v2-libraries.md).

> [!NOTE]
> Alla Azure AD-scenarier och -funktioner stöds inte av slutpunkten för Microsoft identity platform. För att ta reda på om du ska använda slutpunkten för Microsoft identity platform läser du om begränsningar av [Microsofts identitetsplattform](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Grunderna

I nästan alla OAuth 2.0- och OpenID Connect-flöden är det fyra parter som är involverade i utbytet:

![Diagram som visar OAuth 2.0-rollerna](./media/active-directory-v2-flows/protocols-roles.svg)

* **Auktoriseringsservern** är slutpunkten för Microsoft-identitetsplattformen och ansvarar för att säkerställa användarens identitet, bevilja och återkalla åtkomst till resurser och utfärda token. Auktoriseringsservern även känd som identitetsprovidern - den hanterar säkert allt som har att göra med användarens information, deras åtkomst och förtroenderelationer mellan parter i ett flöde.
* **Resursägaren** är vanligtvis slutanvändaren. Det är den part som äger data och har befogenhet att tillåta tredje part att komma åt dessa data eller resurser.
* **OAuth-klienten** är din app som identifieras av dess program-ID. OAuth-klienten är vanligtvis den part som slutanvändaren interagerar med och begär token från auktoriseringsservern. Klienten måste ha behörighet att komma åt resursen av resursägaren.
* **Resursservern** är där resursen eller data finns. Den litar på auktoriseringsservern för att autentisera och auktorisera OAuth-klienten på ett säkert sätt och använder bärareåtkomsttoken för att säkerställa att åtkomst till en resurs kan beviljas.

## <a name="app-registration"></a>Appregistrering

Varje app som vill acceptera både personliga konton och arbets- eller skolkonton måste registreras via **appregistreringsupplevelsen** i [Azure-portalen](https://aka.ms/appregistrations) innan den kan logga in dessa användare med OAuth 2.0 eller OpenID Connect. Appregistreringsprocessen samlar in och tilldelar några värden till din app:

* Ett **program-ID** som unikt identifierar din app
* En **Redirect URI** (valfritt) som kan användas för att rikta svar tillbaka till din app
* Några andra scenariospecifika värden.

Om du vill ha mer information lär du dig hur du [registrerar en app](quickstart-register-app.md).

## <a name="endpoints"></a>Slutpunkter

När appen har registrerats kommunicerar den med Microsofts identitetsplattform genom att skicka förfrågningar till slutpunkten:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Där `{tenant}` kan ta en av fyra olika värden:

| Värde | Beskrivning |
| --- | --- |
| `common` | Gör att användare med både personliga Microsoft-konton och arbets-/skolkonton från Azure AD kan logga in på programmet. |
| `organizations` | Tillåter endast användare med arbets-/skolkonton från Azure AD att logga in på programmet. |
| `consumers` | Tillåter endast användare med personliga Microsoft-konton (MSA) att logga in på programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` | Tillåter endast användare med arbets-/skolkonton från en viss Azure AD-klient att logga in på programmet. Antingen kan det egna domännamnet för Azure AD-klienten eller klientens GUID-identifierare användas. |

Om du vill veta hur du interagerar med dessa slutpunkter väljer du en viss apptyp i avsnittet [Protokoll](#protocols) och följer länkarna för mer information.

> [!TIP]
> Alla appar som är registrerade i Azure AD kan använda slutpunkten för Microsoft-identitetsplattformen, även om de inte loggar in på personliga konton.  På så sätt kan du migrera befintliga program till Microsoft identity platform och [MSAL](reference-v2-libraries.md) utan att återskapa ditt program.  

## <a name="tokens"></a>Token

Microsoft identity-plattformsimplementeringen av OAuth 2.0 och OpenID Connect använder bärare tokens i stor utsträckning, inklusive innehavartoken som representeras som JWTs. En innehavartoken är en lätt säkerhetstoken som ger "bäraren" åtkomst till en skyddad resurs. I denna mening är "bärare" någon part som kan presentera token. Även om en part först måste autentisera med Microsofts identitetsplattform för att ta emot innehavartoken, kan den fångas upp och användas av en oavsiktlig part om de nödvändiga stegen inte vidtas för att skydda token i överföring och lagring. Vissa säkerhetstoken har en inbyggd mekanism för att förhindra att obehöriga parter använder dem, men innehavartoken har inte den här mekanismen och måste transporteras i en säker kanal som säkerhet för transportlager (HTTPS). Om en innehavartoken överförs i clear kan en skadlig part använda en man-in-the-middle-attack för att hämta token och använda den för obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper gäller när du lagrar eller cachelagrar innehavartoken för senare användning. Se alltid till att din app överför och lagrar innehavartoken på ett säkert sätt. Mer säkerhetsöverväganden på innehavartoken finns i [RFC 6750 avsnitt 5](https://tools.ietf.org/html/rfc6750).

Mer information om olika typer av token som används i slutpunkten för Microsoft-identitetsplattform finns i [microsoft identity platform slutpunktstokenreferens](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoll

Om du är redo att se några exempelförfrågningar kan du komma igång med en av nedanstående självstudier. Var och en motsvarar ett visst autentiseringsscenario. Om du behöver hjälp med att avgöra vilket som är rätt flöde för dig kan du kolla in [vilka typer av appar du kan skapa med Microsofts identitetsplattform](v2-app-types.md).

* [Skapa mobil och inbyggd applikation med OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Skapa webbappar med OpenID Connect](v2-protocols-oidc.md)
* [Skapa ensidiga appar med OAuth 2.0 Implicit Flow](v2-oauth2-implicit-grant-flow.md)
* [Bygga demoner eller serverprocesser med klientautentiseringsflödet OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Hämta token i ett webb-API med OAuth 2.0 för flow-uppdrag](v2-oauth2-on-behalf-of-flow.md)
