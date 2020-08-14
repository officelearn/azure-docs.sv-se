---
title: Koncept – nätverks anslutning
description: Lär dig mer om viktiga aspekter och användnings fall för nätverk och interanslutning i Azure VMware-lösning (AVS)
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 6f1f1f5a089781f1f7e882c9c8692f0c845ae485
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214104"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware Solution (AVS) – begrepp för förhandsversionsnätverk och sammankoppling

Nätverks anslutning mellan dina privata moln i Azure VMware-lösningen (AVS) och lokala miljöer eller virtuella nätverk i Azure ger dig åtkomst till och använder ditt privata moln. I den här artikeln tar vi upp några viktiga begrepp som upprättar grunden för nätverk och anslutningar.

Ett användbart perspektiv på anslutning är att ta hänsyn till de två typerna av molnets privata moln implementeringar:

1. [**Grundläggande Azure-anslutningar med enbart Azure**](#azure-virtual-network-interconnectivity) kan du hantera och använda ditt privata moln med bara ett enda virtuellt nätverk i Azure. Den här implementeringen passar bäst för AVS-utvärderingar eller implementeringar som inte kräver åtkomst från lokala miljöer.

1. En [**fullständig lokal till privat moln anslutning**](#on-premises-interconnectivity) utökar den grundläggande Azure-bara implementeringen för att omfatta samanslutning mellan lokala och AVS-privata moln.
 
Du hittar mer information om kraven och de två typerna av moln-och samkörnings implementeringar i AVS-moln som beskrivs i avsnitten nedan.

## <a name="avs-private-cloud-use-cases"></a>Användnings fall för molnets privata moln

Användnings fallen för privata AVS-moln är:
- nya virtuella VMware-arbetsbelastningar i molnet
- VM-arbetsbelastning till molnet (endast lokalt till AVS)
- Migrering av VM-arbetsbelastning till molnet (endast lokalt till AVS)
- haveri beredskap (AVS to AVS eller lokalt till AVS)
- användning av Azure-tjänster

 Alla användnings fall för AVS-tjänsten är aktiverade med lokal anslutning till privat moln. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>Krav för virtuella nätverks-och ExpressRoute-kretsar
 
När du skapar en anslutning från ett virtuellt nätverk i din prenumeration upprättas ExpressRoute-kretsen genom peering, använder en autentiseringsnyckel och ett peering-ID som du begär i Azure Portal. Peering är en privat, en-till-en-anslutning mellan ditt privata moln och det virtuella nätverket.

> [!NOTE] 
> ExpressRoute-kretsen ingår inte i en distribution av privata moln. Den lokala ExpressRoute-kretsen ligger utanför det här dokumentets omfattning. Om du behöver lokal anslutning till ditt privata moln kan du använda en av dina befintliga ExpressRoute-kretsar eller köpa ett i Azure Portal.

När du distribuerar ett privat moln får du IP-adresser för vCenter och NSX-T Manager. För att få åtkomst till dessa hanterings gränssnitt måste du skapa ytterligare resurser i ett virtuellt nätverk i din prenumeration. Du kan hitta procedurerna för att skapa dessa resurser och upprätta ExpressRoute privata peering i självstudierna.

Det privata molnet logiska nätverk levereras med företablerade NSX-T. En gateway på nivå 0 och nivå 1 har fördefinierats för dig. Du kan skapa ett segment och koppla det till den befintliga nivån-1-gatewayen eller koppla den till en ny nivå 1-gateway som du definierar. NSX-T-T logiska nätverks komponenter ger sydöstra anslutningar mellan arbets belastningar och ger även Nord-syd-anslutning till Internet och Azure-tjänster. 

## <a name="routing-and-subnet-requirements"></a>Krav för Routning och undernät

Routning är Border Gateway Protocol (BGP) baserat, som automatiskt tillhandahålls och aktive ras som standard för varje privat moln distribution. För moln privata moln måste du planera privata moln nätverks adress utrymmen med minst/22-prefixlängden för CIDR-nätverks adress block för undernät, som visas i tabellen nedan. Adress blocket får inte överlappa adress block som används i andra virtuella nätverk som finns i din prenumeration och lokala nätverk. I det här adress blocket tillhandahålls hantering, etablering och vMotion nätverk automatiskt.

Exempel på `/22` CIDR-nätverks adress block:  `10.10.0.0/22`

Under näten:

| Nätverksanvändning             | Undernät | Exempel        |
| ------------------------- | ------ | -------------- |
| Hantering av privata moln  | `/24`  | `10.10.0.0/24` |
| vMotion-nätverk           | `/24`  | `10.10.1.0/24` |
| Arbetsbelastningar för virtuella datorer              | `/24`  | `10.10.2.0/24` |
| ExpressRoute-peering      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Anslutning mellan virtuella Azure-nätverk

I det virtuella nätverket för implementering av privata moln kan du hantera ditt moln privata moln, använda arbets belastningar i ditt privata moln och få åtkomst till Azure-tjänster via ExpressRoute-anslutningen. 

Diagrammet nedan visar den grundläggande nätverks anslutningen som upprättats vid tidpunkten för en distribution av privata moln. Det visar det logiska, ExpressRoute-baserade nätverket mellan ett virtuellt nätverk i Azure och ett privat moln. Den här anslutningen uppfyller tre av de främsta användnings fallen:
* Inkommande åtkomst till vCenter Server och NSX-T-hanterare som är tillgängliga från virtuella datorer i din Azure-prenumeration och inte från dina lokala system. 
* Utgående åtkomst från virtuella datorer till Azure-tjänster. 
* Inkommande åtkomst och konsumtion av arbets belastningar som kör ett privat moln.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Grundläggande virtuellt nätverk för anslutning till privat moln" border="false":::

## <a name="on-premises-interconnectivity"></a>Lokal anslutning

I det virtuella nätverket och lokalt för att få fullständig privat moln implementering kan du komma åt dina moln privata moln från lokala miljöer. Den här implementeringen är en utökning av den grundläggande implementering som beskrivs i föregående avsnitt. Precis som den grundläggande implementeringen krävs en ExpressRoute-krets, men med den här implementeringen används den för att ansluta från lokala miljöer till ditt privata moln i Azure. 

I diagrammet nedan visas samanslutningen mellan lokala och privata moln, vilket möjliggör följande användnings fall:
* Hett/kall över-vCenter-vMotion
* Åtkomst för hantering av privata moln lokalt till AVS

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuella nätverk och lokala anslutningar med fullständig privat moln" border="false":::

För fullständig anslutning till ditt privata moln aktiverar du ExpressRoute Global Reach och begär sedan en auktoriseringspost och ett privat peering-ID för Global Reach i Azure Portal. Auktoriseringsregeln och peering-ID används för att etablera Global Reach mellan en ExpressRoute-krets i din prenumeration och ExpressRoute-kretsen för ditt nya privata moln. När de två ExpressRoute kretsarna dirigerar nätverks trafiken mellan dina lokala miljöer till ditt privata moln.  Se [självstudien för att skapa en ExpressRoute Global Reach peering till ett privat moln](tutorial-expressroute-global-reach-private-cloud.md) för procedurerna för att begära och använda auktoriseringsprincipen och PEERING-ID.


## <a name="next-steps"></a>Nästa steg 

Nästa steg är att lära dig om [privata moln lagrings koncept](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
