---
title: Använda Azure ExpressRoute för att stödja fjärranvändare
description: På den här sidan beskrivs hur du kan använda Azure ExpressRoute för att möjliggöra fjärrarbete på grund av COVID-19-pandemin.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336657"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Använda Azure ExpressRoute för att skapa hybridanslutning för att stödja fjärranvändare

I den här artikeln beskrivs hur du kan använda ExpressRoute, Azure, Microsoft-nätverk och Azure-partnerekosystemet för att arbeta på distans.

## <a name="connecting-to-azure-services-with-expressroute"></a>Ansluta till Azure-tjänster med ExpressRoute

>[!NOTE]
>I den här artikeln beskrivs hur du kan utnyttja ExpressRoute, Azure, Microsoft-nätverket och Azure-partnerekosystemet för att arbeta på distans och minska nätverksproblem som du står inför på grund av COVID-19-krisen.
>

[ExpressRoute](expressroute-introduction.md) är en Azure-tjänst som möjliggör privat anslutning mellan Microsoft-datacenter och infrastruktur som finns i dina lokaler eller i en samlokaliseringsfunktion. ExpressRoute-anslutningar går inte via offentligt Internet. De erbjuder säker anslutning, tillförlitlighet och hastigheter, med lägre och konsekventa svarstider än typiska anslutningar via Internet.

## <a name="useful-links"></a>Användbara länkar

* [Hur man ökar bandbredden för befintliga ExpressRoute krets](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute övervakning, mått och varningar](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Ruttoptimering över ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute för O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Asymmetriska routningsöverväganden](expressroute-asymmetric-routing.md)
* [Så här öppnar du en supportbegäran i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Felsöka

* [Kontrollera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
* Skaffa ARP-tabell i [Resurshanteraren](expressroute-troubleshooting-arp-resource-manager.md) och [Klassisk](expressroute-troubleshooting-arp-classic.md)
* [Återställa en misslyckad krets](reset-circuit.md)
* [Felsöka nätverksprestanda](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Efterföljande moment

* [Läs mer om ExpressRoute-anslutningsmodeller](expressroute-connectivity-models.md)
* Läs mer om ExpressRoute-anslutningar och routningsdomäner. Se [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md)
* Hitta en tjänstleverantör. Se [ExpressRoute-partner och peering-platser](expressroute-locations.md)
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* [Skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md)
* [Skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
* [Koppla ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)
