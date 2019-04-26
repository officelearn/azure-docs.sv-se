---
title: 'Utöka ditt lokala nätverk till Azure över en privat anslutning – Översikt över ExpressRoute: Azure | Microsoft Docs'
description: Den tekniska översikten över ExpressRoute förklarar hur du kan använda en ExpressRoute-anslutning för att utöka ditt lokala nätverk till Azure över en privat anslutning.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: overview
ms.date: 10/18/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 56baabb95a7873b48a27943a58b8e26c56efb6b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364354"
---
# <a name="expressroute-overview"></a>Översikt över ExpressRoute
Med ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning som tillhandahålls av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.

Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning. ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet. Mer information om hur du ansluter nätverket till Microsoft med ExpressRoute finns [ExpressRoute-anslutningsmodeller](expressroute-connectivity-models.md).

![Översikt över ExpressRoute-anslutning](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Viktiga fördelar

* Layer 3-anslutningen mellan ditt lokala nätverk och Microsoft Cloud via en anslutningsleverantör. Anslutningen kan vara från ett ”any-to-any”-nätverk (IPVPN), en ”point-to-point”-anslutning med Ethernet, eller med en virtuell korsanslutning via ett Ethernet-utbyte.
* Anslutning till Microsofts molntjänster i alla regioner i den geopolitiska regionen.
* Global anslutning till Microsofts tjänster i alla regioner med ExpressRoutes-premiumtillägget.
* Dynamisk routning mellan ditt nätverk och Microsoft via BGP.
* Inbyggd redundans i varje peeringplats för högre tillförlitlighet.
* Anslutningens drifttids-[SLA](https://azure.microsoft.com/support/legal/sla/).
* QoS-stöd för Skype för företag.

Mer information finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

## <a name="features"></a>Funktioner

### <a name="layer-3-connectivity"></a>Layer 3-anslutning
Microsoft använder BGP, branschens standardprotokoll för dynamisk routning för att utbyta routning mellan det lokala nätverket, dina instanser i Azure och Microsofts offentliga adresser. Vi upprättar flera BGP-sessioner med ditt nätverk för olika trafikprofiler. Mer information finns i artikeln [ExpressRoute-krets och routningsdomäner](expressroute-circuit-peerings.md).

### <a name="redundancy"></a>Redundans
Varje ExpressRoute-krets består av två anslutningar till två Microsoft Enterprise-gränsroutrar (MSEE) från anslutningsleverantören/din nätverksgräns. Microsoft kräver en dubbel BGP-anslutning från anslutningsleverantören/din nätverksgräns – en för varje MSEE. Du kan välja att inte distribuera redundanta enheter/Ethernet-kretsar. Dock använder anslutningsleverantörer redundanta enheter för att dina projekt ska lämnas över till Microsoft på ett redundant sätt. En redundant Layer 3-anslutningskonfiguration är ett krav för att vår [SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltig.

### <a name="connectivity-to-microsoft-cloud-services"></a>Anslutning till Microsofts molntjänster
ExpressRoute-anslutningar ger åtkomst till följande tjänster:
* Microsoft Azure-tjänster
* Microsoft Office 365-tjänster
* Microsoft Dynamics 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

En detaljerad lista över de tjänster som stöds via ExpressRoute finns på sidan [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Anslutning till alla regioner inom en geopolitisk region
Du kan ansluta till Microsoft på någon av våra [peeringplatser](expressroute-locations.md) och få åtkomst till regioner inom den geopolitiska regionen.

Om du exempelvis ansluter till Microsoft i Amsterdam via ExpressRoute kommer du ha åtkomst till alla Microsoft-molntjänster som finns i Europa, norra och Europa, västra. En översikt över geopolitiska regioner, tillhörande Microsoft-molnområden och motsvarande ExpressRoute-peeringplatser finns i [ExpressRoute-partners och peeringplatser](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Global anslutning med ExpressRoutes premiumtillägg
Du kan aktivera ExpressRoutes premiumtillägg om du vill utöka anslutningen över geopolitiska gränser. Om du till exempel ansluter till Microsoft i Amsterdam via ExpressRoute får du åtkomst till alla Microsoft-molntjänster som finns i alla regioner över hela världen (med undantag för nationella moln). Du kan komma åt tjänster som distribueras i Sydamerika eller Australien på samma sätt som du har åtkomst till regionerna Europa, norra och Europa, västra.

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Lokal anslutning till flera platser med ExpressRoute Global Reach
Du kan aktivera ExpressRoute Global Reach för att utbyta data på dina olika lokala platser genom att ansluta ExpressRoute-kretsarna. Om du till exempel har ett privat datacenter i Kalifornien som är anslutet till ExpressRoute i Silicon Valley och ett annat privat datacenter anslutet till ExpressRoute i Dallas kan du, med ExpressRoute Global Reach, ansluta dina privata datacenter till varandra genom två ExpressRoute-kretsar. Din trafik över datacenter passerar genom Microsofts nätverk.

Mer information finns i [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Utförligt ekosystem med anslutningspartner
ExpressRoute har ett ständigt växande ekosystem med anslutningsleverantörer och systemintegrerarpartner. Den senaste informationen finns i [ExpressRoute-partner och peeringplatser](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Anslutning till nationella moln
Microsoft använder isolerade molnmiljöer för särskilda geopolitiska regioner och kundsegment. Se sidan [ExpressRoute-partner och peeringplatser](expressroute-locations.md) för en lista med nationella moln och leverantörer.

### <a name="expressroute-direct"></a>ExpressRoute Direct
Med ExpressRoute Direct får kunder möjligheten att ansluta direkt till Microsofts globala nätverk vid peering-platser strategiskt fördelade runt om i världen. ExpressRoute Direct ger dubbel 100 Gbps-anslutning, som stöder aktiv/aktiv-anslutningar skalanpassat.

Viktiga funktioner som ExpressRoute Direct ger är till exempel:

* Stora datainmatningar till tjänster som Storage och Cosmos DB
* Fysisk isolering för reglerade branscher som kräver dedikerade och isolerade anslutningar, till exempel: banker, myndigheter och detaljhandeln
* Detaljerad kontroll över kretsfördelning utifrån affärsenheter

Mer information finns i [Om ExpressRoute Direct](https://go.microsoft.com/fwlink/?linkid=2022973).

### <a name="bandwidth-options"></a>Bandbreddsalternativ
Du kan köpa ExpressRoute-kretsar för en mängd olika bandbredder. Listan med bandbredder som stöds finns nedan. Kontrollera med din anslutningsleverantör för att avgöra vilka bandbredder de stöder.

* 50 Mbit/s
* 100 Mbit/s
* 200 Mbit/s
* 500 Mbit/s
* 1 Gbit/s
* 2 Gbit/s
* 5 Gbit/s
* 10 Gbit/s

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamisk skalning av bandbredd
Du kan öka ExpressRoute-kretsens bandbredd (baserat på bästa prestanda) utan att behöva avbryta dina anslutningar. Mer information finns i [Ändra en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Flexibla faktureringsmodeller
Du kan välja den faktureringsmodell som passar dig bäst. Välj mellan faktureringsmodellerna nedan. Mer information finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

* **Obegränsad data**. Fakturering baseras på en månatlig avgift. All inkommande och utgående dataöverföring ingår utan extra kostnad.
* **Avgiftsbelagda data**. Fakturering baseras på en månatlig avgift. All inkommande dataöverföring är kostnadsfri. Utgående dataöverföring debiteras per GB data som överförs. Dataöverföringskostnader varierar beroende på region.
* **ExpressRoutes premiumtillägg**. ExpressRoutes premium är ett tillägg till ExpressRoute-kretsen. ExpressRoutes premiumtillägg innehåller följande funktioner: 
  * Ökade väggränser för Azures offentliga och privata peering från 4 000 vägar till 10 000 vägar.
  * Global anslutning för tjänster. En ExpressRoute-krets som skapats i en region (exklusive nationella moln) har åtkomst till resurser i alla andra regioner i världen. Till exempel kan ett virtuellt nätverk som skapats i västra Europa nås via en ExpressRoute-krets som etablerats i Silicon Valley.
  * Ökat antal VNet-länkar per ExpressRoute-krets från 10 till en högre gräns, beroende på kretsens bandbredd.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
Vanliga frågor om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [ExpressRoute-anslutningsmodeller](expressroute-connectivity-models.md).
* Läs mer om ExpressRoute-anslutningar och routningsdomäner. Se [ExpressRoute-anslutningar och routningsdomäner](expressroute-circuit-peerings.md).
* Hitta en tjänstleverantör. Se [ExpressRoute-partners och peeringplatser](expressroute-locations.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* Se kraven för [routning](expressroute-routing.md), [NAT](expressroute-nat.md) och [QoS](expressroute-qos.md).
* Konfigurera ExpressRoute-anslutningen.
  * [Skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md)
  * [Skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
  * [Koppla ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)
* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
