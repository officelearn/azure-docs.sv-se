---
title: Azure PowerShell-exempel för virtuellt nätverk | Microsoft Docs
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
ms.date: 04/17/2019
ms.author: kumud
ms.openlocfilehash: e10f7ad4ae8995d9efffec97a37dc64b28fa37b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688028"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Azure PowerShell-exempel för virtuellt nätverk

Följande tabell innehåller länkar till Azure PowerShell-skript:

| | |
|----|----|
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP, medan trafik till serverdelsundernätet är begränsad till SQL, port 1433. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP och HTTPS. Utgående trafik till Internet från serverdelsundernätet är inte tillåten. |
|[Konfigurera IPv4 + IPv6 dual stack virtuellt nätverk](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Distribuerar dual-stack (IPv4 + IPv6) virtuellt nätverk med två virtuella datorer och en grundläggande belastningsutjämnare för Azure med IPv4 och IPv6 offentliga IP-adresser. |
