---
title: Azure Active Directory-större ändringar referens | Microsoft Docs
description: Ändringar i Azure AD-protokoll som kan påverka ditt program.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 14217d03cdb56c5c641ab8f8c3fc0748e8e815a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987798"
---
# <a name="whats-new-for-authentication"></a>Vad är nytt för autentisering? 

|
>Håll dig informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att lägga till detta [URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) på din RSS-feed-läsare.

System för användarautentisering ändrar och lägger till funktioner med jämna mellanrum för att förbättra säkerheten och förbättra överensstämmelse med standarder. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

- De senaste funktionerna
- Kända problem
- Protokollet ändringar
- Inaktuell funktion

> [!TIP] 
> Den här sidan uppdateras regelbundet, så gå tillbaka ofta. Om inget annat anges, placera ändringarna endast för nyligen registrerade program.  

## <a name="upcoming-changes"></a>Kommande ändringar

### <a name="authorization-codes-can-no-longer-be-reused"></a>Auktoriseringskoder kan inte längre återanvändas

**Ikraftträdandedatum**: 10 oktober 2018 **slutpunkter påverkas**: både v1.0 och v2.0 **protokollet påverkas**: [Code flöde](v2-oauth2-auth-code-flow.md)

Från och med 10 oktober 2018, att Azure AD sluta acceptera tidigare använda autentisering-koder för nya appar. Alla appar som skapats innan den 10 oktober 2018 kommer fortfarande att kunna återanvända koder för autentisering. Ändringen security hjälper dig att ta med Azure AD i enlighet med OAuth-specifikationen och tillämpas på både v1 och v2-slutpunkter.

Om din app återanvänder auktoriseringskoder för att hämta token för flera resurser, rekommenderar vi att du använda koden för att få en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger i flera resurser. Alla nya appar som försöker att återanvända en Autentiseringskod under flödet för OAuth-kod får ett invalid_grant-fel.

Läs mer om uppdateringstoken [uppdatera åtkomsttoken](v1-protocols-oauth-code.md#refreshing-the-access-tokens).

## <a name="may-2018"></a>Maj 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID-token kan inte användas för OBO-flöde

**Datum**: 1 maj 2018 **slutpunkter påverkas**: både v1.0 och v2.0 **protokoll påverkas**: Implicit flöde och [OBO-flöde](v1-oauth2-on-behalf-of-flow.md)

Efter den 1 maj 2018 kan id_tokens inte användas som kontrollen i ett OBO-flöde för nya program.  Åtkomsttoken ska användas i stället för att skydda API: er, även mellan en klient och mellannivå av samma program.  Appar som är registrerade innan den 1 maj 2018 fortsätter att fungera och att kunna exchange id_tokens för en åtkomst-token - men detta anses inte bästa praxis.

För att undvika den här ändringen kan göra du följande:

1. Skapa ett webb-API för ditt program på mellannivå, med ett eller flera scope.  Detta gör att störrre kontroll och säkerhet.
1. I appmanifestet, den [Azure-portalen](https://portal.azure.com), eller [App Registreringsportalen](https://apps.dev.microsoft.com), se till att appen får åtkomst-token via det implicita flödet. Detta styrs den `oauth2AllowImplicitFlow` nyckel.
1. När klientprogrammet begär en id_token via `response_type=id_token`, också begära en åtkomsttoken (`response_type=token`) för webb-API som skapades ovan.  Därför när du använder v2.0-slutpunkten i `scope` parametern bör likna `api://GUID/SCOPE`.  På slutpunkten för v1.0 den `resource` parameter ska vara URI: n som webb-API.
1. Skicka den här åtkomst-token till mellannivån i stället för id_token.  