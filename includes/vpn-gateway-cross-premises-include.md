---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fa9c27457b1da4d233aaea2a6621af9f5d01149d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
|  | **Punkt-till-plats** | **Plats-till-plats** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure-tjänster som stöds** |Cloud Services och Virtual Machines |Cloud Services och Virtual Machines |[Lista över tjänster](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Vanliga bandbredder** |Vanligtvis < 100 Mbps sammanlagt |Vanligtvis < 1 Gbit/s-mängd |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Protokoll som stöds** |SSTP (Secure Sockets Tunneling Protocol) |IPsec |Direktanslutning över VLAN, NSP:er VPN-teknologier (MPLS, VPLS,...) |
| **Routning** |RouteBased (dynamisk) |Vi stöder PolicyBased (statisk routning) och RouteBased (dynamisk routning VPN) |BGP |
| **Anslutningsåterhämtning** |aktiv-passiv |aktivt-passivt eller aktivt-aktivt |aktiv-aktiv |
| **Vanligt användningsfall** |Prototyper, dev/test/testlabb-scenarier för molntjänster och virtuella datorer |Dev/test/testlabb-scenarier och småskaliga produktionsbelastningar för molntjänster och virtuella datorer |Åtkomst till alla Azure-tjänster (validerad lista), Enterprise-klass och verksamhetskritiska arbetsbelastningar, säkerhetskopiering, Big Data, Azure som en DR-plats |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Prissättning** |[Prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prissättning](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Teknisk dokumentation** |[Dokumentation om VPN-gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentation om VPN-gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentation om ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Vanliga frågor och svar** |[Vanliga frågor och svar om VPN-gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar om VPN-gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Vanliga frågor och svar för ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
