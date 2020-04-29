---
title: Vad är API:et för entitetssökning i Bing?
titleSuffix: Azure Cognitive Services
description: Använd API för entitetsökning i Bing för att extrahera och söka efter entiteter och platser från sökfrågor.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2f374e29f4dc5406956cd56d1bb0bd1466e65773
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75384527"
---
# <a name="what-is-bing-entity-search-api"></a>Vad är API för entitetsökning i Bing?

API:et för entitetsökning i Bing skickar en sökfråga till Bing och hämtar resultat som innehåller entiteter och platser. Platsresultat omfattar restauranger, hotell eller andra lokala företag. Bing returnerar platser om frågan anger namnet på det lokala företaget eller frågar efter en typ av verksamhet (exempelvis restauranger i närheten). Bing returnerar entiteter om frågan anger välkända personer, platser (turist Attractions, stater, länder/regioner osv.) eller saker.

|Funktion  |Beskrivning  |
|---------|---------|
|[Sökförslag i realtid](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Ge sökförslag som kan visas som en listruta när dina användare skriver.       | 
| [Tvetydiga entiteter](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Hämta flera entiteter för frågor med flera möjliga innebörder. |
| [Söka efter platser](concepts/search-for-entities.md#find-places) | Sök efter och returnera information om lokala företag och entiteter  |

## <a name="workflow"></a>Arbetsflöde

API:et för entitetssökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda tjänsten med hjälp av antingen REST API eller SDK.

1. Skapa ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) kostnads fritt.
2. Skicka en begäran till API:et med en giltig sökfråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

* Prova med den [interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) av API:et för entitetssökning i Bing. 
* Kom igång snabbt med din första begäran genom att testa en [Snabbstart](quickstarts/csharp.md).
* Referensavsnittet [API för entitetsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
* I [användnings- och visningskraven för Bing](./use-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.
* Gå till [sidan Bing-sökning API Hub](../bing-web-search/search-the-web.md) och utforska de andra tillgängliga API: erna.