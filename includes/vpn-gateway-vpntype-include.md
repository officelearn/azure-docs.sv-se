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
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187225"
---
* **Principbaserad:** Principbaserade VPN: er kallades tidigare gatewayer med statisk routning i den klassiska distributionsmodellen. Principbaserade VPN: er krypterar och dirigerar paket via IPsec-tunnlar baserat på IPsec-principer som konfigureras med kombinationer av adressprefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-enhetens konfiguration. Värdet för en PolicyBased VPN-typ är *PolicyBased*. När du använder en PolicyBased VPN, Tänk på följande begränsningar:
  
  * Principbaserade VPN kan **endast** användas på Basic-gateway SKU: N. Den här VPN-typ är inte kompatibel med andra gateway SKU: er.
  * Du kan ha endast 1 tunnel när du använder en PolicyBased VPN.
  * Du kan bara använda principbaserad VPN-anslutningar för S2S-anslutningar och endast för vissa konfigurationer. De flesta konfigurationer för VPN-Gateway kräver en RouteBased VPN.
* **Routningsbaserad**: Ruttbaserad VPN kallades tidigare för dynamiska routnings-gatewayer i den klassiska distributionsmodellen. Ruttbaserad VPN använder ”vägar” i IP-vidarebefordringen eller i routningstabellen för att dirigera paket till sina respektive tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen (eller trafikväljaren) för RouteBased VPN: er konfigureras som alla-till-alla (eller jokertecken). Värdet för en RouteBased VPN-typ är *RouteBased*.
