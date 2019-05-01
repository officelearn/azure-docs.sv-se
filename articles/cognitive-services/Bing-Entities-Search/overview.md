---
title: Vad är API:et för entitetssökning i Bing?
titlesuffix: Azure Cognitive Services
description: Använd API för entitetsökning i Bing för att extrahera och söka efter entiteter och platser från sökfrågor.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 0755688d1cc2f00ffef253b7695d9cb504f9e169
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866847"
---
# <a name="what-is-bing-entity-search-api"></a>Vad är API för entitetsökning i Bing?

API:et för entitetsökning i Bing skickar en sökfråga till Bing och hämtar resultat som innehåller entiteter och platser. Platsresultat omfattar restauranger, hotell eller andra lokala företag. Bing returnerar platser om frågan anger namnet på det lokala företaget eller frågar efter en typ av verksamhet (exempelvis restauranger i närheten). Bing returnerar entiteter om frågan anger välkända personer, platser (turistattraktioner, tillstånd, länder/regioner, osv.) eller saker.

|Funktion  |Beskrivning  |
|---------|---------|
|[Sökförslag i realtid](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Ge sökförslag som kan visas som en listruta när dina användare skriver.       | 
| [Tvetydiga entiteter](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Hämta flera entiteter för frågor med flera möjliga innebörder. |
| [Söka efter platser](concepts/search-for-entities.md#find-places) | Sök efter och returnera information om lokala företag och entiteter  |

## <a name="workflow"></a>Arbetsflöde

API:et för entitetssökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda tjänsten med hjälp av antingen REST API eller SDK.

1. Skapa ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) kostnadsfritt.
2. Skicka en begäran till API:et med en giltig sökfråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

* Prova med den [interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) av API:et för entitetssökning i Bing. 
* Kom igång snabbt med din första begäran genom att testa en [Snabbstart](quickstarts/csharp.md).
* Referensavsnittet [API för entitetsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference).
* I [användnings- och visningskraven för Bing](./use-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.
