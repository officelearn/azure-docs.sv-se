---
title: Konfigurera BFD via ExpressRoute | Microsoft Docs
description: Det här dokumentet innehåller anvisningar om hur du konfigurerar BFD över privata peering för en ExpressRoute-krets.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 8/17/2018
ms.author: rambala
ms.openlocfilehash: acac0a866d1f494830be45c23ebca6844431c9de
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40226027"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurera BFD via ExpressRoute

ExpressRoute stöder dubbelriktade vidarebefordran identifiering (BFD) över privat peering. Genom att aktivera BFD via ExpressRoute, kan du påskynda länk felidentifiering mellan Microsoft Enterprise-gränsenheter (MSEE) och routrar som där du avslutar ExpressRoute-krets (PE). Du kan avsluta ExpressRoute över routning kundens Edge-enheter eller Routning Partner Edge-enheter (om du har gått med hanteringstjänster för Layer 3-anslutning). Det här dokumentet vägleder dig genom behovet av BFD och hur du aktiverar BFD via ExpressRoute.

## <a name="need-for-bfd"></a>Behovet av BFD

Följande diagram visar fördelen med att aktivera BFD via ExpressRoute-krets: [ ![1]][1]

Du kan aktivera ExpressRoute-krets antingen med Layer-2-anslutningar eller hanterade Layer 3-anslutningar. I båda fallen om det finns en eller flera Layer-2-enheter i anslutningssökvägen ExpressRoute ansvaret identifiera eventuella fel i sökvägen med ligger ovanpå BGP.

På msee: N-enheter konfigureras BGP keepalive och håll ned tid vanligtvis som 60 och 180 sekunder respektive. Därför länka fel tre minuter att upptäcka efter ett länkfel som det tar upp till och växla trafik till alternativ anslutning.

Du kan styra BGP timers genom att konfigurera lägre BGP keepalive och håll ned tid på kundens edge peering-enheten. Om BGP-timers Felmatchade mellan de två peering enheterna, använder BGP-sessionen mellan peer-datorer det lägre timer-värdet. BGP-keepalive kan anges så lågt som tre sekunder och driftstid i tio sekunder. Dock inställningarna BGP timers aggressivt mindre föredra eftersom protokollet är processen beräkningsintensiva.

I det här scenariot kan BFD. BFD ger låg belastning länk felidentifiering i ett subsecond tidsintervall. 


## <a name="enabling-bfd"></a>Aktivera BFD

BFD konfigureras som standard under alla nyligen skapade ExpressRoute privat peering gränssnitt i msee: erna. Därför, om du vill aktivera BFD, måste du konfigurera bara BFD på din PEs. Konfigurera BFD är en tvåstegsprocess: du behöver konfigurera BFD gränssnitt och länkar den till BGP-sessionen.

En exempelkonfiguration PE (med Cisco IOS XE) visas nedan. 

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
>Aktivera BFD under en redan befintlig privat peering; Du behöver återställa peer-kopplingen. Se [återställa ExpressRoute-peerkopplingar][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD Timer förhandling

Mellan BFD peer-datorer avgör långsammare för de båda datorerna överföringshastigheten. Msee BFD överföring och ta emot intervall är inställda på 300 millisekunder. Genom att konfigurera högre värden kan tvinga du dessa intervall som ska vara längre; men inte kortare.

>[!NOTE]
>Om du har konfigurerat Geo-redundant ExpressRoute-kretsar för privat peering eller använda plats-till-plats-IPSec VPN-anslutning som säkerhetskopiering för ExpressRoute privat peering; Aktivera BFD över privat peering vill hjälpa redundans snabbare följa en ExpressRoute-anslutningsfel. 
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
[ResetPeering]: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-howto-reset-peering






