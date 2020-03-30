---
title: Inbyggda appar i Azure Active Directory
description: Beskriver vad inbyggda appar är och grunderna för protokollflöde, registrering och tokenförfallodatum för den här apptypen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154805"
---
# <a name="native-apps"></a>Inbyggda appar

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Inbyggda appar är program som anropar ett webb-API för en användares räkning. Det här scenariot bygger på beviljad typ av OAuth 2.0-auktoriseringskod med en offentlig klient, enligt beskrivningen i avsnitt 4.1 i [OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749). Det inbyggda programmet hämtar en åtkomsttoken för användaren med hjälp av OAuth 2.0-protokollet. Den här åtkomsttoken skickas sedan i begäran till webb-API:et, som auktoriserar användaren och returnerar önskad resurs.

## <a name="diagram"></a>Diagram

![Inbyggt program till webb-API-diagram](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokollflöde

Om du använder AD-autentiseringsbiblioteken hanteras de flesta av de protokollinformation som beskrivs nedan för dig, till exempel webbläsarpop-up, tokencacheing och hantering av uppdateringstoken.

1. Med hjälp av ett webbläsarpop-up gör det inbyggda programmet en begäran till auktoriseringsslutpunkten i Azure AD. Den här begäran innehåller program-ID och omdirigera URI för det inbyggda programmet som visas i Azure-portalen och program-ID URI för webb-API. Om användaren inte redan har loggat in uppmanas han att logga in igen
1. Azure AD autentiserar användaren. Om det är ett program med flera innehavare och samtycke krävs för att använda programmet, måste användaren godkänna om de inte redan har gjort det. Efter att ha beviljat medgivande och vid lyckad autentisering utfärdar Azure AD ett auktoriseringskodsvar tillbaka till klientprogrammets omdirigera URI.
1. När Azure AD utfärdar ett auktoriseringskodsvar tillbaka till omdirigerings-URI:n stoppar klientprogrammet webbläsarinteraktionen och extraherar auktoriseringskoden från svaret. Med den här auktoriseringskoden skickar klientprogrammet en begäran till Azure AD:s tokenslutpunkt som innehåller auktoriseringskoden, information om klientprogrammet (Application ID och redirect URI) och önskad resurs (program-ID URI för klientprogrammet (Application ID och den önskade resursen (program-ID URI för klientprogrammet (Application ID och redirect URI) och önskad resurs (program-ID URI för klientprogrammet webb-API).
1. Auktoriseringskoden och informationen om klientprogrammet och webb-API:et valideras av Azure AD. Efter lyckad validering returnerar Azure AD två token: en JWT-åtkomsttoken och en JWT-uppdateringstoken. Dessutom returnerar Azure AD grundläggande information om användaren, till exempel deras visningsnamn och klient-ID.
1. Över HTTPS använder klientprogrammet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med en "Bärare"-beteckning i auktoriseringshuvudet för begäran till webb-API:et. Webb-API:et validerar sedan JWT-token och om valideringen lyckas returnerar den önskade resursen.
1. När åtkomsttoken upphör att gälla får klientprogrammet ett felmeddelande som indikerar att användaren måste autentisera igen. Om programmet har en giltig uppdateringstoken kan det användas för att hämta en ny åtkomsttoken utan att användaren uppmanas att logga in igen. Om uppdateringstoken upphör att gälla måste programmet autentisera användaren interaktivt igen.

> [!NOTE]
> Uppdateringstoken som utfärdas av Azure AD kan användas för att komma åt flera resurser. Om du till exempel har ett klientprogram som har behörighet att anropa två webb-API:er kan uppdateringstoken användas för att även hämta en åtkomsttoken till det andra webb-API:et.

## <a name="code-samples"></a>Kodexempel

Se kodexemplen för inbyggda program till webb-API-scenarier. Och kom tillbaka ofta - vi lägger till nya prover ofta. [Native Program till Webb-API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Appregistrering

Information om hur du registrerar ett program med slutpunkten Azure AD v1.0 finns [i Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* En enda klient - Både det inbyggda programmet och webb-API:et måste registreras i samma katalog i Azure AD. Webb-API:et kan konfigureras för att visa en uppsättning behörigheter som används för att begränsa det inbyggda programmets åtkomst till dess resurser. Klientprogrammet väljer sedan önskade behörigheter från listrutan Behörigheter till andra program i Azure-portalen.
* Flera innehavare - För det första, det inbyggda programmet bara någonsin registrerats i utvecklaren eller utgivarens katalog. För det andra är det inbyggda programmet konfigurerat för att ange de behörigheter som krävs för att fungera. Den här listan över nödvändiga behörigheter visas i en dialogruta när en användare eller administratör i målkatalogen ger sitt samtycke till programmet, vilket gör det tillgängligt för deras organisation. Vissa program kräver bara behörigheter på användarnivå, vilket alla användare i organisationen kan samtycka till. Andra program kräver behörigheter på administratörsnivå, vilket en användare i organisationen inte kan samtycka till. Endast en katalogadministratör kan ge sitt samtycke till program som kräver den här behörighetsnivån. När användaren eller administratören samtycker registreras endast webb-API:et i deras katalog. 

## <a name="token-expiration"></a>Token förfallodatum

När det inbyggda programmet använder sin auktoriseringskod för att hämta en JWT-åtkomsttoken får det också en JWT-uppdateringstoken. När åtkomsttoken upphör att gälla kan uppdateringstoken användas för att autentisera användaren igen utan att de behöver logga in igen. Den här uppdateringstoken används sedan för att autentisera användaren, vilket resulterar i en ny åtkomsttoken och uppdaterar token.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra [programtyper och scenarier](app-types.md)
- Lär dig mer om grunderna i Azure [AD-autentisering](v1-authentication-scenarios.md)
