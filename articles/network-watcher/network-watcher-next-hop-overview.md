---
title: Introduktion till nästa hopp i Azure Network Watcher | Microsoft-dokument
description: Den här artikeln innehåller en översikt över nästa hoppkapacitet för Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844066"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Använd nästa hopp för att diagnostisera routningsproblem för virtuella datorer

Trafik från en virtuell dator (VM) skickas till ett mål baserat på de effektiva vägar som är associerade med ett nätverksgränssnitt (NIC). Nästa hopp får nästa hopptyp och IP-adress för ett paket från en viss virtuell dator och nätverkskort. Att veta nästa hopp hjälper dig att avgöra om trafiken dirigeras till den avsedda destinationen, eller om trafiken skickas ingenstans. En felaktig konfiguration av vägar, där trafiken dirigeras till en lokal plats eller en virtuell installation, kan leda till anslutningsproblem. Nästa hopp returnerar också rutttabellen som är associerad med nästa hopp. Om flödet definieras som en användardefinierad väg returneras den vägen. Annars returnerar nästa hopp **Systemrutt .**

![nästa hop översikt](./media/network-watcher-next-hop-overview/figure1.png)

Nästa hopp som kan returneras av nästa hoppkapacitet är följande:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge (microsoftedge)
* Inget

Mer information om varje nästa hopptyp finns i [Översikt över Routning](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder nästa hopp för att diagnostisera problem med vm-nätverksroutning finns i Diagnostisera problem med vm-nätverksroutning med [Azure-portalen,](diagnose-vm-network-routing-problem.md) [PowerShell](diagnose-vm-network-routing-problem-powershell.md)eller [Azure CLI](diagnose-vm-network-routing-problem-cli.md).
