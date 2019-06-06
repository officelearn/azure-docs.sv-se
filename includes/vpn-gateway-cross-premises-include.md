---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/16/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c82e3d2c978a7980d02bc686346acdcb21dd591
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66425748"
---
|  | **Punkt-till-plats** | **Plats till plats** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure-tjänster som stöds** |Cloud Services och Virtual Machines |Cloud Services och Virtual Machines |[Tjänstlista](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Vanliga bandbredder** |Baserat på gateway-SKU |Vanligtvis < 1 Gbit/s sammanlagt |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Protokoll som stöds** |Secure Sockets Tunneling Protocol (SSTP), OpenVPN och IPsec |IPsec |Direktanslutning över VLAN, NSP:er VPN-teknologier (MPLS, VPLS,...) |
| **Routning** |RouteBased (dynamisk) |Vi stöder principbaserad (statisk routning) och ruttbaserad (dynamisk routning VPN) |BGP |
| **Anslutningsåterhämtning** |aktiv-passiv |aktiv-passiv eller aktiv-aktiv |aktiv-aktiv |
| **Vanligt användningsfall** |Prototyper, dev/test/testlabb-scenarier för molntjänster och virtuella datorer |Dev/test/testlabb-scenarier och småskaliga produktionsbelastningar för molntjänster och virtuella datorer |Åtkomst till alla Azure-tjänster (validerad lista), Enterprise-klass och verksamhetskritiska arbetsbelastningar, säkerhetskopiering, Big Data, Azure som en DR-plats |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Prissättning** |[Prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prissättning](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Teknisk dokumentation** |[Dokumentation om VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentation om VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentation om ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Vanliga frågor och svar** |[Vanliga frågor och svar om VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar om VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar för ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
