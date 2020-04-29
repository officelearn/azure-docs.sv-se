---
title: Azure CLI-exempel – nätverk
description: Azure CLI-exempel
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: 84754a61bfe9537e928759aefbcb5dcddce33089
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457968"
---
# <a name="azure-cli-samples-for-networking"></a>Azure CLI-exempel för nätverk

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|-|-|
|**Anslutning mellan Azure-resurser**||
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverks trafik till klient dels under nätet är begränsad till HTTP, HTTPS och SSH. Utgående trafik till Internet från Server dels under nätet är inte tillåten. |
|**Belastnings utjämning och trafik riktning**||
| [Belastningsutjämna flera webbplatser på virtuella datorer](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar två virtuella datorer med flera IP-konfigurationer, anslutna till en Azures tillgänglighets uppsättning, tillgänglig via en Azure Load Balancer. |
| [Direkt trafik över flera regioner för hög program tillgänglighet](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Skapar två App Service-planer, två webbappar, en Traffic Manager-profil och två Traffic Manager-slutpunkter. |
| | |
