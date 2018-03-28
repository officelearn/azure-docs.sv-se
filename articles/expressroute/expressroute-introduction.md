---
title: 'Översikt över ExpressRoute: Utöka ditt lokala nätverk till Azure över en privat anslutning | Microsoft Docs'
description: Den här tekniska översikten över ExpressRoute förklarar hur du kan använda en ExpressRoute-anslutning för att utöka ditt lokala nätverk till Azure över en privat anslutning.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: 75d69669945d31fbc9876d0c1e709b37f96aee6d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="expressroute-overview"></a>Översikt över ExpressRoute
Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.

Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning. ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare och har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet. Mer information om hur du ansluter nätverket till Microsoft med ExpressRoute finns [ExpressRoute-anslutningsmodeller](expressroute-connectivity-models.md).

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Viktiga fördelar

* Layer 3-anslutningen mellan ditt lokala nätverk och Microsoft Cloud via en anslutningsleverantör. Anslutningen kan vara från ett ”any-to-any”-nätverk (IPVPN), en ”point-to-point”-anslutning med Ethernet, eller med en virtuell korsanslutning via ett Ethernet-utbyte.
* Anslutning till Microsofts molntjänster i alla regioner i den geopolitiska regionen.
* Global anslutning till Microsofts tjänster i alla regioner med ExpressRoutes premiumtillägg.
* Dynamisk routning mellan ditt nätverk och Microsoft med branschens standardprotokoll (BGP).
* Inbyggd redundans i varje peeringplats för högre tillförlitlighet.
* Anslutningens drifttids-[SLA](https://azure.microsoft.com/support/legal/sla/).
* QoS-stöd för Skype för företag.

Mer information finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

## <a name="features"></a>Funktioner

### <a name="layer-3-connectivity"></a>Layer 3-anslutning
Microsoft använder branschens standardprotokoll för dynamisk routning (BGP) för att utbyta routning mellan det lokala nätverket, dina instanser i Azure och Microsofts offentliga adresser.  Vi upprättar flera BGP-sessioner med ditt nätverk för olika trafikprofiler. Mer information finns i artikeln [ExpressRoute-krets och routningsdomäner](expressroute-circuit-peerings.md).

### <a name="redundancy"></a>Redundans
Varje ExpressRoute-krets består av två anslutningar till två Microsoft Enterprise-gränsroutrar (MSEE) från anslutningsleverantören/din nätverksgräns. Microsoft kräver en dubbel BGP-anslutning från anslutningsleverantören/din sida – en för varje MSEE. Du kan välja att inte distribuera redundanta enheter/Ethernet-kretsar. Dock använder anslutningsleverantörer redundanta enheter för att dina projekt ska lämnas över till Microsoft på ett redundant sätt. En redundant Layer 3-anslutningskonfiguration är ett krav för att vår [SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltig.

### <a name="connectivity-to-microsoft-cloud-services"></a>Anslutning till Microsofts molntjänster

ExpressRoute-anslutningar ger åtkomst till följande tjänster:

* Microsoft Azure-tjänster
* Microsoft Office 365-tjänster
* Microsoft Dynamics 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

En detaljerad lista över de tjänster som stöds via ExpressRoute finns på sidan [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) 

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Anslutning till alla regioner inom en geopolitisk region
Du kan ansluta till Microsoft på någon av våra [peeringplatser](expressroute-locations.md) och få åtkomst till alla regioner inom den geopolitiska regionen. 

Om du exempelvis har anslutit till Microsoft i Amsterdam via ExpressRoute, kommer du ha åtkomst till alla Microsoft-molntjänster som finns i norra Europa och västra Europa. En översikt över geopolitiska regioner, tillhörande Microsoft-molnområden och motsvarande ExpressRoute-peeringplatser finns i [ExpressRoute-partners och peeringplatser](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Global anslutning med ExpressRoutes premiumtillägg
Du kan aktivera ExpressRoutes premiumtillägg om du vill utöka anslutningen över geopolitiska gränser. Om du till exempel är ansluten till Microsoft i Amsterdam via ExpressRoute, får du åtkomst till alla Microsoft-molntjänster som finns i alla regioner över hela världen (med undantag för nationella moln). Du kan komma åt tjänster som distribueras i Sydamerika eller Australien på samma sätt som du har åtkomst till de norra och västra Europaregionerna.

### <a name="rich-connectivity-partner-ecosystem"></a>Utförligt ekosystem med anslutningspartner
ExpressRoute har ett ständigt växande ekosystem med anslutningsleverantörer och SI-partners. Den senaste informationen hittar du i artikeln [ExpressRoute-leverantörer och platser](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Anslutning till nationella moln
Microsoft använder isolerade molnmiljöer för särskilda geopolitiska regioner och kundsegment. Se artikeln [ExpressRoute-leverantörer och platser](expressroute-locations.md) för en lista med nationella moln och leverantörer.

### <a name="bandwidth-options"></a>Bandbreddsalternativ
Du kan köpa ExpressRoute-kretsar för en mängd olika bandbredder. Listan med bandbredder som stöds finns nedan. Kontrollera med din anslutningsleverantör vilken bandbreddslista de erbjuder.

* 50 Mbit/s
* 100 Mbit/s
* 200 Mbit/s
* 500 Mbit/s
* 1 Gbit/s
* 2 Gbit/s
* 5 Gbit/s
* 10 Gbit/s

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamisk skalning av bandbredd
Du kan öka ExpressRoute-kretsens bandbredd (baserat på bästa prestanda) utan att behöva avbryta dina anslutningar. 

### <a name="flexible-billing-models"></a>Flexibla faktureringsmodeller
Du kan välja den faktureringsmodell som passar dig bäst. Välj mellan faktureringsmodellerna nedan. Mer information finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

* **Obegränsad data**. ExpressRoute-kretsen debiteras baserat på en månatlig avgift och all inkommande och utgående dataöverföring är kostnadsfri. 
* **Avgiftsbelagda data**. ExpressRoute-kretsens debitering baseras på en månatlig avgift. All inkommande dataöverföring är kostnadsfri. Utgående dataöverföring debiteras per GB data som överförs. Dataöverföringskostnader varierar beroende på region.
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
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurera peering för en ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
  * [Koppla ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)
* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
