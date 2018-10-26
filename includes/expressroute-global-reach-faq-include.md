---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 10/23/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4a3581b3d61c5ad17352c2865fc059704c1eece1
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "50035187"
---
### <a name="what-is-expressroute-global-reach"></a>Vad är ExpressRoute Global räckvidd?

Global räckvidd i ExpressRoute är en Azure-tjänst som ansluter dina lokala nätverk via ExpressRoute-tjänsten via Microsofts globala nätverk. Till exempel om du har ett privat datacenter i Kalifornien som är anslutet till ExpressRoute i Silicon Valley och ett annat privat datacenter i Texas anslutet till ExpressRoute i Dallas, med ExpressRoute Global räckvidd, kan du ansluta dina privata Datacenter tillsammans via två ExpressRoute-anslutningar och din mellan ska data center trafik passera genom Microsofts stamnätverk.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Hur jag för att aktivera eller inaktivera ExpressRoute Global räckvidd?

Du aktiverar ExpressRoute Global räckvidd genom att ansluta din ExpressRoute-kretsar tillsammans. Du kan inaktivera funktionen genom att koppla ifrån kretsarna. Se den [configuration](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Behöver jag ExpressRoute Premium för Global räckvidd för ExpressRoute?

Om din ExpressRoute-kretsar finns i samma geopolitiska region, behöver du inte ExpressRoute Premium för att ansluta dem till varandra. Om det finns två ExpressRoute-kretsar i olika geopolitiska regioner, måste ExpressRoute Premium för både kretsar för att aktivera anslutning mellan dem. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Hur kommer jag att debiteras för Global räckvidd för ExpressRoute?

Med ExpressRoute kan anslutningar från ditt lokala nätverk till Microsofts molntjänster. ExpressRoute Global räckvidd möjliggör anslutningar mellan dina egna lokala nätverk via din befintliga ExpressRoute-kretsar, att utnyttja Microsofts globala nätverk. ExpressRoute Global räckvidd faktureras separat från den befintliga ExpressRoute-tjänsten. Det finns en avgift för tillägg för att aktivera den här funktionen på varje ExpressRoute-krets. Trafik mellan ditt lokala nätverk som aktiveras med Global räckvidd för ExpressRoute debiteras för en utgående hastighet vid källan och för en inkommande hastighet vid målet. Priserna baseras på zonen där kretsarna som är placerade. Se <pricing page>

### <a name="where-is-expressroute-global-reach-supported"></a>Där stöds ExpressRoute Global räckvidd?

ExpressRoute Global räckvidd stöds i följande länder. ExpressRoute-kretsar måste skapas på peering-platser i dessa länder.

* Australien
* Frankrike
* Hongkong SAR
* Irland
* Japan
* Nederländerna
* Storbritannien
* USA

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Jag har fler än två lokala nätverk, som är anslutna till en ExpressRoute-krets. Kan jag aktivera ExpressRoute Global räckvidd att koppla ihop alla mitt lokala nätverk?

Ja, du kan, så länge som kretsarna som finns i de länder/regioner som stöds. Du måste ansluta två ExpressRoute-kretsar i taget. Om du vill skapa ett fullständigt sluten nätverk, måste du räkna upp alla kretspar och upprepa konfigurationen. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Kan jag aktivera ExpressRoute Global räckvidd mellan två ExpressRoute-kretsar på samma peering plats?

Nej. De två kretsarna måste komma från olika peering-platser. Om en App i metro i ett land som stöds har mer än en ExpressRoute-peering plats, kan du ansluta tillsammans ExpressRoute-kretsar som skapas vid en annan peering-platser i den metro. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Om ExpressRoute Global räckvidd har aktiverats mellan krets X och Y-kretsen och mellan krets Y och Z-kretsen, kommer mitt lokala nätverk som är anslutna till krets X och krets Z kommunicera med varandra via Microsofts nätverk?

Nej. Om du vill aktivera anslutningen mellan två av dina lokala nätverk, måste du ansluta de motsvarande ExpressRoute-kretsarna uttryckligen. I exemplet ovan måste du ansluta krets X och Z-kretsen. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Vad är nätverkets genomflöde jag kan förvänta sig mellan min lokala nätverk när jag har aktiverat ExpressRoute Global räckvidd?

Nätverkets genomflöde mellan ditt lokala nätverk, aktiveras med ExpressRoute Global räckvidd, är begränsat av det mindre av de två ExpressRoute-kretsarna.

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Med ExpressRoute Global räckvidd, vilka är begränsningar i hur många vägar som jag kan annonsera och hur många vägar som jag får?

Hur många vägar du kan annonsera till Microsoft på Azures privata peering förblir 4000 på en standardkrets eller 10000 på en Premium-krets. Hur många vägar du får från Microsoft på Azures privata peering kommer att summan av vägar för din Azure-nätverk och vägarna från ditt lokala nätverk som är anslutna via ExpressRoute Global räckvidd. Kontrollera att du kan ange en lämplig maximal prefix-gräns på den lokala routern. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Vad är serviceavtalet för Global räckvidd för ExpressRoute?

ExpressRoute Global räckvidd ger samma [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) som vanlig ExpressRoute-tjänsten.
