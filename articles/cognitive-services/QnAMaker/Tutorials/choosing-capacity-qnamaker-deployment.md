---
title: Kapacitet för distribution – QnA Maker
titleSuffix: Azure Cognitive Services
description: Innan du skapar QnA Maker-tjänsten kan bestämma du vilken nivå av tjänsterna ovan passar dig.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9e197929ce08f4e0c665f96d1c4ddbd382fdfb22
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084471"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Välja kapacitet för QnA Maker-distribution

QnA Maker-tjänsten får ett beroende på tre Azure-resurser:
1.  App Service (för runtime)
2.  Azure Search (för att lagra kunskapsbaser)
3.  App Insights (valfritt, för att lagra chattloggarna och telemetri)

Innan du skapar QnA Maker-tjänsten kan bestämma du vilken nivå av tjänsterna ovan passar dig. 

Det finns vanligtvis tre parametrar som du behöver tänka på:
1. **Dataflödet som du behöver från tjänsten**: Välj lämpliga [App Plan](https://azure.microsoft.com/pricing/details/app-service/plans/) för din apptjänst utifrån dina behov. Du kan [skala upp](https://docs.microsoft.com/azure/app-service/web-sites-scale) eller ned appen. Det bör också påverka valet av Azure Search-SKU, mer information finns i [här](https://docs.microsoft.com/azure/search/search-sku-tier).

2. **Diskstorleken, antalet kunskapsbaser**: Välj lämplig [Azure Sök SKU](https://azure.microsoft.com/pricing/details/search/) för ditt scenario. Du kan publicera N-1 kunskapsbaser i en viss nivå, där N är maximalt antal index tillåts i nivån. Kontrollera även den maximala storleken och antalet dokument som tillåts per nivå.

3. **Antal dokument som källor**: kostnadsfria SKU för hanteringstjänsten QnA Maker begränsar antalet dokument som du kan hantera via portalen och API: er till 3 (i 1 MB storlek varje). Standard SKU har ingen gräns för antalet dokument som du kan hantera. Läs mer [här](https://aka.ms/qnamaker-pricing).

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
