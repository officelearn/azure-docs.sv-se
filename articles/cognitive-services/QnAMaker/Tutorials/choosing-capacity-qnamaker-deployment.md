---
title: Kapacitet för distribution – QnA Maker
titleSuffix: Azure Cognitive Services
description: Innan du skapar QnA Maker-tjänsten kan bestämma du vilken nivå av tjänsterna ovan passar dig.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: a332d263526bb6507e7394c205caa1c4d1f9e3e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613278"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Välja kapacitet för QnA Maker-distribution

QnA Maker-tjänsten får ett beroende på tre Azure-resurser:
1.  App Service (för runtime)
2.  Azure Search (för att lagra kunskapsbaser)
3.  App Insights (valfritt, för att lagra chattloggarna och telemetri)

Innan du skapar QnA Maker-tjänsten kan bestämma du vilken nivå av tjänsterna ovan passar dig. 

Det finns vanligtvis tre parametrar som du behöver tänka på:

1. **Dataflödet som du behöver från tjänsten**: Välj lämplig [App Plan](https://azure.microsoft.com/pricing/details/app-service/plans/) för din apptjänst utifrån dina behov. Du kan [skala upp](https://docs.microsoft.com/azure/app-service/web-sites-scale) eller ned appen. Det bör också påverka valet av Azure Search-SKU, mer information finns i [här](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Diskstorleken, antalet kunskapsbaser**: Välj lämplig [Azure Sök SKU](https://azure.microsoft.com/pricing/details/search/) för ditt scenario. Du kan publicera N-1 kunskapsbaser i en viss nivå, där N är maximalt antal index tillåts i nivån. Kontrollera även den maximala storleken och antalet dokument som tillåts per nivå.

    Om din nivå har 15 tillåtna index, kan du publicera 14 kunskapsbaser (1 index per publicerade knowledge base). Femtonde index används för alla kunskapsbaser för redigering och testning. 

1. **Antal dokument som källor**: Kostnadsfri SKU för hanteringstjänsten QnA Maker begränsar antalet dokument som du kan hantera via portalen och API: er till 3 (i 1 MB storlek varje). Standard SKU har ingen gräns för antalet dokument som du kan hantera. Läs mer [här](https://aka.ms/qnamaker-pricing).

Följande tabell innehåller riktlinjer på hög nivå.

|                        | QnA Maker-hantering | App Service | Azure Search | Begränsningar                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentering        | Kostnadsfri SKU             | Kostnadsfri nivå   | Kostnadsfri nivå    | Publicera upp till 2 kB-artiklar, 50 MB storlek  |
| Miljö för utveckling/testning   | Standard-SKU         | Delad      | Basic        | Publicera upp till 14 KB-artiklar, 2 GB storlek    |
| Produktionsmiljö | Standard-SKU         | Basic       | Standard     | Publicera upp till 49 kB-artiklar, 25 GB storlek |

Uppgradera din QnA Maker-stack finns i [uppgradera QnA Maker-tjänsten](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Uppgradera din QnA Maker-tjänsten](../How-To/upgrade-qnamaker-service.md)
