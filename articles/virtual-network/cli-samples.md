---
title: Azure CLI-exempel för virtuellt nätverk | Microsoft Docs
description: Azure CLI-exempel för virtuellt nätverk.
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
ms.openlocfilehash: baa4dbc8f95e068eb1a939fdee53fb2a4ee8117f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="azure-cli-samples-for-virtual-network"></a>Azure CLI-exempel för virtuellt nätverk

Följande tabell innehåller länkar till bash-skript med Azure CLI-kommandon:

| | |
|----|----|
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP, HTTPS och SSH. Utgående trafik till Internet från serverdelsundernätet är inte tillåten. |