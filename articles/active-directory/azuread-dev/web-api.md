---
title: Webb-API-appar i Azure Active Directory
description: Beskriver vad webb-API-program är och grunderna för protokollflöde, registrering och tokenförfallodatum för den här apptypen.
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154431"
---
# <a name="web-api"></a>Webb-API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Webb-API-appar är webbprogram som behöver hämta resurser från ett webb-API. I det här fallet finns det två identitetstyper som webbprogrammet kan använda för att autentisera och anropa webb-API:

- **Programidentitet** - Det här scenariot använder OAuth 2.0-klientautentiseringsuppgifter bevilja för att autentisera som programmet och komma åt webb-API. När du använder en programidentitet kan webb-API:et bara identifiera att webbprogrammet anropar det, eftersom webb-API:et inte får någon information om användaren. Om programmet tar emot information om användaren skickas det via programprotokollet och det är inte signerat av Azure AD. Webb-API:et litar på att webbprogrammet autentiserade användaren. Därför kallas det här mönstret för ett betrott delsystem.
- **Delegerad användaridentitet** - Det här scenariot kan utföras på två sätt: OpenID Connect och OAuth 2.0-auktoriseringskod bevilja med en konfidentiell klient. Webbprogrammet hämtar en åtkomsttoken för användaren, vilket bevisar för webb-API:et att användaren har autentiserats till webbprogrammet och att webbprogrammet kunde hämta en delegerad användaridentitet för att anropa webb-API:et. Den här åtkomsttoken skickas i begäran till webb-API:et, som auktoriserar användaren och returnerar önskad resurs.

Både programidentiteten och delegerade användaridentitetstyper beskrivs i flödet nedan. Den viktigaste skillnaden mellan dem är att den delegerade användaridentiteten först måste hämta en auktoriseringskod innan användaren kan logga in och få åtkomst till webb-API:et.

## <a name="diagram"></a>Diagram

