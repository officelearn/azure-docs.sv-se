---
title: Resurstillgänglighet per region
description: Tillgänglighet för beräknings- och minnesresurser för Azure Container Instances-tjänsten i olika Azure-regioner.
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: c84ab7833f7c90d5d4b3c340c268fd9f75e20da9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399406"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Resurstillgänglighet för Azure Container Instances i Azure-regioner

I den här artikeln beskrivs tillgängligheten för Azure Container Instances-beräknings-, minnes- och lagringsresurser i Azure-regioner och efter måloperativsystem. 

Värden som presenteras är de maximala resurser som är tillgängliga per distribution av en [behållargrupp](container-instances-container-groups.md). Värden är aktuella vid tidpunkten för publiceringen. 

> [!NOTE]
> Behållargrupper som skapas inom dessa resursgränser är beroende av tillgänglighet inom distributionsregionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser. Om du vill minska ett sådant distributionsfel kan du prova att distribuera instanser med lägre resursinställningar eller prova distributionen vid ett senare tillfälle eller i en annan region med tillgängliga resurser.

Information om kvoter och andra begränsningar i distributionerna finns i [Kvoter och begränsningar för Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Tillgänglighet - Allmänt

Följande regioner och maximala resurser är tillgängliga för behållargrupper med Linux och Windows Server 2016-baserade behållare som [stöds.](container-instances-faq.md#what-windows-base-os-images-are-supported)

| Regioner | Operativsystem | Maximal CPU | Maximalt minne (GB) | Lagring (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brasilien Södra, Kanada Central, Centrala Indien, Centrala USA, Östasien, Östra USA, Östra USA 2, Norra Europa, Södra centrala USA, Sydostasien, Sydindiska, Södra Indien, Storbritannien Syd- och Västeuropa, Västra USA, Västra USA 2 | Linux | 4 | 16 | 50 |
| Östra Australien, Östra Japan | Linux | 2 | 8 | 50 |
| USA, norra centrala | Linux | 2 | 3.5 | 50 |
| Södra Brasilien, Östra Japan, Västeuropa | Windows | 4 | 16 | 20 |
| Östra USA, västra USA | Windows | 4 | 14 | 20 |
| Östra Australien, Kanada Central, Centrala Indien, Centrala USA, Östasien, Östra USA 2, Norra centrala USA, Norra Europa, Södra centrala USA, Sydostasien, Sydindiska, Södra Indien, Storbritannien Syd, Västra USA 2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Tillgänglighet – Windows Server 2019 LTSC, 1809-distributioner (förhandsversion)

Följande regioner och maximala resurser är tillgängliga för behållargrupper med Windows Server 2019-baserade behållare (förhandsversion).

| Regioner | Operativsystem | Maximal CPU | Maximalt minne (GB) | Lagring (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Östra Australien, Södra Brasilien, Centrala centrala Kanada, Centrala indien, Centrala USA, Östasien, Östra USA, Östra Japan, Norra centrala USA, Norra Europa, Södra centrala USA, Sydostasien, Södra Indien, Storbritannien Syd-, Västeuropa | Windows | 4 | 16 | 20 |
| Östra USA 2, Västra USA 2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Tillgänglighet - Distribution av virtuella nätverk

Följande regioner och maximala resurser är tillgängliga för en behållargrupp som distribueras i ett [virtuellt Azure-nätverk](container-instances-vnet.md).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Tillgänglighet - GPU-resurser (förhandsgranskning)

Följande regioner och maximala resurser är tillgängliga för en behållargrupp som distribueras med [GPU-resurser](container-instances-gpu.md) (förhandsversion).

> [!IMPORTANT]
> GPU-resurser är endast tillgängliga på begäran. Om du vill begära åtkomst till GPU-resurser skickar du en [Azure-supportbegäran][azure-support].

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Nästa steg

Meddela teamet om du vill se ytterligare regioner eller ökad resurstillgänglighet på [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Information om felsökning av distribution av behållarinstans finns [i Felsöka distributionsproblem med Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
