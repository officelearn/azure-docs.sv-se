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
ms.openlocfilehash: 5e7e365a73c9ba9cfd91d4f93759e7af55a0ad48
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "30929333"
---
I följande tabell visas kraven för PolicyBased och RouteBased VPN-gatewayer. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen. Den klassiska modellen PolicyBased VPN-gatewayer är detsamma som statiska gatewayer och ruttbaserade gatewayer är detsamma som dynamiska gatewayer.

|  | **PolicyBased grundläggande VPN-Gateway** | **RouteBased grundläggande VPN-Gateway** | **RouteBased Standard VPN-Gateway** | **RouteBased hög prestanda VPN-Gateway** |
| --- | --- | --- | --- | --- |
| **Plats-till-plats-anslutning (S2S)** |PolicyBased VPN-konfiguration |RouteBased VPN-konfiguration |RouteBased VPN-konfiguration |RouteBased VPN-konfiguration |
| **Punkt-till-plats-anslutning (P2S**) |Stöds inte |Stöds (Kan samexistera med S2S) |Stöds (Kan samexistera med S2S) |Stöds (Kan samexistera med S2S) |
| **Autentiseringsmetod** |I förväg delad nyckel |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |
| **Maximalt antal S2S-anslutningar** |1 |10 |10 |30 |
| **Maximalt antal P2S-anslutningar** |Stöds inte |128 |128 |128 |
| **Stöd för aktiv routning (BGP)-** (*) |Stöds inte |Stöds inte |Stöds |Stöds |

  (*) BGP stöds inte för den klassiska distributionsmodellen.
