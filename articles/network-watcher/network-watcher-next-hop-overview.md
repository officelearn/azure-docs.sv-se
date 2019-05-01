---
title: Introduktion till nästa hopp i Azure Network Watcher | Microsoft Docs
description: Den här artikeln innehåller en översikt över Network Watcher nästa hopp-funktionen.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: eb613455ccd6b6630d0d75b5a8458f812bb36846
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730219"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Använd nästa hopp för att diagnostisera problem med Routning-VM

Trafik från en virtuell dator (VM) som skickas till ett mål baserat på de effektiva vägarna som är associerade med ett nätverksgränssnitt (NIC). Nästa hopp hämtar nästa hopptyp och IP-adress för ett paket från en viss virtuell dator och nätverkskort. Att känna till nästa hopp hjälper dig att avgöra om trafik dirigeras till det avsedda målet, eller om trafiken som skickas långt. En felaktig konfigurering av vägar, där trafiken börjar dirigeras till en lokal plats eller en virtuell installation, kan leda till problem med nätverksanslutningen. Nästa hopp returnerar också routningstabellen som är associerad med nästa hopp. Om vägen har definierats som en användardefinierad väg, returneras det flödet. Nästa hopp i annat fall returnerar **Systemväg**.

![Nästa hopp-översikt](./media/network-watcher-next-hop-overview/figure1.png)

Nästa hopp som kan returneras av funktionen nästa hopp är följande:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Ingen

Läs mer om varje nästa hopptyp i [routningsöversikten](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Läs hur du använder nästa hopp för att diagnostisera problem med Virtuella nätverk routning i diagnostisera VM network routning problem med att använda den [Azure-portalen](diagnose-vm-network-routing-problem.md), [PowerShell](diagnose-vm-network-routing-problem-powershell.md), eller [Azure CLI](diagnose-vm-network-routing-problem-cli.md).
