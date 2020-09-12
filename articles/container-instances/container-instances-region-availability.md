---
title: Resurs tillgänglighet per region
description: Tillgänglighet för beräknings-och minnes resurser för tjänsten Azure Container Instances i olika Azure-regioner.
ms.topic: article
ms.date: 04/27/2020
ms.openlocfilehash: 97baa5199a1803bd967c0b55c846908ea5a2ddcf
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565437"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Resurs tillgänglighet för Azure Container Instances i Azure-regioner

Den här artikeln innehåller information om tillgängligheten för Azure Container Instances beräknings-, minnes-och lagrings resurser i Azure-regioner och efter mål operativ system. 

Värden som anges är de maximala tillgängliga resurserna per distribution av en [behållar grupp](container-instances-container-groups.md). Värden är aktuella vid tidpunkten för publiceringen. 

> [!NOTE]
> Behållar grupper som skapas inom dessa resurs gränser är beroende av tillgängligheten i distributions regionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser. Om du vill undvika ett sådant distributions haveri försök att distribuera instanser med lägre resurs inställningar, eller försök att distribuera vid ett senare tillfälle eller i en annan region med tillgängliga resurser.

Information om kvoter och andra begränsningar i dina distributioner finns i [kvoter och gränser för Azure Container instances](container-instances-quotas.md).

## <a name="availability---general"></a>Tillgänglighet – allmänt

Följande regioner och maximalt antal resurser är tillgängliga för behållar grupper med Linux och [stödda](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016-baserade behållare.

| Regioner | Operativsystem | Maximal CPU | Högsta mängd minne (GB) | Lagring (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Södra Brasilien, centrala Kanada, centrala Indien, centrala USA, Asien, östra, östra USA, östra USA 2, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Västeuropa, västra USA, västra USA 2 | Linux | 4 | 16 | 50 |
| Östra Australien, Östra Japan | Linux | 2 | 8 | 50 |
| USA, norra centrala | Linux | 2 | 3.5 | 50 |
| Södra Brasilien, Östra Japan, västra Europa | Windows | 4 | 16 | 20 |
| Östra USA, västra USA | Windows | 4 | 14 | 20 |
| Östra Australien, centrala Kanada, centrala Indien, centrala USA, Asien, östra, östra USA 2, norra centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, västra USA 2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Tillgänglighet – Windows Server 2019 LTSC, 1809 distributioner (för hands version)

Följande regioner och maximalt antal resurser är tillgängliga för behållar grupper med Windows Server 2019-baserade behållare (för hands version).

| Regioner | Operativsystem | Maximal CPU | Högsta mängd minne (GB) | Lagring (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Östra Australien, södra Brasilien, centrala Kanada, centrala Indien, centrala USA, Asien, östra, östra USA, östra USA, norra centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Västeuropa | Windows | 4 | 16 | 20 |
| USA, östra 2, västra USA 2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Tillgänglighet – virtuell nätverks distribution

Följande regioner och maximalt antal resurser är tillgängliga för en behållar grupp som distribueras i ett [virtuellt Azure-nätverk](container-instances-vnet.md).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Tillgänglighet – GPU-resurser (förhands granskning)

Följande regioner och maximalt antal resurser är tillgängliga för en behållar grupp som distribueras med [GPU-resurser](container-instances-gpu.md) (för hands version).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Nästa steg

Låt teamet veta om du vill se fler regioner eller ökad resurs tillgänglighet på [aka.MS/ACI/feedback](https://aka.ms/aci/feedback).

Information om hur du felsöker distribution av container instans finns i [Felsöka distributions problem med Azure Container instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
