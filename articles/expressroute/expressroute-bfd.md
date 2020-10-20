---
title: 'Azure-ExpressRoute: Konfigurera BFD'
description: Den här artikeln innehåller anvisningar om hur du konfigurerar BFD (identifiering av dubbelriktad vidarebefordran) över privat peering av en ExpressRoute-krets.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: duau
ms.openlocfilehash: fd1cad4031d83fd0e17286bfaabb77aa746b646a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202335"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurera BFD över ExpressRoute

ExpressRoute stöder identifiering av dubbelriktad vidarebefordran (BFD) både över privat och Microsoft-peering. Genom att aktivera BFD över ExpressRoute kan du påskynda identifieringen av länkar mellan Microsoft Enterprise Edge-enheter (MSEE: N) och de routrar som du avslutar ExpressRoute-kretsen på (CE/PE). Du kan säga upp ExpressRoute över kund gräns enheter eller routnings enheter för klient sidan (om du gick med i den hanterade Layer 3-anslutnings tjänsten). Det här dokumentet vägleder dig genom behovet av BFD och hur du aktiverar BFD över ExpressRoute.

## <a name="need-for-bfd"></a>Behov av BFD

Följande diagram visar fördelarna med att aktivera BFD över ExpressRoute-kretsen: [![1]][1]

Du kan aktivera ExpressRoute-kretsen antingen via Layer 2 anslutningar eller hanterade Layer 3-anslutningar. I båda fallen finns det ansvar för att identifiera eventuella länkfel i sökvägen, om det finns en eller flera Layer-2-enheter i ExpressRoute-anslutnings Sök väg.

BGP keepalive-och MSEE: N-enheter är vanligt vis konfigurerade som 60 respektive 180 sekunder. Därför kan det ta upp till tre minuter att identifiera eventuella länkfel och byta trafik till en annan anslutning efter en länk som misslyckats.

Du kan kontrol lera BGP-tidsövervakarna genom att konfigurera lägre BGP keepalive-och Hold-Time på kundens Edge-peering-enhet. Om BGP-tidsintervallen är felaktiga mellan de två peering-enheterna, använder BGP-sessionen mellan peer-datorerna det nedre timer-värdet. BGP keepalive kan anges till tre sekunder, och Hold-tiden i storleksordningen på flera sekunder. Att ställa in BGP-timers är dock mindre lämpligt eftersom protokollet är process intensivt.

I det här scenariot kan BFD hjälpa dig. BFD ger låg overheadkostnad för länkfel i ett under sekunds tidsintervall. 


## <a name="enabling-bfd"></a>Aktivera BFD

BFD konfigureras som standard under alla nyligen skapade ExpressRoute privata peering-gränssnitt på msee. För att aktivera BFD behöver du därför bara konfigurera BFD på din CEs/Parameterentiteter (både på dina primära och sekundära enheter). Att konfigurera BFD är två stegs process: du måste konfigurera BFD på gränssnittet och sedan länka det till BGP-sessionen.

Ett exempel på CE/PE-konfiguration (med Cisco IOS XE) visas nedan. 

```console
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
```

>[!NOTE]
>Så här aktiverar du BFD under en redan befintlig privat peering. Du måste återställa peer kopplingen. Se [återställa ExpressRoute-peering][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD timer-förhandling

Mellan BFD-peer-datorer fastställer överföringshastigheten för de två peer-datorerna. Msee BFD-överföring/mottagnings intervall anges till 300 millisekunder. I vissa fall kan intervallet anges till ett högre värde på 750 millisekunder. Genom att konfigurera högre värden kan du tvinga dessa intervall att vara längre. men inte kortare.

>[!NOTE]
>Om du har konfigurerat geo-redundanta ExpressRoute-kretsar eller använda VPN-anslutning från plats till plats som säkerhets kopiering, aktivering av BFD skulle hjälpa till att växla snabbare efter ett ExpressRoute anslutnings fel. 
>

## <a name="next-steps"></a>Efterföljande moment

Mer information eller hjälp finns i följande länkar:

- [Skapa och ändra en ExpressRoute-krets][CreateCircuit]
- [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD påskyndar länkning" av länkfel

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md