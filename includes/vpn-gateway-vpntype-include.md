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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187225"
---
* **PolicyBaserad:** Principbaserade VPN kallades tidigare statiska routningsgateways i den klassiska distributionsmodellen. Principbaserade VPN krypterar och dirigerar paket via IPsec-tunnlar baserat på IPsec-principerna som konfigurerats med kombinationer av adressprefix mellan det lokala nätverket och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-enhetens konfiguration. Värdet för en PolicyBased VPN-typ är *PolicyBased*. Tänk på följande begränsningar när du använder ett PolicyBased VPN:
  
  * Principbaserade VPN kan **endast** användas på SKU för grundläggande gateway. Den här VPN-typen är inte kompatibel med andra sku:er för gateway.
  * Du kan bara ha en tunnel när du använder en PolicyBased VPN.
  * Du kan bara använda Principbaserade VPN för S2S-anslutningar och endast för vissa konfigurationer. De flesta VPN Gateway-konfigurationer kräver en RouteBased VPN.
* **RouteBased**: RouteBased VPN kallades tidigare dynamiska routningsgateways i den klassiska distributionsmodellen. RouteBased VPN använder "vägar" i IP-vidarebefordrings- eller routningstabellen för att dirigera paket till motsvarande tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen (eller trafikväljaren) för RouteBased VPN är konfigurerad som valfri (eller jokertecken). Värdet för en RouteBased VPN-typ är *RouteBased*.
