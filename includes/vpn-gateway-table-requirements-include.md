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
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187230"
---
I följande tabell visas kraven för PolicyBased- och RouteBased VPN-gateways. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen. För den klassiska modellen är PolicyBased VPN-gateways samma som statiska gateways och ruttbaserade gateways är desamma som dynamiska gateways.

|  | **Principbaserad grundläggande VPN-gateway** | **RouteBased Grundläggande VPN-gateway** | **RouteBased Standard VPN Gateway** | **RouteBased VPN-gateway med hög prestanda** |
| --- | --- | --- | --- | --- |
| **Plats-till-plats-anslutning   (S2S)** |Principbaserad VPN-konfiguration |RoutBaserad VPN-konfiguration |RoutBaserad VPN-konfiguration |RoutBaserad VPN-konfiguration |
| **Punkt-till-plats-anslutning (P2S**) |Stöds inte |Stöds (Kan samexistera med S2S) |Stöds (Kan samexistera med S2S) |Stöds (Kan samexistera med S2S) |
| **Autentiseringsmetod** |I förväg delad nyckel |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |
| **Maximalt antal S2S-anslutningar** |1 |10 |10 |30 |
| **Maximalt antal P2S-anslutningar** |Stöds inte |128 |128 |128 |
| **Aktivt routningsstöd (BGP)** (*) |Stöds inte |Stöds inte |Stöds |Stöds |

  (*) BGP stöds inte för den klassiska distributionsmodellen.
