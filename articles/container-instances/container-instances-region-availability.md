---
title: Resurs tillgänglighet per region
description: Tillgänglighet för beräknings-och minnes resurser för tjänsten Azure Container Instances i olika Azure-regioner.
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: aef66a9fdbe73ccd4da79ce972b7beb061e9fe35
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533474"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Resurs tillgänglighet för Azure Container Instances i Azure-regioner

Den här artikeln innehåller information om tillgängligheten för Azure Container Instances beräknings-och minnes resurser i Azure-regioner. 

Värden som anges är de maximala tillgängliga resurserna per distribution av en [behållar grupp](container-instances-container-groups.md). Värden är aktuella vid tidpunkten för publiceringen. 

> [!NOTE]
> Behållar grupper som skapas inom dessa resurs gränser är beroende av tillgängligheten i distributions regionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser. Försök att distribuera instanser med lägre resurs inställningar eller försök att distribuera vid ett senare tillfälle för att undvika ett sådant distributions haveri.

Information om kvoter och andra begränsningar i dina distributioner finns i [kvoter och gränser för Azure Container instances](container-instances-quotas.md).

## <a name="availability---general"></a>Tillgänglighet – allmänt

Följande regioner och resurser är tillgängliga för behållar grupper med Linux och [stödda](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016-baserade behållare.

| Plats | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Centrala Kanada, centrala Indien, centrala USA, Asien, östra, östra USA, östra USA 2, norra Europa, södra centrala USA, Sydostasien, Storbritannien, södra, västra USA | Linux | 4 | 16 |
| Västeuropa, västra USA 2 | Linux | 4 | 14 |
| Östra Australien, Östra Japan | Linux | 2 | 8 |
| Norra centrala USA, södra Indien | Linux | 2 | 3.5 |
| Västra Europa | Windows | 4 | 16 |
| Östra USA, västra USA | Windows | 4 | 14 |
| Östra Australien, centrala Kanada, centrala Indien, centrala USA, Asien, östra, östra USA 2, Östra Japan, norra centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, västra USA 2 | Windows | 2 | 3.5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Tillgänglighet – Windows Server 2019 LTSC, 1809 distributioner (för hands version)

Följande regioner och resurser är tillgängliga för behållar grupper med Windows Server 2019-baserade behållare (för hands version).

| Plats | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Sydostasien Europa, norra Europa, Västeuropa, centrala USA, östra USA, västra USA, västra USA 2 | Windows | 4 | 16 |
| USA, östra 2 | Windows | 2 | 3.5 |


## <a name="availability---virtual-network-deployment-preview"></a>Tillgänglighet – distribution av virtuella nätverk (för hands version)

Följande regioner och resurser är tillgängliga för en behållar grupp som distribueras i ett [virtuellt Azure-nätverk](container-instances-vnet.md) (för hands version).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Tillgänglighet – GPU-resurser (förhands granskning)

Följande regioner och resurser är tillgängliga för en behållar grupp som distribueras med [GPU-resurser](container-instances-gpu.md) (för hands version).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Nästa steg

Låt teamet veta om du vill se fler regioner eller ökad resurs tillgänglighet på [aka.MS/ACI/feedback](https://aka.ms/aci/feedback).

Information om hur du felsöker distribution av container instans finns i [Felsöka distributions problem med Azure Container instances](container-instances-troubleshooting.md).
