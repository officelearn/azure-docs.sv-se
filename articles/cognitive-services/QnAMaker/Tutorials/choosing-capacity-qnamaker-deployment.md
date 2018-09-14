---
title: Välja kapacitet för distributionen QnA Maker - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: en guide för att välja kapacitet för QnA Maker-distribution
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 71af374fbd08fe1f7568bc1ece2a65af2de3ad19
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573374"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Välja kapacitet för QnA Maker-distribution

QnA Maker-tjänsten får ett beroende på tre Azure-resurser:
1.  App Service (för runtime)
2.  Azure Search (för att lagra kunskapsbaser)
3.  App Insights (valfritt, för att lagra chatlogs och telemetri)

Innan du skapar QnA Maker-tjänsten kan bestämma du vilka nivåer av tjänsterna ovan passar dig. 

Det finns vanligtvis tre parametrar som du behöver tänka på:
1. **Dataflödet som du behöver från tjänsten**: Välj lämpliga [App Plan](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) för din apptjänst utifrån dina behov. Du kan [skala upp](https://docs.microsoft.com/azure/app-service/web-sites-scale) eller ned appen. Det bör också påverka valet av Azure Search-SKU, mer information finns i [här](https://docs.microsoft.com/azure/search/search-sku-tier).

2. **Diskstorleken, antalet kunskapsbaser**: Välj lämplig [Azure Sök SKU](https://azure.microsoft.com/en-in/pricing/details/search/) för ditt scenario. Du kan publicera N-1 kunskapsbaser i en viss nivå, där N är maximalt antal index tillåts i nivån. Kontrollera även den maximala storleken och antalet dokument som tillåts per nivå.

3. **Antal dokument som källor**: kostnadsfria SKU för hanteringstjänsten QnA Maker begränsar antalet dokument som du kan hantera via portalen och API: er till 3 (i 1 MB storlek varje). Standard SKU har ingen gräns för antalet dokument som du kan hantera. Läs mer [här](https://aka.ms/qnamaker-pricing).

Följande tabell innehåller riktlinjer på hög nivå.

|                        | QnA Maker-hantering | App Service | Azure Search | Begränsningar                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentering        | Kostnadsfri SKU             | Kostnadsfri nivå   | Kostnadsfri nivå    | Publicera upp till 2 kB-artiklar, 50 MB storlek  |
| Miljö för utveckling/testning   | Standard-SKU         | Delad      | Basic        | Publicera upp till 4 kB-artiklar, 2GB storlek    |
| Produktionsmiljö | Standard-SKU         | Basic       | Standard     | Publicera upp till 49 kB-artiklar, 25 GB storlek |

Uppgradera din QnA Maker-stack finns i [uppgradera QnA Maker-tjänsten](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Uppgradera din QnA Maker-tjänsten](../How-To/upgrade-qnamaker-service.md)
