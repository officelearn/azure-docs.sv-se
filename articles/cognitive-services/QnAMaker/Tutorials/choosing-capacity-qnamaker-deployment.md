---
title: Resurs kapacitet för distribution – QnA Maker
titleSuffix: Azure Cognitive Services
description: Innan du skapar din QnA Maker-tjänst bör du bestämma vilken nivå av tjänsterna ovan som passar dig bäst.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5cbdb6fcf9fcdf12b54ff1db4b577bb975517131
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793952"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Välja kapacitet för din QnA Maker-distribution

QnA Makers tjänsten tar ett beroende på tre Azure-resurser:
1.  App Service (för körning)
2.  Azure Kognitiv sökning (för lagring och sökning av kring)
3.  App Insights (valfritt, för lagring av chatt-loggar och telemetri)

Innan du skapar din QnA Maker-tjänst bör du bestämma vilken nivå av tjänsterna ovan som passar dig bäst. 

Det finns vanligt vis tre parametrar som du behöver tänka på:

1. **Det data flöde du behöver från tjänsten**: Välj lämplig [app-plan](https://azure.microsoft.com/pricing/details/app-service/plans/) för din app service utifrån dina behov. Du kan [skala upp](https://docs.microsoft.com/azure/app-service/manage-scale-up) eller ned appen. Detta bör även påverka din Azure Kognitiv sökning SKU-val, se mer information [här](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Storlek och antal kunskaps baser**: Välj lämplig [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) för ditt scenario. Du kan publicera N-1 kunskaps baser på en viss nivå, där N är de maximala index som tillåts på nivån. Kontrol lera också den maximala storleken och antalet dokument som tillåts per nivå.

    Om din nivå till exempel har 15 tillåtna index, kan du publicera 14 kunskaps banker (1 index per publicerad kunskaps bas). Det femtonde indexet används för alla kunskaps banker för redigering och testning. 

1. **Antal dokument som källor**: den kostnads fria SKU: n för QNA Maker hanterings tjänsten begränsar antalet dokument som du kan hantera via portalen och API: erna till 3 (med en storlek på 1 MB). Standard-SKU: n har ingen gräns för antalet dokument som du kan hantera. Se mer information [här](https://aka.ms/qnamaker-pricing).

I följande tabell får du några rikt linjer på hög nivå.

|                        | QnA Maker hantering | App Service | Kognitiv sökning i Azure | Begränsningar                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentering        | Kostnads fri SKU             | Kostnadsfri nivå   | Kostnadsfri nivå    | Publicera upp till 2 KB, 50 MB storlek  |
| Utvecklings-och test miljö   | Standard-SKU         | Delad      | Basic        | Publicera upp till 14 KB, 2 GB storlek    |
| Produktions miljö | Standard-SKU         | Basic       | Standard     | Publicera upp till 49 KB, 25 GB storlek |

Information om hur du uppgraderar QnA Maker stack finns i [uppgradera QNA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Uppgradera din QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
