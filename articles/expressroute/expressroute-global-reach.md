---
title: Om ExpressRoute Global räckvidd | Microsoft Docs
description: Den här artikeln beskrivs nyheter ExpressRoute Global räckvidd.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/24/2018
ms.author: cherylmc
ms.openlocfilehash: 324225600ed52c523228f54c540c810a15ffd5d7
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023244"
---
# <a name="expressroute-global-reach-preview"></a>ExpressRoute (förhandsversion) med Global räckvidd
ExpressRoute är en privat och flexibel sätt att ansluta ditt lokala nätverk till Microsoft Cloud. Du kan komma åt många Microsoft-molntjänster som Azure, Office 365 och Dynamics 365 från ditt privata Datacenter eller företagets nätverk. Du kan till exempel ha ett avdelningskontor i San Francisco med en ExpressRoute-krets i Silicon Valley och ett annat filialkontor i London med en ExpressRoute-krets i samma stad. Båda avdelningskontor kan ha höghastighetsanslutning till Azure-resurser i västra USA och Storbritannien, södra. Men det går inte att filialkontor utbyta data direkt med varandra. Med andra ord skicka 10.0.1.0/24 data till 10.0.3.0/24 och 10.0.4.0/24 men inte till 10.0.2.0/24.

![utan][1]

Med **ExpressRoute Global räckvidd**, du kan länka ExpressRoute-kretsar tillsammans för att göra ett privat nätverk mellan ditt lokala nätverk. I exemplet ovan med hjälp av ExpressRoute Global räckvidd, utbyta San Francisco kontoret (10.0.1.0/24) direkt data med dina London office (10.0.2.0/24) via de befintliga ExpressRoute-kretsarna och via Microsofts globala nätverk. 

![med][2]

## <a name="use-case"></a>Användningsfall
ExpressRoute Global räckvidd är avsedd att komplettera din tjänstleverantör WAN implementering och ansluta ditt avdelningskontor över hela världen. Till exempel om tjänstleverantören främst fungerar i USA och har länkade alla grenarna i USA, men tjänstleverantören fungerar inte i Japan och Hongkong SAR, med ExpressRoute Global räckvidd du arbeta med en lokal tjänst-provider och Microsoft ansluter grenarna för det de i USA med hjälp av ExpressRoute och vårt globala nätverk.

![Användningsfall][3]

## <a name="availability"></a>Tillgänglighet 
ExpressRoute Global räckvidd stöds för närvarande på följande platser.

* Australien
* Frankrike
* Hongkong SAR
* Irland
* Japan
* Nederländerna
* Förenade Kindom
* USA

ExpressRoute-kretsarna måste skapas på den [ExpressRoute-peeringplatser](expressroute-locations.md) i ovanstående land eller region. Så här aktiverar du ExpressRoute Global räckvidd mellan [olika geopolitiska regioner](expressroute-locations.md), din kretsar måste vara Premium-SKU.

## <a name="next-steps"></a>Nästa steg
1. [Mer information om ExpressRoute Global räckvidd](expressroute-faqs.md)
2. [Så här aktiverar du ExpressRoute Global räckvidd](expressroute-howto-set-global-reach.md)
3. [Länka ExpressRoute-krets till Azure-nätverk](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram utan global räckvidd"
[2]: ./media/expressroute-global-reach/2.png "diagram med global räckvidd"
[3]: ./media/expressroute-global-reach/3.png "användningsfall för global räckvidd"
