---
title: Resurstillgänglighet för Azure Container Instances
description: Tillgängligheten för resurser för beräkning och minne för tjänsten Azure Container Instances i olika Azure-regioner.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 64b60178413e470cc7fe9b3991c6fc29b5a0f860
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794301"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Resurstillgänglighet för Azure Container Instances i Azure-regioner

Den här artikeln beskriver tillgängligheten för Azure Container Instances beräknings- och minnesresurser resurser i Azure-regioner. 

Värden som visas är de maximala resurserna som är tillgängliga per distribution av en [behållargruppen](container-instances-container-groups.md). Värden är aktuella vid tidpunkten för publiceringen. 

> [!NOTE]
> Behållargrupper som skapats inom dessa resursgränser finns mån av tillgång i distributionsregionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser. För att lösa sådana distributionsfel, försök distribuera instanser med lägre resursinställningar eller prova att genomföra distributionen vid ett senare tillfälle.

Läs om hur kvoter och andra begränsningar i dina distributioner [kvoter och begränsningar för Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Tillgänglighet – Allmänt

Följande regioner och resurser är tillgängliga för behållargrupper med Linux och [stöds](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016-baserade behållare.

| Location | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada, centrala Kanada, centrala Indien, centrala USA, östra Asien, östra USA, östra USA 2, Nordeuropa, södra centrala USA, Sydostasien, Storbritannien, södra, USA, västra | Linux | 4 | 16 |
| Västra Europa, västra USA 2 | Linux | 4 | 14 |
| Östra Japan, östra | Linux | 2 | 8 |
| Norra centrala USA, södra Indien | Linux | 2 | 3.5 |
| Europa, västra | Windows | 4 | 16 |
| Östra USA, västra USA | Windows | 4 | 14 |
| Australien, östra, Kanada, centrala Kanada, centrala Indien, centrala USA, östra Asien, östra USA 2, Japan östra, norra centrala USA, Nordeuropa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, USA, västra 2 | Windows | 2 | 3.5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Tillgänglighet – Windows Server 2019 LTSC 1809 distributioner (förhandsversion)

Följande regioner och resurser är tillgängliga för behållargrupper med Windows Server 2019-baserad behållare (förhandsversion).

| Location | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Sydostasien, Nordeuropa, Västeuropa, centrala USA, östra USA, västra USA, västra USA 2 | Windows | 4 | 16 |
| USA, östra 2 | Windows | 2 | 3.5 |


## <a name="availability---virtual-network-deployment-preview"></a>Tillgänglighet – Virtual network-distributionen (förhandsversion)

Följande regioner och resurser är tillgängliga för en behållargrupp som distribueras i en [Azure-nätverk](container-instances-vnet.md) (förhandsversion).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Tillgänglighet – GPU-resurser (förhandsgranskning)

Följande regioner och resurser är tillgängliga för en behållargrupp som distribueras med [GPU resurser](container-instances-gpu.md) (förhandsversion).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Nästa steg

Informera teamet veta om du vill se ytterligare regioner eller ökad resurstillgänglighet på [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Information om felsökning av distribution av behållarinstanser finns i [felsöka distributionsproblem med Azure Container Instances](container-instances-troubleshooting.md).
