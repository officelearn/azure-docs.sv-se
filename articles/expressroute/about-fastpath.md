---
title: Om Azure ExpressRoute FastPath
description: Lär dig mer om Azure ExpressRoute FastPath för att skicka nätverkstrafik genom att kringgå gatewayen
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282859"
---
# <a name="about-expressroute-fastpath"></a>Om ExpressRoute FastPath

ExpressRoute virtuellt nätverk gateway är utformad för att utbyta nätverksvägar och dirigera nätverkstrafik. FastPath är utformat för att förbättra datasökvägens prestanda mellan det lokala nätverket och det virtuella nätverket. När det är aktiverat skickar FastPath nätverkstrafik direkt till virtuella datorer i det virtuella nätverket och kringgår gatewayen.

## <a name="requirements"></a>Krav

### <a name="circuits"></a>Kretsar

FastPath är tillgängligt på alla ExpressRoute-kretsar.

### <a name="gateways"></a>Gateways

FastPath kräver fortfarande att en virtuell nätverksgateway skapas för att utbyta vägar mellan virtuellt nätverk och lokalt nätverk. Mer information om virtuella nätverksgateways och ExpressRoute, inklusive prestandainformation och gateway-SKU:er, finns i [ExpressRoute-gateways för virtuella nätverk](expressroute-about-virtual-network-gateways.md).

Om du vill konfigurera FastPath måste den virtuella nätverksgatewayen vara antingen:

* Ultra prestanda
* ErGw3AZ (ErGw3AZ)

## <a name="supported-features"></a>Funktioner som stöds

FastPath stöder de flesta konfigurationer, men stöder inte följande funktioner:

* UDR i gateway-undernätet: Om du använder en UDR på gateway-undernätet i det virtuella nätverket fortsätter nätverkstrafiken från det lokala nätverket att skickas till den virtuella nätverksgatewayen.

* VNet-peering: Om du har andra virtuella nätverk peered med den som är ansluten till ExpressRoute, nätverkstrafik från ditt lokala nätverk till andra virtuella nätverk (dvs. den så kallade "Spoke" virtuella nätverk) kommer att fortsätta att skickas till det virtuella nätverket Gateway. Lösningen är att ansluta alla virtuella nätverk till ExpressRoute-kretsen direkt.

* Grundläggande belastningsutjämning: Om du distribuerar en grundläggande intern belastningsutjämnare i ditt virtuella nätverk eller azure PaaS-tjänsten som du distribuerar i det virtuella nätverket använder en grundläggande intern belastningsutjämnare, använder nätverkstrafiken från det lokala nätverket till de virtuella IP-adresser som finns på Grundläggande belastningsutjämnare skickas till den virtuella nätverksgatewayen. Lösningen är att uppgradera den grundläggande belastningsutjämnaren till en [standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Privat länk: Om du ansluter till en [privat slutpunkt](../private-link/private-link-overview.md) i det virtuella nätverket från det lokala nätverket går anslutningen via den virtuella nätverksgatewayen.
 
## <a name="next-steps"></a>Nästa steg

Om du vill aktivera FastPath finns i [Länka ett virtuellt nätverk till ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
