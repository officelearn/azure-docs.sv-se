---
title: Azure PowerShell-exempel för virtuellt nätverk | Microsoft Docs
description: Azure PowerShell-exempel för virtuellt nätverk.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: ab3a0935f23cda70cfef49af3563f6b1e85d1d8b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Azure PowerShell-exempel för virtuellt nätverk

Följande tabell innehåller länkar till Azure PowerShell-skript:

| | |
|----|----|
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP, medan trafik till serverdelsundernätet är begränsad till SQL, port 1433. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP och HTTPS. Utgående trafik till Internet från serverdelsundernätet är inte tillåten. |
