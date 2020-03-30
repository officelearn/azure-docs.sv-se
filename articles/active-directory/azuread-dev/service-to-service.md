---
title: Tjänst-till-tjänst-appar i Azure Active Directory
description: Beskriver vilka service-till-tjänst-program och grunderna för protokollflöde, registrering och tokenförfallodatum för den här apptypen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154771"
---
# <a name="service-to-service-apps"></a>Tjänst-till-tjänst-appar

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Service-to-service-program kan vara en demon eller serverprogram som behöver hämta resurser från ett webb-API. Det finns två underscenarier som gäller för det här avsnittet:

- En demon som måste anropa ett webb-API, byggd på OAuth 2.0-klientautentiseringsbehörighetsbeslagstyp

    I det här scenariot är det viktigt att förstå några saker. För det första är användarinteraktion inte möjligt med ett daemon-program, vilket kräver att programmet har sin egen identitet. Ett exempel på ett daemon-program är ett batch-jobb eller en operativsystemtjänst som körs i bakgrunden. Den här typen av program begär en åtkomsttoken med hjälp av dess programidentitet och presentera dess program-ID, autentiseringsuppgifter (lösenord eller certifikat) och program-ID URI till Azure AD. Efter lyckad autentisering får demonen en åtkomsttoken från Azure AD, som sedan används för att anropa webb-API:et.

- Ett serverprogram (till exempel ett webb-API) som måste anropa ett webb-API, byggt på OAuth 2.0 För utkastspecifikation

    I det här fallet, föreställa sig att en användare har autentiserats på ett inbyggt program, och det här inbyggda programmet måste anropa ett webb-API. Azure AD utfärdar en JWT-åtkomsttoken för att anropa webb-API:et. Om webb-API:et behöver anropa ett annat nedströms webb-API kan det använda flödet för att delegera användarens identitet och autentisera till webb-API:et på andra nivån.

## <a name="diagram"></a>Diagram

![Daemon- eller serverprogram till webb-API-diagram](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokollflöde

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Programidentitet med OAuth 2.0-klientautentiseringsbehörighetsbeslag

1. Först måste serverprogrammet autentisera med Azure AD som sig själv, utan någon mänsklig interaktion, till exempel en interaktiv inloggningsdialogruta. Den gör en begäran till Azure AD:s tokenslutpunkt, som tillhandahåller autentiseringsuppgifter, program-ID och program-ID URI.
1. Azure AD autentiserar programmet och returnerar en JWT-åtkomsttoken som används för att anropa webb-API:et.
1. Över HTTPS använder webbprogrammet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med en "Bärare"-beteckning i auktoriseringshuvudet för begäran till webb-API:et. Webb-API:et validerar sedan JWT-token och om valideringen lyckas returnerar den önskade resursen.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegerad användaridentitet med OAuth 2.0 för utkastspecifikation för räkning

Flödet som beskrivs nedan förutsätter att en användare har autentiserats för ett annat program (till exempel ett inbyggt program) och att deras användaridentitet har använts för att hämta en åtkomsttoken till webb-API:et på den första nivån.

1. Det inbyggda programmet skickar åtkomsttoken till webb-API:et på första nivån.
1. Webb-API:et på första nivån skickar en begäran till Azure AD:s tokenslutpunkt och tillhandahåller dess program-ID och autentiseringsuppgifter samt användarens åtkomsttoken. Dessutom skickas begäran med en on_behalf_of parameter som anger att webb-API:et begär nya token för att anropa ett nedströms webb-API för den ursprungliga användarens räkning.
1. Azure AD verifierar att webb-API:et på den första nivån har behörighet att komma åt webb-API:et på andra nivån och validerar begäran, returnerar en JWT-åtkomsttoken och en JWT-uppdateringstoken till webb-API:et på den första nivån.
1. Över HTTPS anropar webb-API:et på den första nivån sedan webb-API:et på andra nivån genom att lägga till tokensträngen i auktoriseringshuvudet i begäran. Webb-API:et på den första nivån kan fortsätta att anropa webb-API:et på andra nivån så länge åtkomsttoken och uppdateringstoken är giltiga.

## <a name="code-samples"></a>Kodexempel

Se kodexemplen för Daemon- eller Serverprogram till webb-API-scenarier: [Server- eller Daemon-program till webb-API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Appregistrering

* En klient - För både programidentitet och delegerade användaridentitetsfall måste demon- eller serverprogrammet registreras i samma katalog i Azure AD. Webb-API:et kan konfigureras för att visa en uppsättning behörigheter som används för att begränsa demonens eller serverns åtkomst till dess resurser. Om en delegerad användaridentitetstyp används måste serverprogrammet välja önskade behörigheter. Välj **Delegerade behörigheter**på sidan **API-behörighet** för programregistreringen när du har valt **Lägg till en behörighet** och valt API-familjen och välj sedan dina behörigheter. Det här steget krävs inte om programidentitetstypen används.
* Flera innehavare - Först är demonen eller serverprogrammet konfigurerat för att ange de behörigheter som krävs för att fungera. Den här listan över nödvändiga behörigheter visas i en dialogruta när en användare eller administratör i målkatalogen ger sitt samtycke till programmet, vilket gör det tillgängligt för deras organisation. Vissa program kräver bara behörigheter på användarnivå, vilket alla användare i organisationen kan samtycka till. Andra program kräver behörigheter på administratörsnivå, vilket en användare i organisationen inte kan samtycka till. Endast en katalogadministratör kan ge sitt samtycke till program som kräver den här behörighetsnivån. När användaren eller administratören samtycker registreras båda webb-API:erna i sin katalog.

## <a name="token-expiration"></a>Token förfallodatum

När det första programmet använder sin auktoriseringskod för att hämta en JWT-åtkomsttoken får det också en JWT-uppdateringstoken. När åtkomsttoken upphör att gälla kan uppdateringstoken användas för att autentisera användaren igen utan att fråga efter autentiseringsuppgifter. Den här uppdateringstoken används sedan för att autentisera användaren, vilket resulterar i en ny åtkomsttoken och uppdaterar token.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra [programtyper och scenarier](app-types.md)
- Lär dig mer om grunderna i Azure [AD-autentisering](v1-authentication-scenarios.md)
