---
title: 'Azure-ExpressRoute: kretsar och peering'
description: Den här sidan innehåller en översikt över ExpressRoute-kretsar och routning domäner/peering.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356597"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-kretsar och peering

ExpressRoute-kretsar ansluter din lokala infrastruktur till Microsoft via en anslutningsleverantör. Den här artikeln hjälper dig att förstå ExpressRoute-kretsar och routning domäner/peering. Följande bild visar en logisk representation av anslutningen mellan ditt WAN-nätverk och Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Azures offentliga peering är inaktuell och är inte tillgänglig för nya ExpressRoute-kretsar. Nya kretsar stöder Microsoft-peering och privat peering.  
>

## <a name="circuits"></a>ExpressRoute-kretsar

En ExpressRoute-krets representerar en logisk anslutning mellan din lokala infrastruktur och Microsofts molntjänster via en anslutningsleverantör. Du kan beställa flera ExpressRoute-kretsar. Varje krets kan finnas i samma eller olika regioner och kan vara ansluten till ditt lokala nätverk via olika anslutningsleverantörer.

ExpressRoute-kretsar mappas inte till alla fysiska enheter. En krets identifieras unikt genom en standard som GUID kallas för en Tjänstnyckel (s-nyckel). Tjänstnyckeln är enda information som utbyts mellan Microsoft anslutningsleverantören och du. S-nyckeln är inte en hemlighet av säkerhetsskäl. Det finns en 1:1-mappning mellan en ExpressRoute-krets och s-nyckel.

Nya ExpressRoute-kretsar kan innehålla två oberoende peerings: privat peering och Microsofts peering. Medan befintliga ExpressRoute-kretsar kan innehålla tre peerings: Azure offentliga, privata Azure och Microsoft. Varje peering är två oberoende BGP-sessioner, var och en av dem redundant konfigureras för hög tillgänglighet. Det finns en 1: n (1 < = N < = 3) mappning mellan en ExpressRoute-krets och routning domäner. En ExpressRoute-krets kan ha en, två eller alla tre peerings aktiveras per ExpressRoute-krets.

Varje krets har en fast bandbredd (50 Mbit/s, 100 Mbit/s, 200 Mbit/s, 500 Mbit/s, 1 Gbit/s, 10 Gbit/s) och mappas till en anslutningsprovider och en peering-plats. Den bandbredd som du väljer delas mellan alla krets peer-kopplingar

### <a name="quotas"></a>Kvoter, gränser och begränsningar

Standardkvoter och begränsningar gäller för varje ExpressRoute-krets. Se sidan för [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md) för uppdaterad information om kvoter.

## <a name="routingdomains"></a>ExpressRoute-peering

En ExpressRoute-krets innehåller flera routning domäner/peerkopplingar kopplade till den: Azure offentliga, Azures privata och Microsoft. Varje peering har konfigurerats identiskt på två routrar (i aktiv-aktiv eller dela belastningen konfiguration) för hög tillgänglighet. Azure-tjänster kategoriseras som *offentliga Azure* -tjänster och *Azure Private* för att representera IP-adressering-scheman.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Azure privat peering

Azure-Beräkningstjänster, nämligen virtuella datorer (IaaS) och molntjänster (PaaS), som distribueras inom ett virtuellt nätverk kan anslutas via privata peering domänen. Privat peering domänen anses vara en betrodd utökning av ditt kärnnätverk i Microsoft Azure. Du kan ställa in dubbelriktad anslutning mellan ditt core nätverk och Azure-nätverk (Vnet). Denna peering kan du ansluta till virtuella datorer och molntjänster direkt på sina privata IP-adresser.  

Du kan ansluta flera virtuella nätverk till privat peering domänen. På [sidan med vanliga frågor och svar](expressroute-faqs.md) hittar du information om begränsningar och begränsningar. Du kan gå till sidan för [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md) för uppdaterad information om begränsningar.  På sidan [routning](expressroute-routing.md) hittar du detaljerad information om konfiguration av routning.

### <a name="microsoftpeering"></a>Microsoft-peering

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Anslutning till Microsoft onlinetjänster (Office 365 och Azure PaaS Services) sker via Microsoft-peering. Vi Aktivera dubbelriktad anslutning mellan ditt WAN-nätverk och Microsofts molntjänster via Microsoft peering routningsdomän. Du måste ansluta till Microsofts molntjänster endast med offentliga IP-adresser som ägs av dig eller din anslutningsleverantör och du måste följa alla definierade regler. Mer information finns på sidan med [ExpressRoute-krav](expressroute-prerequisites.md) .

På [sidan med vanliga frågor och svar](expressroute-faqs.md) finns mer information om tjänster som stöds, kostnader och konfigurations information. På sidan [ExpressRoute locations](expressroute-locations.md) hittar du information om listan över anslutnings leverantörer som erbjuder stöd för Microsoft-peering.

## <a name="peeringcompare"></a>Jämförelse av peering

I följande tabell jämförs de tre peerings:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Du kan aktivera en eller flera av routningsdomänerna som en del av ExpressRoute-kretsen. Du kan välja att ha alla routningsdomäner placera samma VPN-anslutning om du vill kombinera dem i en enda routningsdomän. Du kan också publicera dem på olika routningsdomäner liknar diagrammet. Den rekommenderade konfigurationen är att privat peering är ansluten direkt till Kärnnätverket och offentliga och Microsoft peering-länkar som är anslutna till din DMZ.

Varje peering kräver separat BGP-sessioner (ett par för varje typ av peering). BGP-sessionen par tillhandahåller en högtillgänglig länk. Om du ansluter via layer 2-anslutningsleverantörer, ansvarar du för att konfigurera och hantera routning. Du kan lära dig mer genom att granska [arbets flöden](expressroute-workflows.md) för att konfigurera ExpressRoute.

## <a name="health"></a>ExpressRoute-hälsa

ExpressRoute-kretsar kan övervakas för tillgänglighet, anslutning till virtuella nätverk och bandbredds användning med [övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM övervakar hälsotillståndet för Azures privata peering och Microsofts peering. Se vår [post](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) för mer information.

## <a name="next-steps"></a>Nästa steg

* Hitta en tjänstleverantör. Se [ExpressRoute-tjänst leverantörer och platser](expressroute-locations.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* Konfigurera ExpressRoute-anslutningen.
  * [Skapa och hantera ExpressRoute-kretsar](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurera routning (peering) för ExpressRoute-kretsar](expressroute-howto-routing-portal-resource-manager.md)
