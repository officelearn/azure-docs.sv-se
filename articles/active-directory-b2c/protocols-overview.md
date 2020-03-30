---
title: Autentiseringsprotokoll i Azure Active Directory B2C | Microsoft-dokument
description: Så här skapar du appar direkt med hjälp av protokoll som stöds av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183913"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Autentiseringsprotokoll
Azure Active Directory B2C (Azure AD B2C) tillhandahåller identitet som en tjänst för dina appar genom att stödja två branschstandardprotokoll: OpenID Connect och OAuth 2.0. Tjänsten är standardkompatibel, men två implementeringar av dessa protokoll kan ha subtila skillnader.

Informationen i den här guiden är användbar om du skriver koden genom att skicka och hantera HTTP-begäranden direkt i stället för att använda ett bibliotek med öppen källkod. Vi rekommenderar att du läser den här sidan innan du går in på detaljerna i varje specifikt protokoll. Men om du redan är bekant med Azure AD B2C kan du gå direkt till [protokollreferensguiderna](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Grunderna
Varje app som använder Azure AD B2C måste registreras i din B2C-katalog i [Azure-portalen](https://portal.azure.com). Registreringsprocessen samlar in och tilldelar några värden till din app:

* Ett **program-ID** som identifierar din app unikt.
* En **Omdirigera URI** eller **paketidentifierare** som kan användas för att rikta svar tillbaka till din app.
* Några andra scenariospecifika värden. Mer information finns i [hur du registrerar din ansökan](tutorial-register-applications.md).

När du har registrerat din app kommunicerar den med Azure Active Directory (Azure AD) genom att skicka begäranden till slutpunkten:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

I nästan alla OAuth och OpenID Connect flöden, fyra parter är inblandade i utbyte:

![Diagram som visar de fyra OAuth 2.0-rollerna](./media/protocols-overview/protocols_roles.png)

* **Auktoriseringsservern** är Azure AD-slutpunkten. Den hanterar säkert allt som rör användarinformation och åtkomst. Den hanterar också förtroenderelationer mellan parterna i ett flöde. Den ansvarar för att verifiera användarens identitet, bevilja och återkalla åtkomst till resurser och utfärda token. Det är också känt som identitetsprovidern.

* **Resursägaren** är vanligtvis slutanvändaren. Det är den part som äger data, och det har befogenhet att tillåta tredje part att komma åt dessa data eller resurser.

* **OAuth-klienten** är din app. Det identifieras med dess program-ID. Det är oftast den part som slutanvändare interagerar med. Den begär också token från auktoriseringsservern. Resursägaren måste bevilja klientbehörigheten för att komma åt resursen.

* **Resursservern** är där resursen eller data finns. Den litar på att auktoriseringsservern autentiserar och auktoriserar OAuth-klienten på ett säkert sätt. Den använder också bärare åtkomsttoken för att säkerställa att åtkomst till en resurs kan beviljas.

## <a name="policies-and-user-flows"></a>Principer och användarflöden
Förmodligen är Azure AD B2C-principer de viktigaste funktionerna i tjänsten. Azure AD B2C utökar standardprotokollen OAuth 2.0 och OpenID Connect genom att introducera principer. Dessa tillåter Azure AD B2C att utföra mycket mer än enkel autentisering och auktorisering.

Azure AD B2C-portalen innehåller fördefinierade, konfigurerbara principer som kallas **användarflöden**för att hjälpa dig att konfigurera de vanligaste identitetsuppgifterna. Användarflöden beskriver helt konsumentidentitetsupplevelser, inklusive registrering, inloggning och profilredigering. Användarflöden kan definieras i ett administrativt användargränssnitt. De kan köras med hjälp av en särskild frågeparameter i HTTP-autentiseringsbegäranden.

Principer och användarflöden är inte standardfunktioner i OAuth 2.0 och OpenID Connect, så du bör ta dig tid att förstå dem. Mer information finns i referensguiden för användarflödet i [Azure AD B2C](user-flow-overview.md).

## <a name="tokens"></a>Token
Azure AD B2C-implementeringen av OAuth 2.0 och OpenID Connect använder bäraretoken omfattande, inklusive innehavartoken som representeras som JWTs (JSON web tokens). En innehavartoken är en lätt säkerhetstoken som ger "bäraren" åtkomst till en skyddad resurs.

Innehavaren är alla parter som kan presentera token. Azure AD måste först autentisera en part innan den kan ta emot en innehavartoken. Men om de nödvändiga stegen inte vidtas för att säkra token i överföring och lagring, kan den fångas upp och användas av en oavsiktlig part.

Vissa säkerhetstoken har inbyggda mekanismer som förhindrar obehöriga parter från att använda dem, men innehavartoken har inte den här mekanismen. De måste transporteras i en säker kanal, till exempel en säkerhet för transportlager (HTTPS).

Om en innehavartoken överförs utanför en säker kanal kan en skadlig part använda en man-in-the-middle-attack för att hämta token och använda den för att få obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper gäller när innehavartoken lagras eller cachelagras för senare användning. Se alltid till att din app överför och lagrar innehavartoken på ett säkert sätt.

För ytterligare bärare token säkerhetsöverväganden, se [RFC 6750 avsnitt 5](https://tools.ietf.org/html/rfc6750).

Mer information om de olika typer av token som används i Azure AD B2C finns i [Azure AD-tokenreferensen](tokens-overview.md).

## <a name="protocols"></a>Protokoll
När du är redo att granska några exempelförfrågningar kan du börja med någon av följande självstudier. Var och en motsvarar ett visst autentiseringsscenario. Om du behöver hjälp med att avgöra vilket flöde som är rätt för dig kan du kolla in [vilka typer av appar du kan skapa med hjälp av Azure AD B2C](application-types.md).

* [Skapa mobila och inbyggda program med OAuth 2.0](authorization-code-flow.md)
* [Skapa webbappar med OpenID Connect](openid-connect.md)
* [Skapa ensidiga appar med det implicita flödet OAuth 2.0](implicit-flow-single-page-application.md)