![Webbprogram till webb-API-diagram](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokollflöde

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Programidentitet med OAuth 2.0-klientautentiseringsbehörighetsbeslag

1. En användare är inloggad på Azure AD i webbprogrammet (se avsnittet **Webbappar** för mer information).
1. Webbprogrammet måste hämta en åtkomsttoken så att den kan autentisera till webb-API:et och hämta önskad resurs. Den gör en begäran till Azure AD:s tokenslutpunkt, som tillhandahåller autentiseringsuppgifterna, program-ID: n och webb-API:s program-ID-URI.
1. Azure AD autentiserar programmet och returnerar en JWT-åtkomsttoken som används för att anropa webb-API:et.
1. Över HTTPS använder webbprogrammet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med en "Bärare"-beteckning i auktoriseringshuvudet för begäran till webb-API:et. Webb-API:et validerar sedan JWT-token och om valideringen lyckas returnerar den önskade resursen.

### <a name="delegated-user-identity-with-openid-connect"></a>Delegerad användaridentitet med OpenID Connect

1. En användare är inloggad på ett webbprogram med Azure AD (se avsnittet Webbläsare till webbprogram ovan). Om användaren av webbprogrammet ännu inte har samtyckt till att låta webbprogrammet anropa webb-API:et för dess räkning, måste användaren samtycka. Programmet visar de behörigheter som krävs, och om någon av dessa är behörigheter på administratörsnivå kan en normal användare i katalogen inte godkänna det. Den här medgivandeprocessen gäller endast för program med flera innehavare, inte enskilda klientprogram, eftersom programmet redan har nödvändiga behörigheter. När användaren loggade in tog webbprogrammet emot en ID-token med information om användaren samt en auktoriseringskod.
1. Med hjälp av auktoriseringskoden som utfärdats av Azure AD skickar webbprogrammet en begäran till Azure AD:s tokenslutpunkt som innehåller auktoriseringskoden, information om klientprogrammet (Application ID och redirect URI) och önskad resurs (program-ID URI för webb-API: et).
1. Auktoriseringskoden och informationen om webbprogrammet och webb-API:et valideras av Azure AD. Efter lyckad validering returnerar Azure AD två token: en JWT-åtkomsttoken och en JWT-uppdateringstoken.
1. Över HTTPS använder webbprogrammet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med en "Bärare"-beteckning i auktoriseringshuvudet för begäran till webb-API:et. Webb-API:et validerar sedan JWT-token och om valideringen lyckas returnerar den önskade resursen.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegerad användaridentitet med OAuth 2.0-auktoriseringskodsbevilja

1. En användare är redan inloggad på ett webbprogram, vars autentiseringsmekanism är oberoende av Azure AD.
1. Webbprogrammet kräver en auktoriseringskod för att hämta en åtkomsttoken, så den utfärdar en begäran via webbläsaren till Azure AD:s auktoriseringsslutpunkt, vilket ger program-ID och omdirigerar URI för webbprogrammet efter lyckad autentisering. Användaren loggar in på Azure AD.
1. Om användaren av webbprogrammet ännu inte har samtyckt till att låta webbprogrammet anropa webb-API:et för dess räkning, måste användaren samtycka. Programmet visar de behörigheter som krävs, och om någon av dessa är behörigheter på administratörsnivå kan en normal användare i katalogen inte godkänna det. Detta samtycke gäller både enstaka och flera innehavare ansökan. I det enskilda klientärefallet kan en administratör utföra administratörssamtycke för att godkänna på uppdrag av sina användare. Detta kan göras `Grant Permissions` med hjälp av knappen i [Azure-portalen](https://portal.azure.com). 
1. När användaren har medgivande får webbprogrammet den auktoriseringskod som måste hämta en åtkomsttoken.
1. Med hjälp av auktoriseringskoden som utfärdats av Azure AD skickar webbprogrammet en begäran till Azure AD:s tokenslutpunkt som innehåller auktoriseringskoden, information om klientprogrammet (Application ID och redirect URI) och önskad resurs (program-ID URI för webb-API: et).
1. Auktoriseringskoden och informationen om webbprogrammet och webb-API:et valideras av Azure AD. Efter lyckad validering returnerar Azure AD två token: en JWT-åtkomsttoken och en JWT-uppdateringstoken.
1. Över HTTPS använder webbprogrammet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med en "Bärare"-beteckning i auktoriseringshuvudet för begäran till webb-API:et. Webb-API:et validerar sedan JWT-token och om valideringen lyckas returnerar den önskade resursen.

## <a name="code-samples"></a>Kodexempel

Se kodexemplen för webbprogram till webb-API-scenarier. Och kom tillbaka ofta - nya prover läggs till ofta. Webbprogram [till webb-API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Appregistrering

Information om hur du registrerar ett program med slutpunkten Azure AD v1.0 finns [i Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Enskild klient - För både programidentitet och delegerade användaridentitetsfall måste webbprogrammet och webb-API:et registreras i samma katalog i Azure AD. Webb-API:et kan konfigureras för att visa en uppsättning behörigheter som används för att begränsa webbprogrammets åtkomst till dess resurser. Om en delegerad användaridentitetstyp används måste webbprogrammet välja önskade behörigheter från **listrutan Behörigheter till andra program** i Azure-portalen. Det här steget krävs inte om programidentitetstypen används.
* Flera innehavare - Först är webbprogrammet konfigurerat för att ange de behörigheter som krävs för att fungera. Den här listan över nödvändiga behörigheter visas i en dialogruta när en användare eller administratör i målkatalogen ger sitt samtycke till programmet, vilket gör det tillgängligt för deras organisation. Vissa program kräver bara behörigheter på användarnivå, vilket alla användare i organisationen kan samtycka till. Andra program kräver behörigheter på administratörsnivå, vilket en användare i organisationen inte kan samtycka till. Endast en katalogadministratör kan ge sitt samtycke till program som kräver den här behörighetsnivån. När användaren eller administratören samtycker registreras webbprogrammet och webb-API:et i sin katalog.

## <a name="token-expiration"></a>Token förfallodatum

När webbprogrammet använder sin auktoriseringskod för att hämta en JWT-åtkomsttoken får det också en JWT-uppdateringstoken. När åtkomsttoken upphör att gälla kan uppdateringstoken användas för att återuthtentisera användaren utan att de behöver logga in igen. Den här uppdateringstoken används sedan för att autentisera användaren, vilket resulterar i en ny åtkomsttoken och uppdaterar token.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra [programtyper och scenarier](app-types.md)
- Lär dig mer om grunderna i Azure [AD-autentisering](v1-authentication-scenarios.md)
