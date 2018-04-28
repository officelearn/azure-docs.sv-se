---
title: Sök efter nästa hopp med Azure Network Watcher nexthop - PowerShell | Microsoft Docs
description: Den här artikeln beskriver hur du kan hitta nästa hopptyp är och ip-adressen med nästa hopp med hjälp av PowerShell.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2018
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Ta reda på vilka nästa hopptyp är med nästa hopp-funktionen i Azure Nätverksbevakaren med hjälp av PowerShell

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST-API](network-watcher-check-next-hop-rest.md)

Nästa hopp är en funktion i Nätverksbevakaren som tillhandahåller möjligheten get nästa hopptyp och IP-adress baserat på en angiven virtuell dator. Den här funktionen är användbart för att fastställa om trafik som lämnar en virtuell dator som passerar en gateway, internet eller virtuella nätverk för att komma till sin destination.

## <a name="before-you-begin"></a>Innan du börjar

I det här scenariot använder du Azure-portalen för att hitta nästa hopptyp och IP-adress.

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren. Det här scenariot förutsätter att det finns en resursgrupp med en giltig virtuell dator som ska användas.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln använder nästa hopp, en funktion i Nätverksbevakaren som söker efter nästa hopptyp och IP-adress för en resurs. Läs mer om nästa hopp [nästa hopp översikt](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Hämta Nätverksbevakaren

Det första steget är att hämta Nätverksbevakaren-instans. Den `$networkWatcher` variabel har skickats till nästa hopp Kontrollera cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>Hämta en virtuell dator

Nästa hopp Returnerar nästa hopp och IP-adressen för nästa hopp från en virtuell dator. Ett Id för en virtuell dator krävs för cmdlet. Om du redan känner till ID: T för den virtuella datorn att använda, kan du hoppa över det här steget.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> Nästa hopp kräver att den Virtuella datorresursen har allokerats för att köras.

## <a name="get-the-network-interfaces"></a>Hämta nätverksgränssnitt

IP-adressen för ett nätverkskort på den virtuella datorn krävs i det här exemplet vi hämta nätverkskort på en virtuell dator. Om du redan känner till IP-adressen som du vill testa på den virtuella datorn, kan du hoppa över det här steget.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Hämta nästa hopp

Nu vi kallar det `Get-AzureRmNetworkWatcherNextHop` cmdlet. Vi skickar cmdlet Nätverksbevakaren, virtuella Id, källans IP-adress och mål-IP-adress. I det här exemplet är den IP-adressen till en virtuell dator i ett annat virtuellt nätverk. Det finns en virtuell nätverksgateway mellan de två virtuella nätverk.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Granska resultatet

När du är färdig tillhandahålls resultaten. Nästa hopp IP-adress returneras samt typ av resurs som det är. I det här scenariot är det offentliga IP-adressen för den virtuella nätverksgatewayen.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
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

















