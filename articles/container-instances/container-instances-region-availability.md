---
title: "Region- och resurstillgänglighet för Azure Container Instances"
description: "Ta reda på vilka Azure-regioner som har stöd för distribution av behållarinstanser, samt processor- och minnesgränser för dessa instanser."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 12/15/2017
ms.author: marsma
ms.openlocfilehash: ec7f469c47924f4ae22d6509996ca9cf498fc9ad
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Regional tillgänglighet för Azure Container Instances

I förhandsversionen är Azure Container Instances tillgängligt i följande regioner med angivna processor- och minnesgränser.

| Plats | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Europa, västra; USA, västra; USA, östra | Linux | 4 | 14 |
| Europa, västra; USA, västra; USA, östra | Windows | 4 | 14 |

## <a name="resource-availability"></a>Resurstillgänglighet

Behållarinstanser som har skapats inom dessa resursgränser finns i mån av tillgång i distributionsregionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser.

Du kan försöka lindra sådana distributionsfel genom att prova att distribuera instanser med lägre processor- och minnesinställningar. Du kan även prova att genomföra distributionen senare.

## <a name="next-steps"></a>Nästa steg

Mer information om att felsöka distribution av behållarinstanser finns i [Troubleshoot deployment issues with Azure Container Instances](container-instances-troubleshooting.md) (Felsöka distributionsproblem med Azure Container Instances).