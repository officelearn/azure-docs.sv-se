---
title: Enkelsidigt program i Azure Active Directory
description: Beskriver vilka enkelsidigt program (SPA) är och grunderna på protocol flow, registrering och token upphör att gälla för den här apptypen.
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
ms.openlocfilehash: f9f5f7dc4752e3cb568acd3ad907d115e4d24e85
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55102336"
---
# <a name="single-page-applications"></a>Enkelsidigt program

Enkelsidigt program (SPA) är vanligtvis strukturerade som en JavaScript-presentationslagret (klientdel) som körs i webbläsaren och en webb-API-serverdel som körs på en server och implementerar affärslogiken i programmet. Mer information om implicit auktoriseringsbeviljande och hjälper dig att avgöra om det är bäst för ditt program-scenario genom att se [förstå OAuth2 implicit ge flow i Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

I det här scenariot, när användaren loggar in, JavaScript klientdelens slutliga användning [Active Directory Authentication Library för JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) och implicit auktoriseringsbeviljande att hämta ID-token (id_token) från Azure AD. Token cachelagras och klienten kopplas till den på begäran som ägartoken när anrop till dess webb-API-serverdel som skyddas med OWIN-mellanprogrammet.

## <a name="diagram"></a>Diagram

![Diagram över enkelsidigt program](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Protocol flow

1. Användaren navigerar till webbprogrammet.
1. Programmet returnerar JavaScript klientdelen (presentationslagret) till webbläsaren.
1. Användaren initierar inloggning, till exempel klickar du på länken logga in. Webbläsaren skickar en GET till auktoriseringsslutpunkten för Azure AD för att begära en ID-token. Den här begäran innehåller programmets ID och svars-URL i Frågeparametrar.
1. Azure AD verifierar svars-URL mot registrerade svars-URL som har konfigurerats i Azure-portalen.
1. Användaren loggar in på sidan logga in.
1. Om autentiseringen lyckas, Azure AD skapar en ID-token och returnerar det som ett URL-fragment (#) till programmets svars-URL. För ett produktionsprogram måste svars-URL vara HTTPS. Den returnerade token innehåller anspråk om användaren och Azure AD som krävs av programmet för att validera token.
1. JavaScript-klient-kod som körs i webbläsaren extraherar token från svaret som ska användas i skydda anrop till programmets web API tillbaka avslutas.
1. Webbläsaren anropar programmets webb-API: et tillbaka sluta med ID-token i auktoriseringshuvudet. Azure AD-Autentiseringstjänsten utfärdar en ID-token som kan användas som en ägartoken om resursen är samma som klient-ID (i det här fallet detta gäller skick webb-API Apps egen backend).

## <a name="code-samples"></a>Kodexempel

Se den [kodexempel för ensidesapplikation scenarier](sample-v1-code.md#single-page-applications). Se till att Kom tillbaka ofta eftersom nya exempel läggs ofta.

## <a name="app-registration"></a>Appregistrering

* Enskild klient - om du skapar ett program för din organisation kan den registreras i företagets katalog med hjälp av Azure portal.
* Flera innehavare - om du skapar ett program som kan användas av användare utanför organisationen, det måste vara registrerad i företagets katalog, men även måste registreras i varje organisations katalog som kommer att använda programmet. Du kan inkludera en registreringsprocess för dina kunder som gör det möjligt för dem att godkänna ditt program för att göra programmet tillgängligt i sin katalog. När de registrerar sig för ditt program, kommer de att visas en dialogruta som visar de behörigheter som programmet kräver och sedan alternativet för att godkänna. Beroende på behörigheterna som krävs kanske en administratör i den andra organisationen måste ge ditt medgivande. När användaren eller administratören godkänner, registreras programmet i sin katalog.

När du har registrerat programmet, måste den konfigureras för att använda implicit beviljande av OAuth 2.0-protokollet. Det här protokollet är inaktiverat för program som standard. Aktivera protokoll för OAuth2-implicit beviljande för ditt program, redigera dess programmanifestet från Azure-portalen och ange ”oauth2AllowImplicitFlow”-värdet till true. Mer information finns i [programmanifestet](reference-app-manifest.md).

## <a name="token-expiration"></a>Giltighetstid för token

Med hjälp av ADAL.js hjälper med:

* Uppdatera en utgångna token
* begär en åtkomsttoken att anropa ett webb-API-resursen

Efter en lyckad autentisering skriver en cookie i användarens webbläsare för att upprätta en session i Azure AD. Observera att sessionen finns mellan användare och Azure AD (inte mellan användaren och webbprogrammet). När en token upphör att gälla använder ADAL.js den här sessionen tyst hämtar en annan token. ADAL.js använder en dold iFrame för att skicka och ta emot begäran med implicit beviljande av OAuth-protokollet. ADAL.js kan också använda samma mekanism för att erhålla åtkomsttoken tyst för andra webb-API-resurser som programmet anropar så länge dessa resurser stöd för cross-origin resource sharing (CORS) har registrerats i katalogen för den användaren och alla nödvändiga tillstånd har anges av användaren under inloggning.

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [programtyper och scenarier](app-types.md)
* Lär dig mer om Azure AD [grunder](authentication-scenarios.md)
