---
title: Använda Azure-ExpressRoute för att stödja fjärran vändare
description: På den här sidan beskrivs hur du kan använda Azure-ExpressRoute för att aktivera fjärrhantering på grund av COVID-19-Pandemic.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 18b30380ede0c95c7d039749a4d47f65e5824058
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84738081"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Använda Azure-ExpressRoute för att skapa hybrid anslutningar som stöder fjärran vändare

Den här artikeln beskriver hur du kan utnyttja ExpressRoute, Azure, Microsoft Network och Azure-partnerns eko system för att kunna arbeta på distans.

## <a name="connecting-to-azure-services-with-expressroute"></a>Ansluta till Azure-tjänster med ExpressRoute

>[!NOTE]
>Den här artikeln beskriver hur du kan utnyttja ExpressRoute, Azure, Microsoft Network och Azure-partnerns eko system för att arbeta med fjärrnätverket och minimera nätverks problem som du står inför på grund av COVID-19-kris.
>

[ExpressRoute](expressroute-introduction.md) är en Azure-tjänst som möjliggör privat anslutning mellan Microsoft-datacenter och infrastruktur som finns lokalt eller i en samplacerings anläggning. ExpressRoute-anslutningar går inte via offentligt Internet. De erbjuder säker anslutning, tillförlitlighet och hastigheter, med lägre och konsekvent fördröjning än vanliga anslutningar via Internet.

## <a name="useful-links"></a>Användbara länkar

* [Så här ökar du bandbredden för befintliga ExpressRoute-kretsar](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute-övervakning, mått och aviseringar](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Väg optimering över ExpressRoute](expressroute-optimize-routing.md)
* [Azure-ExpressRoute för O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Överväganden vid asymmetrisk routning](expressroute-asymmetric-routing.md)
* [Så här öppnar du en support förfrågan i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Felsöka

* [Kontrollera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
* Hämta ARP-tabell i [Resource Manager](expressroute-troubleshooting-arp-resource-manager.md) och [klassisk](expressroute-troubleshooting-arp-classic.md)
* [Återställa en misslyckad krets](reset-circuit.md)
* [Felsöka nätverks prestanda](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Efterföljande moment

* [Lär dig mer om ExpressRoute anslutnings modeller](expressroute-connectivity-models.md)
* Läs mer om ExpressRoute-anslutningar och routningsdomäner. Se [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md)
* Hitta en tjänstleverantör. Se [ExpressRoute-partner och peering-platser](expressroute-locations.md)
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* [Skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md)
* [Skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
* [Koppla ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)
