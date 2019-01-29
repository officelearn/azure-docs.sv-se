---
title: Tjänst-till-tjänst-appar i Azure Active Directory
description: Beskriver vilka tjänst-till-tjänst-program och grunderna på protocol flow, registrering och token upphör att gälla för den här apptypen.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 1a10bf35618d7f543957d1f839618397f08698d4
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095437"
---
# <a name="service-to-service-apps"></a>Tjänst-till-tjänst-appar

Tjänst-till-tjänst-program kan vara en daemon eller server-program som krävs för att hämta resurser från ett webb-API. Det finns två underordnade scenarier som gäller för det här avsnittet:

- En daemon som måste anropa ett webb-API som bygger på beviljandetypen för OAuth 2.0-klienten autentiseringsuppgifter

    I det här scenariot är det viktigt att förstå några saker. Först går inte användarinteraktion med en daemon-programmet, vilket kräver att programmet har sin egen identitet. Ett exempel på en daemonprogram är ett batch-jobb eller en tjänst i operativsystemet som körs i bakgrunden. Den här typen av program begär en åtkomsttoken genom att använda dess Programidentitet och presentera dess program-ID, autentiseringsuppgifter (lösenord eller certifikat) och program till Azure AD-ID-URI. Efter en lyckad autentisering får daemon en åtkomsttoken från Azure AD, som sedan används för att anropa webb-API.

- Ett serverprogram (till exempel ett webb-API) som måste anropa ett webb-API som bygger på OAuth 2.0 Behalf utkast till en specifikation

    Anta att en användare har autentiserats på en programspecifik och interna programmet måste anropa ett webb-API i det här scenariot. Azure AD utfärdar en JWT-token för åtkomst för att anropa webb-API. Om webb-API måste anropa ett annat underordnat webb-API, använda den on-behalf-of-flöde för att delegera användarens identitet och autentisera till andra nivån webb-API.

## <a name="diagram"></a>Diagram

![Daemon eller ett program till webb-API-diagram](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>DProtocol flöde

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Ge programmet identitet med hjälp av OAuth 2.0-klientautentiseringsuppgifter

1. Serverprogrammet måste först autentisera med Azure AD som själva, utan en mänsklig interaktion, till exempel en dialogruta för interaktiv inloggning. Den gör en begäran till tokenslutpunkten för Azure AD, att ange autentiseringsuppgifter, program-ID och program-ID-URI.
1. Azure AD autentiserar programmet och returnerar en JWT åtkomst-token som används för att anropa webb-API.
1. Över HTTPS använder webbprogrammet returnerade JWT-åtkomsttoken för att lägga till JWT-sträng med en ”ägar” beteckning i auktoriseringshuvudet för begäran till webb-API. Webb-API: verifierar JWT-token och om verifieringen lyckas, returnerar önskad resurs.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegerad användaridentiteter med OAuth 2.0 Behalf Draft-specifikationen

Flödet beskrivs nedan förutsätter att en användare har autentiserats på ett annat program (t.ex ett internt program) och deras användar-ID har använts för att hämta en åtkomsttoken till den första nivån webben-API.

1. Internt program skickar åtkomsttoken till den första nivån webben-API.
1. Den första nivån webben-API skickar en begäran till tokenslutpunkten för Azure AD, ger dess program-ID och autentiseringsuppgifter, samt användarens åtkomsttoken. Dessutom kan begäran skickas med en on_behalf_of parameter som anger webb API begär nya-tokens för att anropa en underordnad webb-API åt den ursprungliga användaren.
1. Azure AD verifierar att den första nivån webben-API har behörighet att komma åt andra nivån webb-API och verifierar begäran kan returnera en JWT-token för åtkomst och en JWT uppdateringstoken till den första nivån webben-API.
1. Över HTTPS anropar första nivån webb-API sedan den andra nivån webben-API genom att lägga till tokensträng i auktoriseringshuvudet i begäran. Den första nivån webben-API kan fortsätta att anropa den andra nivån webben-API så länge åtkomsttoken och uppdateringstoken är giltiga.

## <a name="code-samples"></a>Kodexempel

Se kodexempel för Daemon eller ett program till webb-API-scenarier. Och kontrollera tillbaka ofta eftersom nya exempel läggs ofta. [Servern eller Daemon-programmet till webb-API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Appregistrering

* Enskild klient - för applikationsidentitet såväl delegerade användaren identitet fall daemon eller serverprogram som måste vara registrerade i samma katalog i Azure AD. Webb-API kan konfigureras för att exponera en uppsättning behörigheter som används för att begränsa daemon eller servers åtkomst till dess resurser. Om en delegerad användaridentitetstypen används måste serverprogrammet att välja önskade behörigheter i listrutan ”behörigheter till andra program” i Azure-portalen. Det här steget krävs inte om identitet programtyp som används.
* Flera innehavare-första, den daemon eller server har konfigurerats för att ange de behörigheter som krävs ska fungera. Den här listan över behörigheter som krävs visas i en dialogruta när en användare eller administratör i målmappen ger du ditt medgivande till programmet, vilket gör dem tillgängliga för deras organisation. Vissa program kräver endast användarnivå behörigheter, som alla användare i organisationen kan godkänna. Andra program som kräver på administratörsnivå som en användare i organisationen inte kan godkänna. Endast en directory-administratören kan samtycka till program som kräver den här behörighetsnivån. När användaren eller administratören godkänner, är både webb-API: er registrerade i sin katalog.

## <a name="token-expiration"></a>Giltighetstid för token

När det första programmet använder dess auktoriseringskod för att hämta en JWT åtkomsttoken, emot tas även en uppdatering JWT-token. När åtkomsttoken upphör att gälla användas uppdateringstoken för att autentisera användaren igen utan att fråga efter autentiseringsuppgifter. Den här uppdateringstoken används sedan för att autentisera användaren, vilket innebär att en ny åtkomsttoken och uppdateringstoken.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra [programtyper och scenarier](app-types.md)
- Lär dig mer om Azure AD [grunder](authentication-scenarios.md)
