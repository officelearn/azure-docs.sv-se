---
title: Vad är API:et för entitetssökning i Bing?
titleSuffix: Azure Cognitive Services
description: Lär dig mer om API för entitetsökning i Bing och hur du extraherar och söker efter entiteter och platser från Sök frågor.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: f6f816f6fb4d422a97c98994996967e71f6a1e07
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084909"
---
# <a name="what-is-bing-entity-search-api"></a>Vad är API för entitetsökning i Bing?

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

API:et för entitetsökning i Bing skickar en sökfråga till Bing och hämtar resultat som innehåller entiteter och platser. Platsresultat omfattar restauranger, hotell eller andra lokala företag. Bing returnerar platser om frågan anger namnet på det lokala företaget eller frågar efter en typ av verksamhet (exempelvis restauranger i närheten). Bing returnerar entiteter om frågan anger välkända personer, platser (turist Attractions, stater, länder/regioner osv.) eller saker.

|Funktion  |Beskrivning  |
|---------|---------|
|[Sökförslag i realtid](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Ge sökförslag som kan visas som en listruta när dina användare skriver.       | 
| [Tvetydiga entiteter](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Hämta flera entiteter för frågor med flera möjliga innebörder. |
| [Söka efter platser](concepts/search-for-entities.md#find-places) | Sök efter och returnera information om lokala företag och entiteter  |

## <a name="workflow"></a>Arbetsflöde

API:et för entitetssökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda tjänsten med hjälp av antingen REST API eller SDK.

1. Skapa ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/free/cognitive-services/) kostnads fritt.
2. Skicka en begäran till API:et med en giltig sökfråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

* Prova med den [interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) av API:et för entitetssökning i Bing. 
* Kom igång snabbt med din första begäran genom att testa en [Snabbstart](quickstarts/csharp.md).
* Referensavsnittet [API för entitetsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
* I [användnings- och visningskraven för Bing](./use-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.
* Gå till [sidan Bing-sökning API Hub](../bing-web-search/search-the-web.md) och utforska de andra tillgängliga API: erna.