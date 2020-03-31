---
title: merSomDenna (förhandsgranskningsfunktion)
titleSuffix: Azure Cognitive Search
description: Beskriver funktionen moreLikeThis (preview), som är tillgänglig i förhandsversioner av AZURE Cognitive Search REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873819"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (förhandsversion) i Azure Cognitive Search

> [!IMPORTANT] 
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för portal eller .NET SDK.

`moreLikeThis=[key]`är en frågeparameter i [API:et för sökdokument](https://docs.microsoft.com/rest/api/searchservice/search-documents) som hittar dokument som liknar det dokument som anges av dokumentnyckeln. När en sökbegäran `moreLikeThis`görs med genereras en fråga med söktermer som extraheras från det angivna dokumentet som beskriver dokumentet bäst. Den genererade frågan används sedan för att göra sökbegäran. Som standard beaktas innehållet i alla sökbara fält, minus eventuella `searchFields` begränsade fält som du har angett med parametern. Parametern `moreLikeThis` kan inte användas med `search=[string]`sökparametern .

Som standard beaktas innehållet i alla sökbara fält på den högsta nivån. Om du vill ange vissa fält i `searchFields` stället kan du använda parametern. 

Du kan `MoreLikeThis` inte använda på sökbara underfält i en [komplex typ](search-howto-complex-data-types.md).

## <a name="examples"></a>Exempel

Alla följande exempel använder exempel på hotell från [Snabbstart: Skapa ett sökindex i Azure-portalen](search-get-started-portal.md).

### <a name="simple-query"></a>Exempelfråga

Följande fråga hittar dokument vars beskrivningsfält är mest lika det `moreLikeThis` fält i källdokumentet som anges av parametern:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

I det här exemplet söker begäran efter `HotelId` hotell som liknar den med 29.
I stället för att använda HTTP `MoreLikeThis` GET kan du också anropa med HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Använda filter

`MoreLikeThis`kan kombineras med andra vanliga `$filter`frågeparametrar som . Frågan kan till exempel begränsas till endast hotell vars kategori är "Budget" och där klassificeringen är högre än 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Markera fält och begränsa resultat

Väljaren `$top` kan användas för att begränsa hur många `MoreLikeThis` resultat som ska returneras i en fråga. Dessutom kan fält väljas med `$select`. Här väljs de tre bästa hotellen tillsammans med deras ID, Namn och Betyg: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Nästa steg

Du kan använda valfritt webbtestverktyg för att experimentera med den här funktionen.  Vi rekommenderar att du använder Postman för den här övningen.

> [!div class="nextstepaction"]
> [Utforska Azure Cognitive Search REST API:er med Postman](search-get-started-postman.md)
