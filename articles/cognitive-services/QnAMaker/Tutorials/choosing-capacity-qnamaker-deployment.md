---
title: Kapacitet för distribution – QnA Maker
titleSuffix: Azure Cognitive Services
description: Innan du skapar QnA Maker-tjänsten kan bestämma du vilken nivå av tjänsterna ovan passar dig.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2d8f0fce3cb8f1cd8fdb596cb4e238a79d6cee4c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193500"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Välja kapacitet för QnA Maker-distribution

QnA Maker-tjänsten får ett beroende på tre Azure-resurser:
1.  App Service (för runtime)
2.  Azure Search (för lagring och sökning av kring)
3.  App Insights (valfritt, för att lagra chattloggarna och telemetri)

Innan du skapar QnA Maker-tjänsten kan bestämma du vilken nivå av tjänsterna ovan passar dig. 

Det finns vanligtvis tre parametrar som du behöver tänka på:

1. **Det data flöde du behöver från tjänsten**: Välj lämplig [app-plan](https://azure.microsoft.com/pricing/details/app-service/plans/) för din app service utifrån dina behov. Du kan [skala upp](https://docs.microsoft.com/azure/app-service/manage-scale-up) eller ned appen. Det bör också påverka valet av Azure Search-SKU, mer information finns i [här](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Storlek och antal kunskaps baser**: Välj lämplig [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) för ditt scenario. Du kan publicera N-1 kunskapsbaser i en viss nivå, där N är maximalt antal index tillåts i nivån. Kontrollera även den maximala storleken och antalet dokument som tillåts per nivå.

    Om din nivå till exempel har 15 tillåtna index, kan du publicera 14 kunskaps banker (1 index per publicerad kunskaps bas). Det femtonde indexet används för alla kunskaps banker för redigering och testning. 

1. **Antal dokument som källor**: Den kostnads fria SKU: n av tjänsten QnA Maker hantering begränsar antalet dokument som du kan hantera via portalen och API: erna till 3 (med en storlek på 1 MB). Standard SKU har ingen gräns för antalet dokument som du kan hantera. Läs mer [här](https://aka.ms/qnamaker-pricing).

Följande tabell innehåller riktlinjer på hög nivå.

|                        | QnA Maker-hantering | App Service | Azure Search | Begränsningar                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentering        | Kostnadsfri SKU             | Kostnadsfri nivå   | Kostnadsfri nivå    | Publicera upp till 2 kB-artiklar, 50 MB storlek  |
| Miljö för utveckling/testning   | Standard-SKU         | Delad      | Basic        | Publicera upp till 14 KB-artiklar, 2 GB storlek    |
| Produktionsmiljö | Standard-SKU         | Basic       | Standard     | Publicera upp till 49 kB-artiklar, 25 GB storlek |

Uppgradera din QnA Maker-stack finns i [uppgradera QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Uppgradera din QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
