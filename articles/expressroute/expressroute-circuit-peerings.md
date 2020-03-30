---
title: 'Azure ExpressRoute: kretsar och peering'
description: Den här sidan innehåller en översikt över ExpressRoute-kretsar och routningsdomäner/peering.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281358"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-kretsar och peering

ExpressRoute-kretsar ansluter din lokala infrastruktur till Microsoft via en anslutningsleverantör. Den här artikeln hjälper dig att förstå ExpressRoute-kretsar och routningsdomäner/peering. Följande bild visar en logisk representation av anslutningen mellan WAN och Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Offentlig Azure-peering har inaktuellt och är inte tillgängligt för nya ExpressRoute-kretsar. Nya kretsar stöder Microsoft peering och privat peering.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>ExpressRoute-kretsar

En ExpressRoute-krets representerar en logisk anslutning mellan din lokala infrastruktur och Microsofts molntjänster via en anslutningsleverantör. Du kan beställa flera ExpressRoute-kretsar. Varje krets kan vara i samma eller olika regioner och kan anslutas till dina lokaler via olika anslutningsleverantörer.

ExpressRoute-kretsar mappas inte till några fysiska entiteter. En krets identifieras unikt av en standard GUID som kallas som en tjänstnyckel (s-nyckel). Tjänstnyckeln är den enda information som utbyts mellan Microsoft, anslutningsleverantören och dig. S-key är inte en hemlighet av säkerhetsskäl. Det finns en 1:1 mappning mellan en ExpressRoute-krets och s-tangenten.

Nya ExpressRoute-kretsar kan innehålla två oberoende peerings: Privat peering och Microsoft-peering. Medan befintliga ExpressRoute-kretsar kan innehålla tre peerings: Azure Public, Azure Private och Microsoft. Varje peering är ett par oberoende BGP-sessioner, var och en av dem konfigureras redundant för hög tillgänglighet. Det finns en 1:N (1 <= N <= 3) mappning mellan en ExpressRoute-krets och routningsdomäner. En ExpressRoute-krets kan ha en, två eller alla tre peerings aktiverade per ExpressRoute-krets.

Varje krets har en fast bandbredd (50 Mbit/s, 100 Mbit/s, 200 Mbit/s, 500 Mbit/s, 1 Gbit/s, 10 Gbit/s) och mappas till en anslutningsleverantör och en peering-plats. Den bandbredd du väljer delas över alla krets peerings

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Kvoter, begränsningar och begränsningar

Standardkvoter och standardgränser gäller för varje ExpressRoute-krets. Se sidan [Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md) för aktuell information om kvoter.

## <a name="expressroute-peering"></a><a name="routingdomains"></a>ExpressRoute peering

En ExpressRoute-krets har flera routningsdomäner/peerings associerade med sig: Azure public, Azure private och Microsoft. Varje peering konfigureras identiskt på ett par routrar (i aktiv-aktiv eller belastningsdelningskonfiguration) för hög tillgänglighet. Azure-tjänster kategoriseras som *offentliga Azure-* och *Azure-tjänster* för att representera IP-adresseringsscheman.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Azures privata peering

Azure-beräkningstjänster, nämligen virtuella datorer (IaaS) och molntjänster (PaaS), som distribueras inom ett virtuellt nätverk kan anslutas via den privata peering-domänen. Den privata peering-domänen anses vara ett betrott tillägg av ditt kärnnätverk till Microsoft Azure. Du kan ställa in dubbelriktad anslutning mellan kärnnätverket och virtuella Azure-nätverk (Virtuella nätverk). Med den här peering-datorn kan du ansluta till virtuella datorer och molntjänster direkt på deras privata IP-adresser.  

Du kan ansluta mer än ett virtuellt nätverk till den privata peering-domänen. Gå igenom [faq-sidan](expressroute-faqs.md) för information om begränsningar och begränsningar. Du kan besöka sidan [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md) för aktuell information om begränsningar.  På sidan [Routning](expressroute-routing.md) finns detaljerad information om routningskonfigurationen.

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Microsoft-peering

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Anslutning till Microsofts onlinetjänster (Office 365- och Azure PaaS-tjänster) sker via Microsoft-peering. Vi aktiverar dubbelriktad anslutning mellan dina WAN- och Microsoft-molntjänster via Microsofts peering-routningsdomän. Du får endast ansluta till Microsofts molntjänster via offentliga IP-adresser som ägs av dig eller din anslutningsleverantör och du måste följa alla definierade regler. Mer information finns på sidan [ExpressRoute-krav.](expressroute-prerequisites.md)

Se [sidan Vanliga frågor och svar](expressroute-faqs.md) om mer information om tjänster som stöds, kostnader och konfigurationsinformation. Se sidan [ExpressRoute-platser](expressroute-locations.md) för information om listan över anslutningsleverantörer som erbjuder Microsoft-peering-stöd.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Jämförelse av peering

I följande tabell jämförs de tre peerings:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Du kan aktivera en eller flera routningsdomäner som en del av expressroutekretsen. Du kan välja att låta alla routningsdomäner placeras på samma VPN om du vill kombinera dem till en enda routningsdomän. Du kan också placera dem på olika routningsdomäner, liknande diagrammet. Den rekommenderade konfigurationen är att privat peering är ansluten direkt till kärnnätverket och de offentliga och Microsoft-peering-länkarna är anslutna till din DMZ.

Varje peering kräver separata BGP-sessioner (ett par för varje peering-typ). BGP-sessionsparen ger en länk med hög tillgänglig tillgång. Om du ansluter via anslutningsleverantörer för lager 2 ansvarar du för att konfigurera och hantera routning. Du kan läsa mer genom att granska [arbetsflödena](expressroute-workflows.md) för att konfigurera ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>ExpressRoute-hälsa

ExpressRoute-kretsar kan övervakas med hjälp av tillgänglighet, anslutning till virtuella nätverk och bandbreddsanvändning med [NPM (Network Performance Monitor).](https://docs.microsoft.com/azure/networking/network-monitoring-overview)

NPM övervakar hälsotillståndet för Azure-privat peering och Microsoft-peering. Kolla in vårt [inlägg](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) för mer information.

## <a name="next-steps"></a>Nästa steg

* Hitta en tjänstleverantör. Se [ExpressRoute-tjänsteleverantörer och -platser](expressroute-locations.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* Konfigurera ExpressRoute-anslutningen.
  * [Skapa och hantera ExpressRoute-kretsar](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurera routning (peering) för ExpressRoute-kretsar](expressroute-howto-routing-portal-resource-manager.md)
