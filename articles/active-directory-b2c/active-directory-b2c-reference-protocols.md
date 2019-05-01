---
title: Protokoll för autentisering i Azure Active Directory B2C | Microsoft Docs
description: Hur du skapar appar direkt med hjälp av de protokoll som stöds av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d62bf0d77a7e77b1fb6ece014dcd850d52d26ce0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705550"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Autentiseringsprotokoll
Azure Active Directory B2C (Azure AD B2C) erbjuder identitet som en tjänst för dina appar genom att stödja två protokoll av branschstandardtyp: OpenID Connect och OAuth 2.0. Tjänsten är standardkompatibla, men alla två implementeringen av dessa protokoll kan ha vissa skillnader. 

Informationen i den här guiden är användbart om du skriver koden genom att skicka direkt och hantering av HTTP-begäranden, i stället för ett bibliotek med öppen källkod. Vi rekommenderar att du läser den här sidan innan du fördjupar dig i detaljerna för varje specifik protokoll. Men om du redan är bekant med Azure AD B2C kan du gå direkt till [referenshandböcker protokollet](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Grunderna
Alla appar som använder Azure AD B2C måste registreras i din B2C-katalog i den [Azure-portalen](https://portal.azure.com). Registreringsprocessen samlar in och tilldelar några värden till din app:

* Ett **program-ID** som identifierar din app unikt.
* En **omdirigerings-URI** eller **paketera identifierare** som kan användas för att dirigera svar tillbaka till din app.
* Några andra scenariespecifika-värden. Mer information lär du dig [registrera ditt program](active-directory-b2c-app-registration.md).

När du har registrerat din app, kommunicerar den med Azure Active Directory (Azure AD) för genom att skicka förfrågningar till slutpunkten:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Nästan alla flöden som OAuth och OpenID Connect är fyra parter involverade i exchange:

![OAuth 2.0-roller](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* Den **auktoriseringsservern** är Azure AD-slutpunkten. Den hanterar på ett säkert sätt att något som är kopplat till användarinformation och åtkomst. Den hanterar också förtroenderelationer mellan parterna i ett flöde. Det är ansvarig för att verifiera användarens identitet, bevilja och återkalla åtkomst till resurser och utfärda token. Det kallas även för identitetsprovidern.

* Den **resursägaren** är vanligtvis användaren. Det är den part som äger data och den har rätt att tillåta tredje part att få åtkomst till den data- eller resursen.

* Den **OAuth klienten** använder din app. Det har identifierats av dess program-ID. Det är vanligtvis den part som slutanvändarna interagera med. Det kan också begär token från auktoriseringsservern. Resursägaren måste ge klienten behörighet till resursen.

* Den **resursservern** är där resursen eller data finns. Litar auktoriseringsservern för att autentisera och auktorisera OAuth-klienten på ett säkert sätt. Ägar-åtkomsttoken används också att se till att du kan få åtkomst till en resurs.

## <a name="policies-and-user-flows"></a>Principer och användaren flöden
Azure AD B2C-principer är utan tvekan, de viktigaste funktionerna i tjänsten. Azure AD B2C utökar OAuth 2.0 och OpenID Connect standardprotokoll genom att introducera principer. De här alternativen kan Azure AD B2C att utföra mycket mer än enkel autentisering och auktorisering. 

Som hjälper dig att konfigurera de vanligaste uppgifterna för identitet, Azure AD B2C-portalen innehåller fördefinierade, konfigureras principer som kallas **användarflöden**. Användarflöden helt beskriver kundupplevelser för identitet, inklusive registrering, inloggning, och profilera redigering. Användarflöden kan definieras i en administrativa Gränssnittet. De kan köras med hjälp av en särskild frågeparameter i HTTP-begäranden om autentisering. 

Principer och användarflöden är inte standardfunktioner för OAuth 2.0 och OpenID Connect, så du bör ta dig tid att förstå hur de. Mer information finns i den [referensguiden för Azure AD B2C användaren flow](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Token
Azure AD B2C-implementeringen av OAuth 2.0 och OpenID Connect gör omfattande användning av ägar-token, inklusive ägar-token som representeras som JSON web token (JWTs). En ägartoken är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs.

Innehavaren är en part som kan utgöra en token. Azure AD måste först autentisera en part innan det kan ta emot en ägartoken. Men om steg som krävs inte vidtas för att skydda token i överföring och lagring, den kan fångas upp och används av en oavsiktlig part.

Vissa säkerhetstoken har inbyggda mekanismer som förhindrar att obehöriga personer som använder dem, men ägar-token har inte den här mekanismen. De måste transporteras i en säker kanal, till exempel en transport layer security (HTTPS). 

Om en ägartoken överförs utanför en säker kanal, kan en obehörig part använda en man-in-the-middle-attack för att hämta token och använda den för att få obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper gäller när ägar-token lagras eller cachen för senare användning. Se alltid till att appen överför och lagrar ägar-token på ett säkert sätt.

Ytterligare ägar-token-säkerhetsaspekter finns [RFC 6750 avsnitt 5](https://tools.ietf.org/html/rfc6750).

Mer information om olika typer av token som används i Azure AD B2C finns i [tokenreferens för Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokoll
När du är redo att gå igenom några exempel begäranden, kan du börja med någon av följande självstudier. Var motsvarar ett visst autentiseringsscenario. Om du behöver hjälp med att fastställa vilka flödet som passar dig kan du kolla [typer av appar som du kan skapa med hjälp av Azure AD B2C](active-directory-b2c-apps.md).

* [Skapa mobila och interna program med hjälp av OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Skapa webbappar med OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Skapa enkelsidiga appar med hjälp av OAuth 2.0 implicit flöde](active-directory-b2c-reference-spa.md)

