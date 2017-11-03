---
title: Azure CLI-exempel | Microsoft Docs
description: Azure CLI-exempel
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: 7977460f61bfdabd399e45e86d9bbf2e5083992b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples-for-networking"></a>Azure CLI-exempel för nätverk

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|-|-|
|**Anslutning mellan Azure-resurser**||
| [Skapa ett virtuellt nätverk för flera nivåer program](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med frontend och backend-undernät. Trafik till undernätet för frontend är begränsad till http- och SSH, medan trafik till backend-undernät är begränsad till MySQL port 3306. |
| [Peer-två virtuella nätverk](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar och ansluter två virtuella nätverk i samma region. |
| [Vidarebefordra trafik via en virtuell nätverksenhet](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med frontend och backend-undernät och en virtuell dator som kan vidarebefordra trafik mellan två undernät. |
| [Filtrera inkommande och utgående nätverkstrafik för VM](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med frontend och backend-undernät. Inkommande nätverkstrafik till undernätet frontend är begränsad till HTTP, HTTPS och SSH. Utgående trafik till Internet från backend-undernät är inte tillåtet. |
|**Läsa in belastningsutjämning och trafik riktning**||
| [Läs in Utjämna trafiken till virtuella datorer för hög tillgänglighet](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar flera virtuella datorer i en hög tillgänglighet och konfiguration för Utjämning av nätverksbelastning. |
| [Belastningsutjämning för flera webbplatser på virtuella datorer](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar två virtuella datorer med flera IP-konfigurationer, ansluten till en Azure Tillgänglighetsuppsättning, tillgängligt via en Azure belastningsutjämnare. |
| [Direkt-trafik över flera regioner för hög tillgänglighet](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Skapar två apptjänstplaner, två webbappar, en traffic manager-profil och två traffic manager-slutpunkter. |
| | |
