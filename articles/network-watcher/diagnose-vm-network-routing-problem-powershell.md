---
title: Diagnostisera virtuella routning nätverksproblem - Azure PowerShell | Microsoft Docs
description: I den här artikeln får lära du att diagnostisera virtuella routning nätverksproblem med nästa hopp-funktionen i Azure Nätverksbevakaren.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f793a201b3fbf57ac2f420c4f4e57a230bc11468
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnostisera virtuella routning nätverksproblem - Azure PowerShell

I den här artikeln distribuera en virtuell dator (VM) och kontrollera kommunikationen till IP-adress och URL: en. Du kan fastställa orsaken till ett kommunikationsfel och hur du kan lösa.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt i den här artikeln kräver AzureRM PowerShell Modulversion 5.4.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Innan du kan skapa en virtuell dator, måste du skapa en resursgrupp som innehåller den virtuella datorn. Skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Skapa den virtuella datorn med [nya AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Det tar några minuter att skapa den virtuella datorn. Inte fortsätta med stegen tills den virtuella datorn skapas och PowerShell returnerar utdata.

## <a name="test-network-communication"></a>Testa nätverkskommunikation

Om du vill testa kommunikation med Nätverksbevakaren måste du först aktivera en nätverksbevakaren i den region som den virtuella datorn som du vill testa och sedan använda nätverket Watcher nästa hopp-funktionen för att testa kommunikation.

## <a name="enable-network-watcher"></a>Aktivera nätverksbevakaren

Om du redan har en aktiverad i östra USA nätverksbevakaren använder [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) att hämta nätverksbevakaren. I följande exempel hämtar en befintlig nätverksbevakaren med namnet *NetworkWatcher_eastus* som finns i den *NetworkWatcherRG* resursgrupp:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Om du inte redan har en nätverksbevakaren aktiverat i östra USA kan du använda [ny AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) att skapa en nätverksbevakaren i östra USA:

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Använd nästa hopp

Azure skapar automatiskt vägar till standard mål. Du kan skapa anpassade vägar som åsidosätter standardvägar. Anpassade vägar kan ibland orsaka kommunikation misslyckas. Testa routning från en virtuell dator med den [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) kommando för att avgöra routning nexthop när trafiken är avsedda för en specifik adress.

Testa utgående kommunikation från den virtuella datorn till en IP-adresser för www.bing.com:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Efter några sekunder utdata informerar som den **NextHopType** är **Internet**, och att den **RouteTableId** är **Systemväg**. Det här resultatet kan du vet att det är en giltig väg till målet.

Testa utgående kommunikation från den virtuella datorn till 172.31.0.100:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Utdatan informerar som **ingen** är den **NextHopType**, och att den **RouteTableId** är också **Systemväg**. Det här resultatet kan du vet att det finns en giltig väg till målet, men det finns ingen nästa hopp för att vidarebefordra trafiken till målet.

## <a name="view-details-of-a-route"></a>Visa information om en väg

Granska för att analysera routning mer effektiva vägarna för nätverksgränssnitt med den [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) kommando:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Utdata som innehåller följande text returneras:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Som du ser i föregående utdata vägen med den **AaddressPrefix** av **0.0.0.0/0** dirigerar all trafik som inte är avsedda för adresser inom andra route-adressprefix med nästa hopp för **Internet**. Som du ser också i utdata, men det finns en standardväg till 172.16.0.0/12 prefix, som innehåller 172.31.0.100 adress, den **nextHopType** är **ingen**. Azure skapar en standardväg till 172.16.0.0/12, men anger inte en nästa hopptyp tills det finns en orsak till. Om till exempel att du lagt till adressintervallet 172.16.0.0/12 adressutrymmet för det virtuella nätverket, Azure ändras den **nextHopType** till **för virtuella nätverk** för vägen. En kontroll kan sedan visa **för virtuella nätverk** som den **nextHopType**.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när de inte längre behövs:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator och diagnostiserats nätverksroutning från den virtuella datorn. Du har lärt dig att Azure skapar flera standardvägar och testat routning till två olika mål. Lär dig mer om [routning i Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapa anpassade vägar](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

För utgående anslutningar av virtuell dator, du kan också bestämma svarstiden och för tillåten respektive nekad nätverkstrafiken mellan den virtuella datorn och en slutpunkt som använder nätverket Watcher [anslutning felsöka](network-watcher-connectivity-powershell.md) kapaciteten. Du kan övervaka kommunikation mellan en virtuell dator och en slutpunkt som en IP-adress eller URL, med tiden med Nätverksbevakaren anslutning övervakaren funktionen. Mer information finns i avsnittet [övervaka en nätverksanslutning](connection-monitor.md).