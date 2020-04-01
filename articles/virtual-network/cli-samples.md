---
title: Azure CLI-exempel för virtuellt nätverk
description: Azure CLI-exempel för virtuellt nätverk.
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
ms.openlocfilehash: 5ce10cf37b61b0269e6c8f2279b8814d9dc4a4f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78271210"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Azure CLI-exempel för virtuellt nätverk

Följande tabell innehåller länkar till bash-skript med Azure CLI-kommandon:

| | |
|----|----|
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP, HTTPS och SSH. Utgående trafik till Internet från serverdelsundernätet är inte tillåten. |
|[Konfigurera virtuellt IPv4 + IPv6-virtuellt nätverk med dubbla staplar med enkel belastningsutjämning](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Distribuerar virtuella nätverk med dubbla stackar (IPv4+IPv6) med två virtuella datorer och en Azure Basic Load Balancer med offentliga IP-adresser iPv4 och IPv6. |
|[Konfigurera virtuellt IPv4 + IPv6-virtuellt nätverk med dubbla staplar med standardbelastningsbalanser](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Distribuerar virtuella nätverk med dubbla stackar (IPv4+IPv6) med två virtuella datorer och en Azure Standard Load Balancer med offentliga IP-adresser iPv4 och IPv6. |
|[Självstudiekurs: Skapa och testa en NAT-gateway – Azure CLI](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Skapa och validera en NAT-gateway med hjälp av en virtuell dator för källa och mål. |