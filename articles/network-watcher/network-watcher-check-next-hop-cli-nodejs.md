---
title: Sök efter nästa hopp med Azure Network Watcher nexthop - Azure CLI 1.0 | Microsoft Docs
description: Den här artikeln beskriver hur du kan hitta nästa hopptyp är och ip-adressen med nästa hopp med Azure CLI.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e849f7952962d177f40ce99307ef1c305e089827
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2018
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-10"></a>Ta reda på vilka nästa hopptyp är med nästa hopp-funktionen i Azure Nätverksbevakaren använder Azure CLI 1.0

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST-API](network-watcher-check-next-hop-rest.md)

Nästa hopp är en funktion i Nätverksbevakaren som tillhandahåller möjligheten get nästa hopptyp och IP-adress baserat på en angiven virtuell dator. Den här funktionen är användbart för att fastställa om trafik som lämnar en virtuell dator som passerar en gateway, internet eller virtuella nätverk för att komma till sin destination.

Den här artikeln använder plattformsoberoende Azure CLI version 1.0, som är tillgänglig för Windows, Mac och Linux.

## <a name="before-you-begin"></a>Innan du börjar

I detta scenario använder du Azure CLI för att hitta nästa hopptyp och IP-adress.

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren. Det här scenariot förutsätter att det finns en resursgrupp med en giltig virtuell dator som ska användas.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln använder nästa hopp, en funktion i Nätverksbevakaren som söker efter nästa hopptyp och IP-adress för en resurs. Läs mer om nästa hopp [nästa hopp översikt](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Hämta nästa hopp

Att hämta nästa hopp som vi kallar det `azure netowrk watcher next-hop` cmdlet. Vi skickar cmdlet resursgruppen Nätverksbevakaren, NetworkWatcher, virtuella Id, källans IP-adress och mål-IP-adress. I det här exemplet är den IP-adressen till en virtuell dator i ett annat virtuellt nätverk. Det finns en virtuell nätverksgateway mellan de två virtuella nätverk. 

```azurecli
azure network watcher next-hop -g resourceGroupName -n networkWatcherName -t targetResourceId -a <source-ip> -d <destination-ip>
```

> [!NOTE]
Om den virtuella datorn har flera nätverkskort och IP-vidarebefordring är aktiverat på något av nätverkskorten och NIC-parameter (-i nic-id) måste anges. Annars är valfria.

## <a name="review-results"></a>Granska resultatet

När du är färdig tillhandahålls resultaten. Nästa hopp IP-adress returneras samt typ av resurs som det är.

```
data:    Next Hop Ip Address             : 10.0.1.2
info:    network watcher next-hop command OK
```

I följande lista visas de tillgängliga värdena för NextHopType:

**Nästa Hopptyp**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Ingen

## <a name="next-steps"></a>Nästa steg

Lär dig hur du granskar din grupp för nätverkssäkerhet via programmering genom att besöka [NSG granskning med Nätverksbevakaren](network-watcher-nsg-auditing-powershell.md)
