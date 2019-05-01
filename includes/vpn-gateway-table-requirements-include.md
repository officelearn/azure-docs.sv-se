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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "64859068"
---
I följande tabell visas kraven för principbaserade och RouteBased VPN-gatewayer. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen. För den klassiska modellen PolicyBased VPN-gatewayer är detsamma som statiska gatewayer och ruttbaserade gatewayer är detsamma som dynamiska gatewayer.

|  | **Principbaserad Basic VPN Gateway** | **Routningsbaserad Basic VPN Gateway** | **Standard för RouteBased VPN-Gateway** | **VPN-Gateway för RouteBased hög prestanda** |
| --- | --- | --- | --- | --- |
| **Plats-till-plats-anslutning (S2S)** |PolicyBased VPN-konfiguration |RouteBased VPN-konfiguration |RouteBased VPN-konfiguration |RouteBased VPN-konfiguration |
| **Punkt-till-plats-anslutning (P2S**) |Stöds inte |Stöds (Kan samexistera med S2S) |Stöds (Kan samexistera med S2S) |Stöds (Kan samexistera med S2S) |
| **Autentiseringsmetod** |I förväg delad nyckel |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |
| **Maximalt antal S2S-anslutningar** |1 |10 |10 |30 |
| **Maximalt antal P2S-anslutningar** |Stöds inte |128 |128 |128 |
| **Stöd för aktiv routning (BGP)** (*) |Stöds inte |Stöds inte |Stöds |Stöds |

  (*) BGP stöds inte för den klassiska distributionsmodellen.
