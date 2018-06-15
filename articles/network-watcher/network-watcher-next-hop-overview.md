---
title: Introduktion till nästa hopp i Azure-Nätverksbevakaren | Microsoft Docs
description: Den här artikeln innehåller en översikt över Nätverksbevakaren kapaciteten för nästa hopp.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bbb782e700781dcfedbbd340c7d10db53767b035
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180393"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Använd nästa hopp för att felsöka virtuella routning problem

Trafik från en virtuell dator (VM) som skickas till ett mål som är baserat på de effektiva vägar som är associerad med ett nätverksgränssnitt (NIC). Nästa hopp hämtar nästa hopptyp och IP-adressen för ett paket från en specifik virtuell dator och nätverkskort. Om du vet nexthop hjälper dig att avgöra om trafik omdirigeras till det avsedda målet, eller om trafiken som skickas långt. En felaktig konfiguration av vägar, där trafik dirigeras till en lokal plats eller en virtuell installation, kan leda till problem med nätverksanslutningen. Nästa hopp returnerar också routningstabellen som är associerad med nästa hopp. Om vägen har definierats som en användardefinierad väg, returneras den rutten. Nästa hopp annars returnerar **Systemväg**.

![Nästa hopp-översikt](./media/network-watcher-next-hop-overview/figure1.png)

Nästa hopp som kan returneras av kapaciteten för nästa hopp är följande:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* VnetPeering
* Ingen

Mer information om varje nästa hopptyp finns [routning: översikt](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Information om hur du använder nästa hopp för att diagnostisera VM routning nätverksproblem finns [diagnostisera VM nätverksproblem routning](diagnose-vm-network-routing-problem.md).
