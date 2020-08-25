---
title: Koncept – nätverks anslutning
description: Lär dig mer om viktiga aspekter och användnings fall för nätverk och anslutningar i Azure VMware-lösningar.
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750563"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Azure VMware-lösning för för hands versions nätverk och interanslutnings koncept

Azure VMware-lösningen (AVS) erbjuder en privat VMware-moln miljö som är tillgänglig för användare och program från lokala och Azure-baserade miljöer eller resurser. Tjänster som Azure ExpressRoute och VPN-anslutningar levererar anslutningen. Dessa tjänster kräver vissa nätverks adress intervall och brand Väggs portar för att aktivera tjänsterna.  

När du distribuerar ett privat moln, skapas privata nätverk för hantering, etablering och vMotion. De används för åtkomst till vCenter och NSX-T Manager och vMotion eller distribution av virtuella datorer. Alla privata nätverk är tillgängliga från ett VNet i Azure eller från lokala miljöer. ExpressRoute Global Reach används för att ansluta privata moln till lokala miljöer och den här anslutningen kräver ett VNet med en ExpressRoute-krets i din prenumeration.

När du distribuerar ett privat moln etablerades dessutom åtkomst till Internet och Azure-tjänster, så att virtuella datorer i produktions nätverk kan använda dem.  Som standard är Internet åtkomst inaktive rad för nya privata moln och kan när som helst aktive ras eller inaktive ras.

Ett användbart perspektiv på anslutning är att ta hänsyn till de två typerna av molnets privata moln implementeringar:

1. [**Grundläggande Azure-anslutningar med enbart Azure**](#azure-virtual-network-interconnectivity) kan du hantera och använda ditt privata moln med bara ett enda virtuellt nätverk i Azure. Den här implementeringen passar bäst för AVS-utvärderingar eller implementeringar som inte kräver åtkomst från lokala miljöer.

1. En [**fullständig lokal till privat moln anslutning**](#on-premises-interconnectivity) utökar den grundläggande Azure-bara implementeringen för att omfatta samanslutning mellan lokala och AVS-privata moln.
 
I den här artikeln tar vi upp några viktiga begrepp som etablerar nätverk och anslutningar, inklusive krav och begränsningar. Vi kommer också att få mer information om de två typerna av samverkande implementeringar av molnets privata moln. Den här artikeln innehåller den information som du behöver känna till för att konfigurera nätverket så att det fungerar med AVS på rätt sätt.

## <a name="avs-private-cloud-use-cases"></a>Användnings fall för molnets privata moln

Användnings fallen för privata AVS-moln är:
- Nya virtuella VMware-arbetsbelastningar i molnet
- VM-arbetsbelastning till molnet (endast lokalt till AVS)
- Migrering av VM-arbetsbelastning till molnet (endast lokalt till AVS)
- Haveri beredskap (AVS to AVS eller lokalt till AVS)
- Användning av Azure-tjänster

> [!TIP]
> Alla användnings fall för AVS-tjänsten är aktiverade med lokal anslutning till privat moln.

## <a name="azure-virtual-network-interconnectivity"></a>Anslutning mellan virtuella Azure-nätverk

I det virtuella nätverket för implementering av privata moln kan du hantera Azure VMware-lösningens privata moln, använda arbets belastningar i ditt privata moln och få åtkomst till Azure-tjänster via ExpressRoute-anslutningen. 

Diagrammet nedan visar den grundläggande nätverks anslutningen som upprättats vid tidpunkten för en distribution av privata moln. Det visar det logiska, ExpressRoute-baserade nätverket mellan ett virtuellt nätverk i Azure och ett privat moln. Den här anslutningen uppfyller tre av de främsta användnings fallen:
* Inkommande åtkomst till vCenter Server och NSX-T-hanterare som är tillgängliga från virtuella datorer i din Azure-prenumeration och inte från dina lokala system. 
* Utgående åtkomst från virtuella datorer till Azure-tjänster. 
* Inkommande åtkomst och konsumtion av arbets belastningar som kör ett privat moln.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Grundläggande virtuellt nätverk för anslutning till privat moln" border="false":::

## <a name="on-premises-interconnectivity"></a>Lokal anslutning

I det virtuella nätverket och lokalt för att få fullständig privat moln implementering kan du komma åt dina privata moln i Azure VMware-lösningen från lokala miljöer. Den här implementeringen är en utökning av den grundläggande implementering som beskrivs i föregående avsnitt. Precis som den grundläggande implementeringen krävs en ExpressRoute-krets, men med den här implementeringen används den för att ansluta från lokala miljöer till ditt privata moln i Azure. 

I diagrammet nedan visas samanslutningen mellan lokala och privata moln, vilket möjliggör följande användnings fall:
* Hett/kall över-vCenter-vMotion
* Lokal åtkomst till Azure VMware-lösning hantering av privata moln

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuella nätverk och lokala anslutningar med fullständig privat moln" border="false":::

För fullständig anslutning till ditt privata moln aktiverar du ExpressRoute Global Reach och begär sedan en auktoriseringspost och ett privat peering-ID för Global Reach i Azure Portal. Auktoriseringsregeln och peering-ID används för att etablera Global Reach mellan en ExpressRoute-krets i din prenumeration och ExpressRoute-kretsen för ditt nya privata moln. När de två ExpressRoute kretsarna dirigerar nätverks trafiken mellan dina lokala miljöer till ditt privata moln.  Se [självstudien för att skapa en ExpressRoute Global Reach peering till ett privat moln](tutorial-expressroute-global-reach-private-cloud.md) för procedurerna för att begära och använda auktoriseringsprincipen och PEERING-ID.

## <a name="next-steps"></a>Nästa steg 

- Lär dig mer om [nätverks anslutningens överväganden och krav](tutorial-network-checklist.md). 
- Lär dig mer om [lagrings koncept för privata moln](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

