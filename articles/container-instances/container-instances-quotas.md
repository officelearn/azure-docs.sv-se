---
title: Kvoter och regional tillgänglighet för Azure Container Instances
description: Standardkvoter och regional tillgänglighet för tjänsten Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 02/27/2018
ms.author: marsma
ms.openlocfilehash: 1bc890abc8b406ae75f292f37775e4cb62cf0473
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115283"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Kvoter och regional tillgänglighet för Azure Container Instances

Alla Azure-tjänster har vissa standardgränser och kvoter för resurser och funktioner. Följande avsnitt beskriver standardresursgränserna för flera Azure Container Instances-resurser (ACI), samt tillgänglighet för ACI-tjänsten i Azure-regioner.

## <a name="service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Regional tillgänglighet

Azure Container Instances är tillgängligt i följande regioner med angivna processor- och minnesgränser.

| Plats | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| USA, västra, USA, östra, Europa, västra, Europa, norra | Linux | 4 | 14 |
| USA, Väst 2, Asien, sydöstra | Linux | 2 | 7 |
| Australien, östra, USA, östra 2, USA, centrala | Linux | 1 | 1.5 |
| USA, västra, USA, östra, Europa, västra, Europa, norra | Windows | 4 | 14 |
| USA, Väst 2, Asien, sydöstra | Windows | 2 | 3.5 |

Containerinstanser som har skapats inom dessa resursgränser finns i mån av tillgång i distributionsregionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser. Du kan försöka lindra sådana distributionsfel genom att prova att distribuera instanser med lägre processor- och minnesinställningar. Du kan även prova att genomföra distributionen senare.

Informera teamet om ytterligare regioner som krävs eller ökade begränsningar för CPU/minne på [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Mer information om att felsöka distribution av behållarinstanser finns i [Troubleshoot deployment issues with Azure Container Instances](container-instances-troubleshooting.md) (Felsöka distributionsproblem med Azure Container Instances).

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om du vill begära en ökning av en eller flera resurser som kan ökas skickar du in en [Azure-supportbegäran][azure-support] (välj ”Kvot” för **Typ av problem**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
