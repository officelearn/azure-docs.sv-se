---
title: 'Azure-ExpressRoute: Konfigurera BFD'
description: Den här artikeln innehåller anvisningar om hur du konfigurerar BFD (dubbelriktad vidarebefordran identifiering) över privata peering för en ExpressRoute-krets.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 608b5e0011d4ed656ff61fec84a23f2fb22373b3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080798"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurera BFD via ExpressRoute

ExpressRoute stöder identifiering av dubbelriktad vidarebefordran (BFD) både över privat och Microsoft-peering. Genom att aktivera BFD över ExpressRoute kan du påskynda identifieringen av länkar mellan Microsoft Enterprise Edge-enheter (MSEE: N) och de routrar som du avslutar ExpressRoute-kretsen på (CE/PE). Du kan avsluta ExpressRoute över routning kundens Edge-enheter eller Routning Partner Edge-enheter (om du har gått med hanteringstjänster för Layer 3-anslutning). Det här dokumentet vägleder dig genom behovet av BFD och hur du aktiverar BFD via ExpressRoute.

## <a name="need-for-bfd"></a>Behovet av BFD

Följande diagram visar fördelen med att aktivera BFD via ExpressRoute-krets: [ ![1]][1]

Du kan aktivera ExpressRoute-krets antingen med Layer-2-anslutningar eller hanterade Layer 3-anslutningar. I båda fallen om det finns en eller flera Layer-2-enheter i anslutningssökvägen ExpressRoute ansvaret identifiera eventuella fel i sökvägen med ligger ovanpå BGP.

På msee: N-enheter konfigureras BGP keepalive och håll ned tid vanligtvis som 60 och 180 sekunder respektive. Därför länka fel tre minuter att upptäcka efter ett länkfel som det tar upp till och växla trafik till alternativ anslutning.

Du kan styra BGP timers genom att konfigurera lägre BGP keepalive och håll ned tid på kundens edge peering-enheten. Om BGP-timers Felmatchade mellan de två peering enheterna, använder BGP-sessionen mellan peer-datorer det lägre timer-värdet. BGP-keepalive kan anges så lågt som tre sekunder och driftstid i tio sekunder. Att ställa in BGP-timers är dock mindre lämpligt eftersom protokollet är process intensivt.

I det här scenariot kan BFD. BFD ger låg belastning länk felidentifiering i ett subsecond tidsintervall. 


## <a name="enabling-bfd"></a>Aktivera BFD

BFD konfigureras som standard under alla nyligen skapade ExpressRoute privat peering gränssnitt i msee: erna. För att aktivera BFD behöver du därför bara konfigurera BFD på din CEs/Parameterentiteter (både på dina primära och sekundära enheter). Att konfigurera BFD är två stegs process: du måste konfigurera BFD på gränssnittet och sedan länka det till BGP-sessionen.

Ett exempel på CE/PE-konfiguration (med Cisco IOS XE) visas nedan. 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Aktivera BFD under en redan befintlig privat peering; Du behöver återställa peer-kopplingen. Se [återställa ExpressRoute-peering][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD Timer förhandling

Mellan BFD peer-datorer avgör långsammare för de båda datorerna överföringshastigheten. Msee BFD överföring och ta emot intervall är inställda på 300 millisekunder. I vissa fall, kan intervallet anges på ett högre värde för 750 millisekunder. Genom att konfigurera högre värden kan tvinga du dessa intervall som ska vara längre; men inte kortare.

>[!NOTE]
>Om du har konfigurerat geo-redundanta ExpressRoute-kretsar eller använda VPN-anslutning från plats till plats som säkerhets kopiering, aktivering av BFD skulle hjälpa till att växla snabbare efter ett ExpressRoute anslutnings fel. 
>

## <a name="next-steps"></a>Nästa steg

Mer information eller hjälp finns i följande länkar:

- [Skapa och ändra en ExpressRoute-krets][CreateCircuit]
- [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD skrivbordsdistributionen länktid fel avdrag"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






