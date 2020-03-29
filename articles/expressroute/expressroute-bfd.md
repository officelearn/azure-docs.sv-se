---
title: 'Azure ExpressRoute: Konfigurera BFD'
description: Den här artikeln innehåller instruktioner om hur du konfigurerar BFD (Bidirectional Forwarding Detection) över privat peering av en ExpressRoute-krets.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 378b639e89ffd46f6b32d7004f934104dd4b5407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064845"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurera BFD via ExpressRoute

ExpressRoute stöder BFD (Bidirectional Forwarding Detection) både via privat och Microsoft-peering. Genom att aktivera BFD via ExpressRoute kan du påskynda identifiering av länkfel mellan MICROSOFT Enterprise edge (MSEE) enheter och de routrar där du avslutar ExpressRoute-kretsen (CE/PE). Du kan avsluta ExpressRoute via Customer Edge-routningsenheter eller Partner Edge-routningsenheter (om du gick med hanterad Layer 3-anslutningstjänst). Det här dokumentet går igenom behovet av BFD och hur du aktiverar BFD över ExpressRoute.

## <a name="need-for-bfd"></a>Behov av BFD

Följande diagram visar fördelen med att aktivera BFD över ExpressRoute krets: [![1]][1]

Du kan aktivera ExpressRoute-krets antingen via Layer 2-anslutningar eller hanterade Layer 3-anslutningar. In either case, if there are one or more Layer-2 devices in the ExpressRoute connection path, responsibility of detecting any link failures in the path lies with the overlying BGP.

På MSEE-enheterna konfigureras normalt BGP keepalive och hold-time som 60 respektive 180 sekunder. Därför, efter en länk fel det skulle ta upp till tre minuter att identifiera eventuella länk fel och växla trafik till alternativ anslutning.

Du kan styra BGP-timers genom att konfigurera lägre BGP keepalive och hold-time på kundens kant peering enhet. Om BGP-timers är inkompatibla mellan de två peering-enheterna, använder BGP-sessionen mellan peer-datorerna det lägre timervärdet. BGP-keepalive kan ställas in så lågt som tre sekunder och hålltiden i ordningen på tiotals sekunder. Att ställa in BGP-timers aggressivt är dock mindre att föredra eftersom protokollet är processintensivt.

I det här fallet kan BFD hjälpa till. BFD ger identifiering av fel vid låga omkostnader i ett delsektertidsintervall. 


## <a name="enabling-bfd"></a>Aktivera BFD

BFD har konfigurerats som standard under alla nyligen skapade ExpressRoute privata peering-gränssnitt på MSEEs. Därför, för att aktivera BFD, måste du bara konfigurera BFD på dina CEs / PEs (både på din primära och sekundära enheter). Konfigurera BFD är tvåstegsprocess: du måste konfigurera BFD på gränssnittet och sedan länka den till BGP-sessionen.

Ett exempel PÅ CE/PE-konfiguration (med Cisco IOS XE) visas nedan. 

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
>Så här aktiverar du BFD under en redan befintlig privat peering; du behöver återställa peering. Se [Återställ ExpressRoute-peerings][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD Timer Förhandling

Mellan BFD-peer-datorer avgör de långsammare av de två peer-datorerna överföringshastigheten. MSEEs BFD överförings-/mottagningsintervall är inställda på 300 millisekunder. I vissa fall kan intervallet ställas in till ett högre värde på 750 millisekunder. Genom att konfigurera högre värden kan du tvinga dessa intervall att vara längre. men inte kortare.

>[!NOTE]
>Om du har konfigurerat Geo-redundanta ExpressRoute-kretsar eller använder IPSec VPN-anslutning från plats till plats som säkerhetskopiering. aktivera BFD skulle hjälpa redundans snabbare efter ett ExpressRoute-anslutningsfel. 
>

## <a name="next-steps"></a>Efterföljande moment

Mer information eller hjälp finns i följande länkar:

- [Skapa och ändra en ExpressRoute-krets][CreateCircuit]
- [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD påskyndar länk fel avdrag tid"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






