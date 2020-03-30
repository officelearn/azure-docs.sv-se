---
title: 'Azure ExpressRoute: Anslut till Microsoft Cloud med global räckvidd'
description: I den här artikeln förklaras ExpressRoute Global Reach.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: cherylmc
ms.openlocfilehash: f574576044b7e4ddd34289b5cc45fe5ca353f180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538512"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute är ett privat och motståndskraftigt sätt att ansluta dina lokala nätverk till Microsoft Cloud. Du kan komma åt många Microsoft-molntjänster som Azure och Office 365 från ditt privata datacenter eller ditt företagsnätverk. Du kan till exempel ha ett filialkontor i San Francisco med en ExpressRoute-krets i Silicon Valley och ett annat filialkontor i London med en ExpressRoute-krets i samma stad. Båda filialkontoren kan ha höghastighetsanslutning till Azure-resurser i US West och UK South. Filialkontoren kan dock inte utbyta data direkt med varandra. Med andra ord kan 10.0.1.0/24 skicka data till 10.0.3.0/24 och 10.0.4.0/24, men INTE till 10.0.2.0/24.

![Utan][1]

Med **ExpressRoute Global Reach**kan du länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk. I exemplet ovan, med tillägg av ExpressRoute Global Reach, kan ditt San Francisco-kontor (10.0.1.0/24) direkt utbyta data med ditt London-kontor (10.0.2.0/24) via befintliga ExpressRoute-kretsar och via Microsofts globala nätverk. 

![Med][2]

## <a name="use-case"></a>Användningsfall
ExpressRoute Global Reach är utformad för att komplettera din tjänsteleverantörs WAN-implementering och ansluta dina filialkontor över hela världen. Om din tjänsteleverantör till exempel i första hand är verksam i USA och har länkat alla dina filialer i USA, men tjänsteleverantören inte är verksam i Japan och Hong Kong, med ExpressRoute Global Reach kan du arbeta med en lokal tjänsteleverantör och Microsoft ansluter dina filialer dit till de i USA med ExpressRoute och vårt globala nätverk.

![användningsfall][3]

## <a name="availability"></a>Tillgänglighet 
ExpressRoute Global Reach stöds för närvarande på följande platser.

* Australien
* Kanada
* Frankrike
* Tyskland
* Hongkong
* Irland
* Japan
* Korea
* Nederländerna
* Nya Zeeland
* Singapore
* Schweiz
* Storbritannien
* USA

Dina ExpressRoute-kretsar måste skapas på [ExpressRoute-peering-platserna](expressroute-locations.md) i ovanstående länder eller region. Om du vill aktivera ExpressRoute Global Reach mellan [olika geopolitiska regioner](expressroute-locations.md)måste dina kretsar vara Premium SKU.

## <a name="next-steps"></a>Nästa steg
1. [Läs mer om ExpressRoute Global Reach](expressroute-faqs.md)
2. [Så här aktiverar du ExpressRoute Global Reach](expressroute-howto-set-global-reach.md)
3. [Link ExpressRoute-krets till virtuellt Azure-nätverk](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram utan global räckvidd"
[2]: ./media/expressroute-global-reach/2.png "diagram med global räckvidd"
[3]: ./media/expressroute-global-reach/3.png "användningsfall av global räckvidd"
