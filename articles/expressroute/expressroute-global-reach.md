---
title: 'Azure-ExpressRoute: Anslut till Microsoft Cloud med Global Reach'
description: Lär dig hur Azure ExpressRoute Global Reach kan länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: duau
ms.openlocfilehash: 8c6ed19a585bc1ebae65045cd1cc4c442f113597
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651489"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute är ett privat och flexibelt sätt att ansluta dina lokala nätverk till Microsoft Cloud. Du kan komma åt många moln tjänster från Microsoft, till exempel Azure och Microsoft 365 från ditt privata data Center eller ditt företags nätverk. Du kan till exempel ha ett avdelnings kontor i San Francisco med en ExpressRoute-krets i Silicon dal och ett annat avdelnings kontor i London med en ExpressRoute-krets i samma stad. Både avdelnings kontor har snabb anslutning till Azure-resurser i västra USA och Storbritannien, södra. Men avdelnings kontoren kan inte ansluta och skicka data direkt med varandra. Med andra ord kan 10.0.1.0/24 skicka data till 10.0.3.0/24 och 10.0.4.0/24-nätverket, men inte till 10.0.2.0/24-nätverket.

![Diagram som visar kretsar som inte länkats tillsammans med Express Route Global Reach.][1]

Med **ExpressRoute Global Reach**kan du länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk. I ovanstående exempel, med tillägget av ExpressRoute Global Reach, kan ditt San Francisco-kontor (10.0.1.0/24) direkt utbyta data med ditt London-kontor (10.0.2.0/24) genom befintliga ExpressRoute-kretsar och via Microsofts globala nätverk. 

![Diagram som visar kretsar som länkats tillsammans med Express Route Global Reach.][2]

## <a name="use-case"></a>Användningsfall
ExpressRoute Global Reach har utformats för att komplettera din tjänst leverantörs WAN-implementering och ansluta dina filial kontor över hela världen. Om din tjänst leverantör till exempel huvudsakligen arbetar i USA och har länkat alla dina grenar i USA, men tjänste leverantören inte fungerar i Japan och Hongkong, med ExpressRoute Global Reach du kan arbeta med en lokal tjänst leverantör och Microsoft ansluter dina grenar dit till dem i USA med ExpressRoute och vårt globala nätverk.

![Diagram som visar ett användnings fall för Express Route-Global Reach.][3]

## <a name="availability"></a>Tillgänglighet 
ExpressRoute Global Reach stöds i de flesta regioner där ExpressRoute stöds för närvarande. Du kan referera till [ExpressRoute-anslutnings leverantörer](expressroute-locations-providers.md#partners) för aktuella regioner som stöds. 

> [!NOTE] 
> Om du vill aktivera ExpressRoute Global Reach mellan [olika politiska regioner](expressroute-locations-providers.md#locations)måste dina kretsar vara **Premium SKU**.

## <a name="next-steps"></a>Nästa steg
- Visa [Global Reach vanliga frågor och svar](expressroute-faqs.md#globalreach).
- Lär dig hur du [aktiverar Global Reach](expressroute-howto-set-global-reach.md).
- Lär dig hur du [länkar en ExpressRoute-krets till det virtuella nätverket](expressroute-howto-linkvnet-arm.md).

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram utan global räckvidd"
[2]: ./media/expressroute-global-reach/2.png "diagram med global räckvidd"
[3]: ./media/expressroute-global-reach/3.png "användnings fall för global räckvidd"
