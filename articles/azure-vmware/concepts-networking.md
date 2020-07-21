---
title: Koncept – nätverks anslutning
description: Lär dig mer om viktiga aspekter och användnings fall för nätverk och interanslutning i Azure VMware-lösning (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 2378ad56e2754b2d2fde7f895f6673e7d7d561c9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539150"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware-lösning (AVS) för hands version av nätverk och interanslutnings koncept

Nätverks anslutning mellan dina privata moln i Azure VMware-lösningen (AVS) och lokala miljöer eller virtuella nätverk i Azure ger dig åtkomst till och användning av ditt privata moln. Några viktiga nätverks-och samanslutnings begrepp som upprättar grunden för en anslutning beskrivs i den här artikeln.

Ett användbart perspektiv på interaktivitet är att ta hänsyn till de två typerna av molnets privata moln implementeringar: implementeringar med grundläggande Azure-endast anslutningar och implementeringar med fullständig lokal till privat moln anslutning.

Användnings fallen för privata AVS-moln är:
- nya virtuella VMware-arbetsbelastningar i molnet
- VM-belastnings överföring till molnet
- Migrering av VM-migrering till molnet
- haveri beredskap
- användning av Azure-tjänster

 Alla användnings fall för AVS-tjänsten är aktiverade med lokal anslutning till privat moln. Den grundläggande modellen för samanslutning passar bäst för AVS-utvärderingar eller implementeringar som inte kräver åtkomst från lokala miljöer.

De två typerna av molnets privata moln anslutningar beskrivs i avsnitten nedan.  Den mest grundläggande anslutningen är "Azure Virtual Network-anslutning"; Det gör att du kan hantera och använda ditt privata moln med bara ett enda virtuellt nätverk i Azure. Den interanslutning som beskrivs i "lokal anslutning" utökar den virtuella nätverks anslutningen för att även omfatta samanslutningar mellan lokala miljöer och AVS-privata moln.

## <a name="azure-virtual-network-interconnectivity"></a>Anslutning mellan virtuella Azure-nätverk

Den grundläggande nätverks anslutningen som fastställs vid tidpunkten för en privat moln distribution visas i diagrammet nedan. Det visar det logiska, ExpressRoute-baserade nätverket mellan ett virtuellt nätverk i Azure och ett privat moln. Den här anslutningen uppfyller tre av de främsta användnings fallen:
- Inkommande åtkomst till hanterings nätverk där vCenter Server och NSX-T Manager finns.
    - Tillgängliga från virtuella datorer i din Azure-prenumeration, inte från dina lokala system.
- Utgående åtkomst från virtuella datorer till Azure-tjänster.
- Inkommande åtkomst och konsumtion av arbets belastningar som kör ett privat moln.

![Grundläggande anslutningar mellan virtuella nätverk och privat moln](./media/concepts/adjacency-overview-drawing-single.png)

ExpressRoute-kretsen i det här scenariot för det virtuella nätverket till ett privat moln upprättas när du skapar en anslutning från ett virtuellt nätverk i din prenumeration till ExpressRoute-kretsen i ditt privata moln. Peering använder en auktoriseringskod och ett krets-ID som du begär i Azure Portal. ExpressRoute-anslutningen som upprättas via peering är en privat, en-till-en-anslutning mellan ditt privata moln och det virtuella nätverket. Du kan hantera ditt privata moln, använda arbets belastningar i ditt privata moln och få åtkomst till Azure-tjänster över den ExpressRoute-anslutningen.

När du distribuerar ett privat AVS-moln krävs ett enskilt/22-adress utrymme för privata nätverk. Det här adress utrymmet får inte överlappa adress utrymmen som används i andra virtuella nätverk i din prenumeration. I det här adress utrymmet tillhandahålls hantering, etablering och vMotion nätverk automatiskt. Routningen är BGP-baserad och tillhandahålls automatiskt och aktive ras som standard för varje privat moln distribution.

När ett privat moln distribueras, får du IP-adresserna för vCenter och NSX-T Manager. För att få åtkomst till dessa hanterings gränssnitt skapar du ytterligare resurser i ett virtuellt nätverk i din prenumeration. Procedurerna för att skapa resurser och upprätta ExpressRoute privata peering finns i självstudierna.

Du utformar det privata molnet för logiska nätverk och implementerar det med NSX-T. Det privata molnet levereras med företablerad NSX-T. En gateway på nivå 0 & nivå 1 Gateway är företablerad för dig. Du kan skapa ett segment och koppla det till den befintliga nivån-1-gatewayen eller ansluta till en ny nivå 1-gateway som du kan definiera. NSX-T-T logiska nätverks komponenter ger sydöstra anslutningar mellan arbets belastningar och ger även Nord-syd-anslutning till Internet och Azure-tjänster. 

## <a name="on-premises-interconnectivity"></a>Lokal anslutning

Du kan också ansluta lokala miljöer till dina moln privata moln. Den här typen av anslutning är ett tillägg till den grundläggande anslutning som beskrivs i föregående avsnitt.

![virtuella nätverk och lokala anslutningar med fullständig privat moln](./media/concepts/adjacency-overview-drawing-double.png)

Om du vill upprätta fullständig anslutning till ett privat moln använder du Azure Portal för att aktivera ExpressRoute Global Reach mellan en privat moln ExpressRoute-krets och en lokal ExpressRoute-krets. Den här konfigurationen utökar den grundläggande anslutningen för att inkludera åtkomst till privata moln från lokala miljöer.

Det krävs en lokal till Azure Virtual Network ExpressRoute-krets för att ansluta från lokala miljöer till ditt privata moln i Azure. Den här ExpressRoute-kretsen finns i din prenumeration och ingår inte i en distribution av privata moln. Den lokala ExpressRoute-kretsen ligger utanför det här dokumentets omfattning. Om du behöver lokal anslutning till ditt privata moln kan du använda en av dina befintliga ExpressRoute-kretsar eller köpa ett i Azure Portal.

När de två ExpressRoute-kretsarna är länkade till Global Reach dirigeras nätverks trafiken mellan dina lokala miljöer och ditt privata moln. Den lokala anslutningen till molnet i det privata molnet visas i föregående diagram. Den sammankopplade anslutningen som representeras i diagrammet möjliggör följande användnings fall:

- Hett/kall över-vCenter-vMotion
- Åtkomst för hantering av privata moln lokalt till AVS

Om du vill aktivera fullständig anslutning kan du begära en nyckel och privat peering-ID för Global Reach i Azure Portal. Du använder nyckel och ID för att upprätta Global Reach mellan en ExpressRoute-krets i din prenumeration och ExpressRoute-kretsen för ditt nya privata moln. [Självstudien för att skapa ett privat moln](tutorial-create-private-cloud.md) ger dig procedurerna för att begära och använda nyckeln och ID: t.

I kraven för routning måste du planera privata moln nätverk adress utrymmen så att du slipper överlappar andra virtuella nätverk och lokala nätverk. AVS-privata moln kräver minst ett `/22` CIDR-adressblock för undernät som visas nedan. Det här nätverket kompletterar dina lokala nätverk. För att kunna ansluta till lokala miljöer och virtuella nätverk måste detta vara ett nätverks adress block som inte överlappar varandra.

Exempel på `/22` CIDR-nätverks adress block:`10.10.0.0/22`

Under näten:

| Nätverksanvändning             | Undernät | Exempel        |
| ------------------------- | ------ | -------------- |
| Hantering av privata moln            | `/24`    | `10.10.0.0/24`   |
| vMotion-nätverk       | `/24`    | `10.10.1.0/24`   |
| Arbetsbelastningar för virtuella datorer | `/24`   | `10.10.2.0/24`   |
| ExpressRoute-peering | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Nästa steg 

Nästa steg är att lära dig om [privata moln lagrings koncept](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
