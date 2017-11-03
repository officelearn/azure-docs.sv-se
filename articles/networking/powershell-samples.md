---
title: Azure PowerShell-exempel | Microsoft Docs
description: Azure PowerShell-exempel
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: georgewallace
ms.openlocfilehash: 0bca4fb6874bd265f0ae9faeb4219abeb4ffb6d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-powershell-samples-for-networking"></a>Azure PowerShell-exempel för nätverk

Följande tabell innehåller länkar till skript som skapats med hjälp av Azure PowerShell.

| | |
|-|-|
|**Anslutning mellan Azure-resurser**||
| [Skapa ett virtuellt nätverk för flera nivåer program](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med frontend och backend-undernät. Trafik till undernätet för frontend är begränsad till HTTP, medan trafik till backend-undernät är begränsad till SQL, port 1433. |
| [Peer-två virtuella nätverk](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar och ansluter två virtuella nätverk i samma region. |
| [Vidarebefordra trafik via en virtuell nätverksenhet](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med frontend och backend-undernät och en virtuell dator som kan vidarebefordra trafik mellan två undernät. |
| [Filtrera inkommande och utgående nätverkstrafik för VM](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med frontend och backend-undernät. Inkommande nätverkstrafik till undernätet frontend är begränsad till HTTP och HTTPS... Utgående trafik till Internet från backend-undernät är inte tillåtet. |
|**Läsa in belastningsutjämning och trafik riktning**||
| [Läs in Utjämna trafiken till virtuella datorer för hög tillgänglighet](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar flera virtuella datorer i en hög tillgänglighet och konfiguration för Utjämning av nätverksbelastning. |
| [Belastningsutjämning för flera webbplatser på virtuella datorer](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar två virtuella datorer med flera IP-konfigurationer, ansluten till en Azure Tillgänglighetsuppsättning, tillgängligt via en Azure belastningsutjämnare. |
| [Direkt-trafik över flera regioner för hög tillgänglighet](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Skapar två apptjänstplaner, två webbappar, en traffic manager-profil och två traffic manager-slutpunkter. |
| | |
