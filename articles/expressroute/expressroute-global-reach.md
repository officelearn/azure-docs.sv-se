---
title: 'Azure-ExpressRoute: Anslut till Microsoft Cloud med Global Reach'
description: Lär dig hur Azure ExpressRoute Global Reach kan länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: cherylmc
ms.openlocfilehash: 5c747253a56476b0fd9431311294d487ef336e69
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190752"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute är ett privat och flexibelt sätt att ansluta dina lokala nätverk till Microsoft Cloud. Du kan komma åt många Microsoft-molntjänster som Azure och Office 365 från ditt privata data Center eller ditt företags nätverk. Du kan till exempel ha ett avdelnings kontor i San Francisco med en ExpressRoute-krets i Silicon dal och ett annat avdelnings kontor i London med en ExpressRoute-krets i samma stad. Både avdelnings kontor kan ha snabb anslutning till Azure-resurser i västra USA och Storbritannien, södra. Avdelnings kontoren kan dock inte utbyta data direkt med varandra. Med andra ord kan 10.0.1.0/24 skicka data till 10.0.3.0/24 och 10.0.4.0/24, men inte till 10.0.2.0/24.

![utan][1]

Med **ExpressRoute Global Reach**kan du länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk. I ovanstående exempel, med tillägget av ExpressRoute Global Reach, kan ditt San Francisco-kontor (10.0.1.0/24) direkt utbyta data med ditt London-kontor (10.0.2.0/24) genom befintliga ExpressRoute-kretsar och via Microsofts globala nätverk. 

![för][2]

## <a name="use-case"></a>Användningsfall
ExpressRoute Global Reach har utformats för att komplettera din tjänst leverantörs WAN-implementering och ansluta dina filial kontor över hela världen. Om din tjänst leverantör till exempel huvudsakligen arbetar i USA och har länkat alla dina grenar i USA, men tjänste leverantören inte fungerar i Japan och Hongkong, med ExpressRoute Global Reach du kan arbeta med en lokal tjänst leverantör och Microsoft ansluter dina grenar dit till dem i USA med ExpressRoute och vårt globala nätverk.

![användnings fall][3]

## <a name="availability"></a>Tillgänglighet 
ExpressRoute Global Reach stöds för närvarande på följande platser.

* Australien
* Kanada
* Frankrike
* Tyskland
* Folkrepubliken Kinas särskilda administrativa region Hongkong
* Irland
* Japan
* Korea
* Nederländerna
* Nya Zeeland
* Norge
* Singapore
* Sverige
* Schweiz
* Storbritannien
* USA

Dina ExpressRoute-kretsar måste skapas på ExpressRoute- [peering-platserna](expressroute-locations.md) i ovanstående länder eller regioner. Om du vill aktivera ExpressRoute Global Reach mellan [olika politiska regioner](expressroute-locations.md)måste dina kretsar vara Premium SKU.

## <a name="next-steps"></a>Nästa steg
1. [Visa Global Reach vanliga frågor och svar](expressroute-faqs.md#globalreach)
2. [Lär dig hur du aktiverar Global Reach](expressroute-howto-set-global-reach.md)
3. [Lär dig hur du länkar en ExpressRoute-krets till ditt virtuella nätverk](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram utan global räckvidd"
[2]: ./media/expressroute-global-reach/2.png "diagram med global räckvidd"
[3]: ./media/expressroute-global-reach/3.png "användnings fall för global räckvidd"
