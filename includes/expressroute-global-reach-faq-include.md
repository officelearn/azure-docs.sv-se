---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187079"
---
### <a name="what-is-expressroute-global-reach"></a>Vad är ExpressRoute Global Reach?

ExpressRoute Global Reach är en Azure-tjänst som ansluter dina lokala nätverk via ExpressRoute-tjänsten via Microsofts globala nätverk. Om du till exempel har ett privat datacenter i Kalifornien anslutet till ExpressRoute i Silicon Valley och ett annat privat datacenter i Texas som är anslutet till ExpressRoute i Dallas, med ExpressRoute Global Reach, kan du ansluta dina privata datacenter tillsammans genom de två ExpressRoute-anslutningarna och din cross datacentertrafik kommer att passera genom Microsofts nätverks ryggrad.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Hur aktiverar eller inaktiverar jag ExpressRoute Global Reach?

Du aktiverar ExpressRoute Global Reach genom att ansluta dina ExpressRoute-kretsar tillsammans. Du inaktiverar funktionen genom att koppla från kretsarna. Se [konfigurationen](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Behöver jag ExpressRoute Premium för ExpressRoute Global Reach?

Om dina ExpressRoute-kretsar finns i samma geopolitiska region behöver du inte ExpressRoute Premium för att koppla ihop dem. Om två ExpressRoute-kretsar finns i olika geopolitiska regioner behöver du ExpressRoute Premium för båda kretsarna för att möjliggöra anslutning mellan dem. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Hur debiteras jag för ExpressRoute Global Reach?

ExpressRoute möjliggör anslutning från ditt lokala nätverk till Microsofts molntjänster. ExpressRoute Global Reach möjliggör anslutning mellan dina egna lokala nätverk via dina befintliga ExpressRoute-kretsar och utnyttjar Microsofts globala nätverk. ExpressRoute Global Reach faktureras separat från den befintliga ExpressRoute-tjänsten. Det finns en tilläggsavgift för att aktivera den här funktionen på varje ExpressRoute-krets. Trafik mellan lokala nätverk som aktiveras av ExpressRoute Global Reach debiteras för en utgående hastighet vid källan och för en ingående hastighet vid målet. Priserna baseras på den zon där kretsarna är belägna.

### <a name="where-is-expressroute-global-reach-supported"></a>Var stöds ExpressRoute Global Reach?

ExpressRoute Global Reach stöds i [vissa länder/regioner eller platser](../articles/expressroute/expressroute-global-reach.md). ExpressRoute-kretsarna måste skapas på peering-platserna i dessa länder/regioner eller platser.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Jag har mer än två lokala nätverk, var och en ansluten till en ExpressRoute krets. Kan jag aktivera ExpressRoute Global Reach för att koppla ihop alla mina lokala nätverk?

Ja, det kan du, så länge kretsarna finns i de länder/regioner som stöds. Du måste ansluta två ExpressRoute-kretsar åt gången. Om du vill skapa ett helt nätverksnät måste du räkna upp alla kretspar och upprepa konfigurationen. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Kan jag aktivera ExpressRoute Global Reach mellan två ExpressRoute-kretsar på samma peering-plats?

Nej. De två kretsarna måste vara från olika peering platser. Om en tunnelbana i ett land/en region som stöds har mer än en ExpressRoute-peering-plats kan du ansluta de ExpressRoute-kretsar som skapats på olika peering-platser i den tunnelbanan. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Om ExpressRoute Global Reach är aktiverat mellan krets X och krets Y, och mellan krets Y och krets Z, kommer mina lokala nätverk anslutna till krets X och krets Z prata med varandra via Microsofts nätverk?

Nej. Om du vill aktivera anslutningen mellan två av dina lokala nätverk måste du uttryckligen ansluta motsvarande ExpressRoute-kretsar. I exemplet ovan måste du ansluta krets X och krets Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Vad är det nätverksdataflöde jag kan förvänta mig mellan mina lokala nätverk när jag har aktiverat ExpressRoute Global Reach?

Nätverksflödet mellan dina lokala nätverk, som aktiveras av ExpressRoute Global Reach, begränsas av den mindre av de två ExpressRoute-kretsarna. Lokal-till-Azure-trafik och lokal-till-lokal trafik delar samma krets och omfattas av samma bandbreddstak. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Med ExpressRoute Global Reach, vilka är gränserna för hur många rutter jag kan annonsera och hur många rutter jag kommer att få?

Antalet vägar som du kan annonsera till Microsoft på Azure-privat peering ligger kvar på 4000 på en standardkrets eller 10000 på en Premium-krets. Antalet vägar som du får från Microsoft på Azure-privat peering kommer att vara summan av vägarna för dina virtuella Azure-nätverk och vägarna från dina andra lokala nätverk som är anslutna via ExpressRoute Global Reach. Se till att du anger en lämplig maximal prefixgräns för din lokala router. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Vad är SLA för ExpressRoute Global Reach?

ExpressRoute Global Reach kommer att tillhandahålla samma [tillgänglighet sla](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) som den vanliga ExpressRoute tjänsten.
