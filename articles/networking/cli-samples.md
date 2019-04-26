---
title: Azure CLI-exempel | Microsoft Docs
description: Azure CLI-exempel
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: c612cdefa3f915212c43d7bdc4d48b10a593150b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60426080"
---
# <a name="azure-cli-samples-for-networking"></a>Azure CLI-exempel för nätverk

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|-|-|
|**Anslutning mellan Azure-resurser**||
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP, HTTPS och SSH. Utgående trafik till Internet från backend-undernät tillåts inte. |
|**Läsa in belastningsutjämning och traffic riktning**||
| [Belastningsutjämna trafik till virtuella datorer för hög tillgänglighet](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar flera virtuella datorer med hög tillgänglighet och belastningsutjämnade konfiguration. |
| [Balansera belasntning för flera webbplatser på virtuella datorer](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar två virtuella datorer med flera IP-konfigurationer, ansluten till en Azure-Tillgänglighetsuppsättning är tillgängliga via en Azure Load Balancer. |
| [Dirigera trafik över flera regioner för hög tillgänglighet](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Skapar två apptjänstplaner, två webbappar, en traffic manager-profil och två traffic manager-slutpunkter. |
| | |
