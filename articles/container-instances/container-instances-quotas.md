---
title: Kvoter och regional tillgänglighet för Azure Container Instances
description: Standardkvoter och regional tillgänglighet för tjänsten Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: 647890517e6f08a4602ebed8ee1057cb45f10cbe
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075506"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Kvoter och regional tillgänglighet för Azure Container Instances

Alla Azure-tjänster har vissa standardgränser och kvoter för resurser och funktioner. Följande avsnitt beskriver standardresursgränserna för flera Azure Container Instances-resurser (ACI), samt tillgänglighet för ACI-tjänsten i Azure-regioner.

## <a name="service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Regional tillgänglighet

Azure Container Instances är tillgängligt i följande regioner med angivna processor- och minnesgränser. Värden är aktuella vid tidpunkten för publiceringen. Använd API [Listfunktioner](/rest/api/container-instances/listcapabilities/listcapabilities) för uppdaterad information. Tillgänglighets- och resursbegränsningarna kan variera när du använder Azure Container Instances med ett [virtuellt nätverk](container-instances-vnet.md) (förhandsversion) eller med [GPU-resurser](container-instances-gpu.md) (förhandsversion).

| Plats | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Centrala Kanada | Linux | 4 | 16 |
| USA, östra; Europa, norra; Europa, västra; USA, västra; USA, västra 2 | Linux | 4 | 14 |
| Östra Japan | Linux | 2 | 8 |
| Australien, östra; USA, östra 2; Asien, sydöstra | Linux | 2 | 7 |
| Centrala Indien, Asien och stillahavsområdet, norra centrala USA, södra centrala USA, södra Indien | Linux | 2 | 3.5 |
| USA, östra; Europa, västra; USA, västra | Windows | 4 | 14 |
| Östra Australien, centrala Kanada, centrala Indien, Asien och stillahavsområdet, östra USA 2, östra Japan, norra centrala USA, Nordeuropa, södra centrala USA, södra Indien, sydöstra Asien, västra USA 2 | Windows | 2 | 3.5 |

Containerinstanser som har skapats inom dessa resursgränser finns i mån av tillgång i distributionsregionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser. Du kan försöka lindra sådana distributionsfel genom att prova att distribuera instanser med lägre processor- och minnesinställningar. Du kan även prova att genomföra distributionen senare.

Informera teamet om ytterligare regioner som krävs eller ökade begränsningar för CPU/minne på [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Mer information om att felsöka distribution av behållarinstanser finns i [Troubleshoot deployment issues with Azure Container Instances](container-instances-troubleshooting.md) (Felsöka distributionsproblem med Azure Container Instances).

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om du vill begära en ökning av en eller flera resurser som kan ökas skickar du in en [Azure-supportbegäran][azure-support] (välj ”Kvot” för **Typ av problem**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
