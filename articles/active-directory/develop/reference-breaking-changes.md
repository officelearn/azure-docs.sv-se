---
title: Azure Active Directory-större ändringar referens | Microsoft Docs
description: Läs mer om ändringar som gjorts i Azure AD-protokoll som kan påverka ditt program.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcc400f952cc89f5fb4bf6e8d6f0f331483868e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401302"
---
# <a name="whats-new-for-authentication"></a>Vad är nytt för autentisering? 

>Håll dig informerad om uppdateringar till den här sidan. Lägg bara till [denna URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) på din RSS-feed-läsare.

System för användarautentisering ändrar och lägger till funktioner med jämna mellanrum att förbättra säkerhet och överensstämmelse med standarder. Om du vill hålla dig uppdaterad med den senaste utvecklingen, ger den här artikeln dig information om följande information:

- De senaste funktionerna
- Kända problem
- Protokollet ändringar
- Inaktuell funktion

> [!TIP] 
> Den här sidan uppdateras regelbundet, så besök ofta. Om inget annat anges, placera ändringarna endast för nyligen registrerade program.  

## <a name="upcoming-changes"></a>Kommande ändringar

Ingen schemalagd just nu. 

## <a name="march-2019"></a>Mars 2019

### <a name="looping-clients-will-be-interrupted"></a>Slingor klienter avbryts

**Ikraftträdandedatum**: Den 25 mars 2019

**Slutpunkter som påverkas**: Både v1.0 och v2.0

**Protokollet som påverkas**: Alla flöden

Klientprogram kan ibland förstöra, utfärda hundratals samma inloggningsförfrågan under en kort tidsperiod.  Dessa begäranden kan eller inte lyckas, men alla bidra till dålig användarupplevelsen och förhöjd arbetsbelastningar för IDP: N, ökar svarstiden för alla användare och minska tillgängligheten för IDP: N.  Dessa program arbetar utanför gränserna för normal användning och ska uppdateras för att fungera korrekt.  

Klienter som utfärda duplicerade flera gånger skickas en `invalid_grant` fel: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

De flesta klienter behöver inte att ändra beteende för att undvika det här felet.  Endast felkonfigurerad klienter (de som saknar tokencachelagring eller de uppvisar fråga slingor redan) påverkas av det här felet.  Klienter spåras på basis av per instans lokalt (via cookie) på följande faktorer:

* Användare-tipset eventuellt

* Scope- eller resursen som efterfrågades

* Klientorganisations-ID

* Omdirigerings-URI

* Svarstypen och läge

Appar som har att göra flera förfrågningar (15 +) i en kort tidsperiod (5 minuter) får en `invalid_grant` felmeddelande om att de slingor.  De token som begärts ha tillräckligt långlivade livslängd (10 minuter minst, 60 minuter som standard), så upprepade begäranden under den här tidsperioden är onödiga.  

Alla appar som ska hantera `invalid_grant` genom som visar en interaktiv prompt i stället för att begära ett token tyst.  För att undvika det här felet, klienter bör se till att de korrekt cachelagring de token som de får.


## <a name="october-2018"></a>Oktober 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Auktoriseringskoder kan inte längre återanvändas

**Ikraftträdandedatum**: Den 15 november 2018

**Slutpunkter som påverkas**: Både v1.0 och v2.0

**Protokollet som påverkas**: [Kodflöde](v2-oauth2-auth-code-flow.md)

Från och med 15 November 2018, att Azure AD sluta acceptera tidigare autentisering-koder för appar. Ändringen security hjälper dig att ta med Azure AD i enlighet med OAuth-specifikationen och tillämpas på både v1 och v2-slutpunkter.

Om din app återanvänder auktoriseringskoder för att hämta token för flera resurser, rekommenderar vi att du använda koden för att få en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger i flera resurser. Alla nya appar som försöker att återanvända en Autentiseringskod under flödet för OAuth-kod får ett invalid_grant-fel.

Läs mer om uppdateringstoken [uppdatera åtkomsttoken](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Om du använder ADAL eller MSAL, hanteras åt dig i Library - Ersätt den andra förekomsten av ”AcquireTokenByAuthorizationCodeAsync' med 'AcquireTokenSilentAsync'. 

## <a name="may-2018"></a>Maj 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID-token kan inte användas för OBO-flöde

**Datum**: 1 maj 2018

**Slutpunkter som påverkas**: Både v1.0 och v2.0

**Protokoll som påverkas**: Implicit flöde och [OBO-flöde](v1-oauth2-on-behalf-of-flow.md)

Efter den 1 maj 2018 kan id_tokens inte användas som kontrollen i ett OBO-flöde för nya program. Åtkomsttoken ska användas i stället för att skydda API: er, även mellan en klient och mellannivå av samma program. Appar som är registrerade innan den 1 maj 2018 fortsätter att fungera och att kunna exchange id_tokens för en åtkomsttoken; Det här mönstret är dock inte anses vara bästa praxis.

Du kan undvika den här ändringen, kan du göra följande:

1. Skapa ett webb-API för ditt program med ett eller flera scope. Den här explicit registreringspunkten låter störrre kontroll och säkerhet.
1. I appmanifestet, i den [Azure-portalen](https://portal.azure.com) eller [app registreringsportalen](https://apps.dev.microsoft.com), se till att appen får åtkomst-token via det implicita flödet. Detta styrs via den `oauth2AllowImplicitFlow` nyckel.
1. När klientprogrammet begär en id_token via `response_type=id_token`, också begära en åtkomsttoken (`response_type=token`) för webb-API som skapades ovan. Därför när du använder v2.0-slutpunkten i `scope` parametern bör likna `api://GUID/SCOPE`. På slutpunkten för v1.0 den `resource` parameter ska vara URI: n som webb-API.
1. Skicka den här åtkomst-token till mellannivån i stället för id_token.  
