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
ms.openlocfilehash: 5de227e5de5ef9b41f6e0f64db86b7195259f7d6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
* **PolicyBased:** PolicyBased VPN kallades tidigare statiska routnings-gatewayer i den klassiska distributionsmodellen. Principbaserade VPN: er krypterar och dirigerar paket via IPsec-tunnlar baserat på IPsec-principer som konfigureras med kombinationer av adressprefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-enhetens konfiguration. Värdet för en PolicyBased VPN-typ är *PolicyBased*. När du använder en PolicyBased VPN, Tänk på följande begränsningar:
  
  * PolicyBased VPN kan **endast** användas på grundläggande SKU-gatewayen. Den här VPN-typ är inte kompatibel med andra gateway-SKU: er.
  * Du kan ha endast 1 tunnel när du använder en PolicyBased VPN.
  * Du kan bara använda PolicyBased VPN för S2S-anslutningar och endast för vissa konfigurationer. De flesta konfigurationer för VPN-Gateway kräver ett RouteBased VPN.
* **RouteBased**: RouteBased VPN kallades tidigare för dynamiska routnings-gatewayer i den klassiska distributionsmodellen. RouteBased VPN: er använder ”rutter” i IP-vidarebefordrings- eller routningstabeller för att dirigera paket till sina motsvarande tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen (eller trafikväljaren) för RouteBased VPN konfigureras som alla-till-alla (eller jokertecken). Värdet för en RouteBased VPN-typ är *RouteBased*.
