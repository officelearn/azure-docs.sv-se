---
title: Azure PowerShell exempel – nätverk
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459125"
---
# <a name="azure-powershell-samples-for-networking"></a>Azure PowerShell exempel för nätverk

Följande tabell innehåller länkar till skript som skapats med Azure PowerShell.

| | |
|-|-|
|**Anslutning mellan Azure-resurser**||
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP, medan trafik till serverdelsundernätet är begränsad till SQL, port 1433. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverks trafik till klient dels under nätet är begränsad till HTTP och HTTPS.. Utgående trafik till Internet från Server dels under nätet är inte tillåten. |
|**Belastnings utjämning och trafik riktning**||
| [Belastningsutjämna trafik till virtuella datorer för hög tillgänglighet](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar flera virtuella datorer i en hög tillgänglig och belastningsutjämnad konfiguration. |
| [Direkt trafik över flera regioner för hög program tillgänglighet](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Skapar två App Service-planer, två webbappar, en Traffic Manager-profil och två Traffic Manager-slutpunkter. |
| | |
