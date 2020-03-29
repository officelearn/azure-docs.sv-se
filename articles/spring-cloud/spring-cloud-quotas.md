---
title: Serviceplaner och kvoter för Azure Spring Cloud
description: Läs mer om tjänstkvoter och serviceplaner för Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278897"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvoter och serviceplaner för Azure Spring Cloud

Alla Azure-tjänster anger standardgränser och kvoter för resurser och funktioner.  Under förhandsgranskningsperioden erbjuder Azure Spring Cloud bara en serviceplan.

I den här artikeln beskrivs de servicekvoter som erbjuds under den aktuella förhandsgranskningsperioden.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure Spring Cloud-tjänstnivåer och kvoter

Under förhandsgranskningsperioden erbjuder Azure Spring Cloud endast en tjänstnivå.

Resurs | Belopp
------- | -------
Virtuell processor | 4 per tjänstinstans
Minne | 8 GByte per tjänstinstans
Azure Spring Cloud-tjänstinstanser per region per prenumeration | 10
Totalt antal appinstanser per Azure Spring Cloud-tjänstinstans | 500
Totalt antal appinstanser per fjäderprogram | 20
Beständiga volymer | 10 x 50 GByte

När du når en kvot får du ett 400-fel som lyder: "Kvoten överskrider gränsen för prenumeration *som din prenumeration* i region där din Azure Spring *Cloud-tjänst skapas*.

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om resursen behöver öka [skapar du en supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
