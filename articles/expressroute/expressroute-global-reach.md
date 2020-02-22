---
title: 'Azure-ExpressRoute: Anslut till Microsoft Cloud med Global Reach'
description: Den här artikeln förklarar ExpressRoute Global räckvidd.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: cherylmc
ms.openlocfilehash: f574576044b7e4ddd34289b5cc45fe5ca353f180
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538512"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute är en privat och flexibel sätt att ansluta ditt lokala nätverk till Microsoft Cloud. Du kan komma åt många Microsoft-molntjänster som Azure och Office 365 från ditt privata data Center eller ditt företags nätverk. Du kan till exempel ha ett avdelningskontor i San Francisco med en ExpressRoute-krets i Silicon Valley och ett annat filialkontor i London med en ExpressRoute-krets i samma stad. Båda avdelningskontor kan ha höghastighetsanslutning till Azure-resurser i västra USA och Storbritannien, södra. Men det går inte att filialkontor utbyta data direkt med varandra. Med andra ord skicka 10.0.1.0/24 data till 10.0.3.0/24 och 10.0.4.0/24 men inte till 10.0.2.0/24.

![utan][1]

Med **ExpressRoute Global Reach**kan du länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk. I exemplet ovan med hjälp av ExpressRoute Global räckvidd, utbyta San Francisco kontoret (10.0.1.0/24) direkt data med dina London office (10.0.2.0/24) via de befintliga ExpressRoute-kretsarna och via Microsofts globala nätverk. 

![för][2]

## <a name="use-case"></a>Användningsfall
ExpressRoute Global räckvidd är avsedd att komplettera din tjänstleverantör WAN implementering och ansluta ditt avdelningskontor över hela världen. Till exempel om tjänstleverantören främst fungerar i USA och har länkade alla grenarna i USA, men tjänstleverantören fungerar inte i Japan och Hongkong SAR, med ExpressRoute Global räckvidd du arbeta med en lokal tjänst-provider och Microsoft ansluter grenarna för det de i USA med hjälp av ExpressRoute och vårt globala nätverk.

![användnings fall][3]

## <a name="availability"></a>Tillgänglighet 
ExpressRoute Global räckvidd stöds för närvarande på följande platser.

* Australien
* Kanada
* Frankrike
* Tyskland
* Hongkong SAR
* Irland
* Japan
* Korea
* Nederländerna
* Nya Zeeland
* Singapore
* Schweiz
* Storbritannien
* USA

Dina ExpressRoute-kretsar måste skapas på ExpressRoute- [peering-platserna](expressroute-locations.md) i ovanstående länder eller regioner. Om du vill aktivera ExpressRoute Global Reach mellan [olika politiska regioner](expressroute-locations.md)måste dina kretsar vara Premium SKU.

## <a name="next-steps"></a>Nästa steg
1. [Läs mer om ExpressRoute Global Reach](expressroute-faqs.md)
2. [Så här aktiverar du ExpressRoute Global Reach](expressroute-howto-set-global-reach.md)
3. [Länka ExpressRoute-krets till Azure Virtual Network](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram utan global räckvidd"
[2]: ./media/expressroute-global-reach/2.png "diagram med global räckvidd"
[3]: ./media/expressroute-global-reach/3.png "användnings fall för global räckvidd"
