---
title: Exempel på Azure PowerShell – nätverk
description: Azure PowerShell-exempel
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: ca6ac145db0536d3cf7e5bcc72a58d72101ab12a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459125"
---
# <a name="azure-powershell-samples-for-networking"></a>Azure PowerShell-exempel för nätverk

Följande tabell innehåller länkar till skript som skapats med Azure PowerShell.

| | |
|-|-|
|**Anslutning mellan Azure-resurser**||
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP, medan trafik till serverdelsundernätet är begränsad till SQL, port 1433. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till frontend-undernätet är begränsad till HTTP och HTTPS.. Det är inte tillåtet att ta ut utgående trafik till Internet från backend-undernätet. |
|**Belastningsutjämning och trafikriktning**||
| [Belastningsutjämna trafik till virtuella datorer för hög tillgänglighet](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar flera virtuella datorer i en högtillgänglig och belastningsbalanserad konfiguration. |
| [Direkt trafik över flera regioner för hög programtillgänglighet](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Skapar två apptjänstplaner, två webbappar, en traffic manager-profil och två slutpunkter för Trafikhanteraren. |
| | |
