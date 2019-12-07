---
title: Om Azure ExpressRoute-FastPath
description: Lär dig om Azure ExpressRoute-FastPath för att skicka nätverks trafik genom att kringgå gatewayen
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: 6ff1dac312bcb4df1e1afc9679df09fc8a2b28ff
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897369"
---
# <a name="about-expressroute-fastpath"></a>Om ExpressRoute FastPath

ExpressRoute virtuella nätverksgateway är utformad för att utbyta nätverks vägar och dirigera nätverks trafik. FastPath är utformat för att förbättra data Sök vägens prestanda mellan ditt lokala nätverk och ditt virtuella nätverk. När aktive rad skickar FastPath nätverks trafik direkt till virtuella datorer i det virtuella nätverket, vilket kringgår gatewayen.

## <a name="requirements"></a>Krav

### <a name="circuits"></a>Kretsar

FastPath finns på alla ExpressRoute-kretsar.

### <a name="gateways"></a>Gateways

FastPath kräver fortfarande att en virtuell nätverksgateway skapas för att utväxla vägar mellan ett virtuellt nätverk och ett lokalt nätverk. Mer information om virtuella nätverks-gatewayer och ExpressRoute finns i [ExpressRoute virtuella](expressroute-about-virtual-network-gateways.md)nätverksgateway.

Den virtuella Nätverksgatewayen måste vara antingen:

* Ultrahöga prestanda
* ErGw3AZ

## <a name="supported-features"></a>Funktioner som stöds

Även om FastPath har stöd för de flesta konfigurationer, stöder den inte följande funktioner:

* UDR i Gateway-undernätet: om du tillämpar en UDR på Gateway-undernätet för det virtuella nätverket, kommer nätverks trafiken från ditt lokala nätverk även att skickas till den virtuella Nätverksgatewayen.

* VNet-peering: om du har andra virtuella nätverk som är peer-anslutna med det som är anslutet till ExpressRoute, kommer nätverks trafiken från ditt lokala nätverk till de andra virtuella nätverken (d.v.s. det kallas "ekrar"-virtuella nätverk) att skickas till det virtuella nätverket nyckeln. Lösningen är att ansluta alla virtuella nätverk till ExpressRoute-kretsen direkt.

* Grundläggande Load Balancer: om du distribuerar en enkel intern belastningsutjämnare i det virtuella nätverket eller den Azure PaaS-tjänst som du distribuerar i ditt virtuella nätverk använder en enkel intern belastningsutjämnare, nätverks trafiken från ditt lokala nätverk till de virtuella IP-adresserna som finns på Basic Load Balancer skickas till den virtuella Nätverksgatewayen. Lösningen är att uppgradera den grundläggande belastningsutjämnaren till en [standard belastnings utjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Privat länk: om du ansluter till en [privat slut punkt](../private-link/private-link-overview.md) i ditt virtuella nätverk från ditt lokala nätverk, kommer anslutningen att gå via den virtuella Nätverksgatewayen.
 
## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar FastPath finns i [Länka ett virtuellt nätverk till ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
