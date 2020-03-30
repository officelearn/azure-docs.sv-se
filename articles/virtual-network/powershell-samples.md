---
title: Azure PowerShell-exempel för virtuellt nätverk
description: Azure PowerShell-exempel för virtuellt nätverk.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: de752cdacf17193d5be95b2b9f887938ace2d50f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74091876"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Azure PowerShell-exempel för virtuellt nätverk

Följande tabell innehåller länkar till Azure PowerShell-skript:

| | |
|----|----|
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP, medan trafik till serverdelsundernätet är begränsad till SQL, port 1433. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP och HTTPS. Utgående trafik till Internet från serverdelsundernätet är inte tillåten. |
|[Konfigurera virtuellt IPv4 + IPv6-virtuellt nätverk med dubbla staplar med enkel belastningsutjämning](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Distribuerar virtuella nätverk med dubbla stackar (IPv4+IPv6) med två virtuella datorer och en Azure Basic Load Balancer med offentliga IP-adresser iPv4 och IPv6. |
|[Konfigurera virtuellt IPv4 + IPv6-virtuellt nätverk med dubbla staplar med standardbelastningsbalanser](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Distribuerar virtuella nätverk med dubbla stackar (IPv4+IPv6) med två virtuella datorer och en Azure Standard Load Balancer med offentliga IP-adresser iPv4 och IPv6. |
