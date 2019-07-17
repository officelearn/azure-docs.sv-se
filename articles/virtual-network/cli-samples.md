---
title: Azure CLI-exempel för virtuellt nätverk | Microsoft Docs
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
ms.openlocfilehash: 4caa19ad22faa0a921fb6397e5b376b65a29e519
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249059"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Azure CLI-exempel för virtuellt nätverk

Följande tabell innehåller länkar till bash-skript med Azure CLI-kommandon:

| | |
|----|----|
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP, HTTPS och SSH. Utgående trafik till Internet från serverdelsundernätet är inte tillåten. |
|[Konfigurera IPv4 + IPv6 dual stack virtuellt nätverk med Basic Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Distribuerar dual-stack (IPv4 + IPv6) virtuellt nätverk med två virtuella datorer och en grundläggande belastningsutjämnare för Azure med IPv4 och IPv6 offentliga IP-adresser. |
|[Konfigurera IPv4 + IPv6 dual stack virtuellt nätverk med Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Distribuerar dual-stack (IPv4 + IPv6) virtuellt nätverk med två virtuella datorer och en Azure Standard Load Balancer med IPv4 och IPv6 offentliga IP-adresser. |