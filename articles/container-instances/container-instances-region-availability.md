---
title: Resurstillgänglighet för Azure Container Instances
description: Tillgängligheten för resurser för beräkning och minne för tjänsten Azure Container Instances i olika Azure-regioner.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60537774"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Resurstillgänglighet för Azure Container Instances i Azure-regioner

Den här artikeln beskriver tillgängligheten för Azure Container Instances beräknings- och minnesresurser resurser i Azure-regioner. 

Värden som visas är de maximala resurserna som är tillgängliga per distribution av en [behållargruppen](container-instances-container-groups.md). Värden är aktuella vid tidpunkten för publiceringen. Använd API [Listfunktioner](/rest/api/container-instances/listcapabilities/listcapabilities) för uppdaterad information. 

> [!NOTE]
> Behållargrupper som skapats inom dessa resursgränser finns mån av tillgång i distributionsregionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser. För att lösa sådana distributionsfel, försök distribuera instanser med lägre resursinställningar eller prova att genomföra distributionen vid ett senare tillfälle.

Läs om hur kvoter och andra begränsningar i dina distributioner [kvoter och begränsningar för Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Tillgänglighet – Allmänt

| Location | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada, centrala; USA, centrala; USA, östra 2; USA, södra centrala | Linux | 4 | 16 |
| USA, östra; Europa, norra; Europa, västra; USA, västra; USA, västra 2 | Linux | 4 | 14 |
| Östra Japan | Linux | 2 | 8 |
| Australien, östra; Asien, sydöstra | Linux | 2 | 7 |
| Indien, centrala; Asien, östra; USA, norra centrala; Indien, södra | Linux | 2 | 3.5 |
| USA, östra; Europa, västra; USA, västra | Windows | 4 | 14 |
| Australien, östra; Kanada, centrala; Indien, centrala; USA, centrala; Asien, östra; USA, östra 2; Japan, östra; USA, norra centrala; Europa, centrala; USA, södra centrala; Indien, södra; Asien, sydöstra; USA, västra 2 | Windows | 2 | 3.5 |

## <a name="availability---virtual-network-deployment-preview"></a>Tillgänglighet – Virtual network-distributionen (förhandsversion)

Följande regioner och resurser är tillgängliga för en behållargrupp som distribueras i en [Azure-nätverk](container-instances-vnet.md) (förhandsversion)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Tillgänglighet – GPU-resurser (förhandsgranskning)

Följande regioner och resurser är tillgängliga för en behållargrupp som distribueras med [GPU resurser](container-instances-gpu.md) (förhandsversion)

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Nästa steg

Informera teamet veta om du vill se ytterligare regioner eller ökad resurstillgänglighet på [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Information om felsökning av distribution av behållarinstanser finns i [felsöka distributionsproblem med Azure Container Instances](container-instances-troubleshooting.md).
