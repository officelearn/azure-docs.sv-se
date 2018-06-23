---
title: Om du väljer kapacitet för distributionen av frågor och svar om Maker - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: en guide till att välja kapaciteten för frågor och svar om Maker-distribution
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b0219b9f7dbbee52406dab9d808134fa2e2a689d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354063"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Om du väljer kapacitet för frågor och svar om Maker distributionen

Tjänsten frågor och svar om Maker tar ett beroende på tre Azure-resurser:
1.  Apptjänst (för runtime)
2.  Azure Search (för att lagra QnAs)
3.  App Insights (valfritt, för att lagra chatlogs och telemetri)

Innan du skapar frågor och svar om Maker tjänsten måste bestämma du vilka nivåer av tjänsterna ovan är rätt för dig. 

Det finns vanligtvis tre parametrar som du behöver tänka på:
1. **Dataflödet som du behöver från tjänsten**: Välj lämpliga [App Plan](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) för din apptjänst utifrån dina behov. Du kan [skala upp](https://docs.microsoft.com/en-us/azure/app-service/web-sites-scale) eller ned appen. Det bör även påverka valet Azure Search SKU finns mer information [här](https://docs.microsoft.com/en-us/azure/search/search-sku-tier).

2. **Storlek och antalet knowledge baser**: Välj lämpliga [Azure söka SKU](https://azure.microsoft.com/en-in/pricing/details/search/) för ditt scenario. Du kan publicera N-1 knowledge databaser i en viss nivå, där N är maximala index som tillåts i nivån. Kontrollera också den maximala storleken och antal dokument som tillåts per nivå.

3. **Antal dokument som källor**: ledigt SKU: N för management-tjänsten för frågor och svar om Maker begränsar antalet dokument som du kan hantera via portalen och API: er till 3 (i 1 MB storlek varje). Standarden SKU har några gränser för antal dokument som du kan hantera. Se mer information [här](https://aka.ms/qnamaker-pricing).

I följande tabell ger en övergripande riktlinjer.

|                        | Frågor och svar om Maker Management | App Service | Azure Search | Begränsningar                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentering        | Ledigt SKU             | Kostnadsfri nivå   | Kostnadsfri nivå    | Publicera upp till 2 KBs, 50 MB storlek  |
| Miljö för utveckling och testning   | Standard-SKU         | Delad      | Basic        | Publicera upp till 4 KBs, 2GB storlek    |
| Produktionsmiljö | Standard-SKU         | Basic       | Standard     | Publicera upp till 49 KBs, 25 GB storlek |

För att uppgradera dina frågor och svar om Maker stack finns [uppgradera tjänsten frågor och svar om Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Uppgradera tjänsten frågor och svar om Maker](../How-To/upgrade-qnamaker-service.md)
