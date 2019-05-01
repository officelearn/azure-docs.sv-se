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
ms.openlocfilehash: 14793d7f787780bfc8604e4af11eb05f1ff0d937
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859868"
---
|  | **Punkt-till-plats** | **Plats till plats** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure-tjänster som stöds** |Cloud Services och Virtual Machines |Cloud Services och Virtual Machines |[Tjänstlista](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Vanliga bandbredder** |Baserat på gateway-SKU |Vanligtvis < 1 Gbit/s sammanlagt |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Protokoll som stöds** |SSTP (Secure Sockets Tunneling Protocol) och IPsec |IPsec |Direktanslutning över VLAN, NSP:er VPN-teknologier (MPLS, VPLS,...) |
| **Routning** |RouteBased (dynamisk) |Vi stöder principbaserad (statisk routning) och ruttbaserad (dynamisk routning VPN) |BGP |
| **Anslutningsåterhämtning** |aktiv-passiv |aktiv-passiv eller aktiv-aktiv |aktiv-aktiv |
| **Vanligt användningsfall** |Prototyper, dev/test/testlabb-scenarier för molntjänster och virtuella datorer |Dev/test/testlabb-scenarier och småskaliga produktionsbelastningar för molntjänster och virtuella datorer |Åtkomst till alla Azure-tjänster (validerad lista), Enterprise-klass och verksamhetskritiska arbetsbelastningar, säkerhetskopiering, Big Data, Azure som en DR-plats |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Prissättning** |[Prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prissättning](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Teknisk dokumentation** |[Dokumentation om VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentation om VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentation om ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Vanliga frågor och svar** |[Vanliga frågor och svar om VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar om VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar för ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
