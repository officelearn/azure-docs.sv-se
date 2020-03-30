---
title: 'Azure ExpressRoute: QoS-krav'
description: Den här sidan innehåller detaljerade krav för att konfigurera och hantera QoS. Skype för företag/rösttjänster diskuteras.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: cherylmc
ms.openlocfilehash: debc5d91478d0a5c3cc16c7b09f5713ba09b467e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74080103"
---
# <a name="expressroute-qos-requirements"></a>QoS-krav för ExpressRoute
Skype för företag har olika arbetsbelastningar som kräver särskild QoS-behandling. Om du tänker använda rösttjänster via ExpressRoute bör du följa kraven som beskrivs nedan.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> QoS-kraven gäller endast för Microsoft-peering. De DSCP-värden i din nätverkstrafik som togs emot på Azures offentliga peering och Azure privata peering kommer att återställas till 0. 
> 
> 

I följande tabell finns en lista över DSCP-markeringar som används av Microsoft Teams och Skype för företag. Se [Hantera QoS för Skype för företag](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) för mer information.

| **Trafikklass** | **Behandling (DSCP-markering)** | **Microsoft Teams och Skype för företag-arbetsbelastningar** |
| --- | --- | --- |
| **Röst** |EF (46) |Skype / Microsoft Teams / Lync röst |
| **Interaktiva** |AF41 (34) |Video, VBSS |
| |AF21 (18) |Appdelning | 
| **Default** |AF11 (10) |Filöverföring |
| |CS0 (0) |Annat |

* Du bör klassificera arbetsbelastningarna och markera rätt DSCP-värden. Följ riktlinjerna [här](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) om hur du anger DSCP-markeringar i nätverket.
* Du bör konfigurera och ge stöd för flera QoS-köer i nätverket. Rösten måste vara en fristående klass och få den EF-behandling som anges i [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* Du kan bestämma kömekanism, princip för överbelastningsidentifiering samt bandbreddsallokering per trafikklass. Men DSCP-markeringen för arbetsbelastningar i Skype för företag måste bevaras. Om du använder DSCP-markeringar som inte visas ovan, t.ex. AF31 (26), måste du skriva DSCP-värdet som 0 innan paketet skickas till Microsoft. Microsoft skickar endast paket som har markerats med DSCP-värdet i ovanstående tabellen. 

## <a name="next-steps"></a>Nästa steg
* Se kraven för [Routning](expressroute-routing.md) och [NAT](expressroute-nat.md).
* Se följande länkar för att konfigurera ExpressRoute-anslutningen.
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md)
  * [Konfigurera routning](expressroute-howto-routing-classic.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md)

