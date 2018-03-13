---
title: "Skapa ett Azure-nätverk – PowerShell | Microsoft Docs"
description: "Snabbt lära dig skapa ett virtuellt nätverk med PowerShell. Ett virtuellt nätverk gör det möjligt för Azure-resurser, till exempel virtuella datorer, privat kommunicera med varandra och med internet."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 13d36e6861a30473e6cb5d54d94a3c23a1e4cc59
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Skapa ett virtuellt nätverk med PowerShell

Ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer (VM), privat kommunicera med varandra och med internet. I den här artikeln får du lära dig hur du skapar ett virtuellt nätverk. När du skapar ett virtuellt nätverk kan distribuera du två virtuella datorer i det virtuella nätverket. Sedan ansluter till en virtuell dator från internet och kommunicera privat mellan de två virtuella datorerna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt i den här artikeln kräver AzureRM PowerShell Modulversion 5.4.1 eller senare. Du hittar den installerade versionen genom att köra ` Get-Module -ListAvailable AzureRM`. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du kan skapa ett virtuellt nätverk, måste du skapa en resursgrupp med det virtuella nätverket. Skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk för standard heter *myVirtualNetwork* i den *EastUS* plats:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Azure-resurser har distribuerats till ett undernät i ett virtuellt nätverk, så du behöver skapa ett undernät. Skapa en konfiguration av undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Skriva undernät-konfigurationen till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), vilket skapar undernät i det virtuella nätverket:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [nya AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). När du kör det kommando som följer tillfrågas du om autentiseringsuppgifter. De värden som du anger är konfigurerade som användarnamn och lösenord för den virtuella datorn. Den `-AsJob` alternativet skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Utdata som liknar följande exempel utdata returneras och Azure börjar skapa den virtuella datorn i bakgrunden.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn 

Ange följande kommando:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med nästa steg förrän föregående kommando kör och resultatet returneras till PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Anslut till en virtuell dator från internet

Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) att returnera offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den *myVm1* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ersätt `<publicIpAddress>` i följande kommando, med den offentliga IP-adress returnerades från föregående kommando och ange följande kommando: 

```
mstsc /v:<publicIpAddress>
```

En Remote Desktop Protocol (RDP)-fil skapas och hämtas till datorn. Öppna hämtade rdp-filen. Välj **Anslut**. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn. Du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn. Välj **OK**. Du kan få en certifikatvarning under inloggningen. Om varningen, väljer **Ja** eller **Fortsätt**, för att fortsätta med anslutningen.

## <a name="communicate-privately-between-vms"></a>Privat kommunikation mellan virtuella datorer

Från PowerShell på de *myVm1* VM, ange `ping myvm2`. Ping misslyckas, eftersom ping använder kontroll meddelandet ICMP (internet protocol) och ICMP är inte tillåtet via Windows-brandväggen som standard.

Att tillåta *myVm2* pinga *myVm1* i ett senare steg, anger du följande kommando från PowerShell, vilket gör att ICMP inkommande via Windows-brandväggen:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Stäng fjärrskrivbordsanslutning till *myVm1*. 

Utför stegen i [Anslut till en virtuell dator från internet](#connect-to-a-vm-from-the-internet) igen, men ansluter till *myVm2*. 

Från en kommandotolk på den *myVm2* VM, ange `ping myvm1`.

Du får svar från *myVm1*, eftersom ICMP tillåts via Windows-brandväggen på den *myVm1* VM i föregående steg.

Stäng fjärrskrivbordsanslutning till *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) att ta bort resursgruppen och alla resurser som den innehåller:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln får skapat du ett virtuellt standardnätverk och två virtuella datorer. Du ansluten till en virtuell dator från Internet och kommunicerade privat mellan den virtuella datorn och en annan virtuell dator. Läs mer om inställningar för virtuellt nätverk i [hantera ett virtuellt nätverk](manage-virtual-network.md). 

Standard Azure tillåter obegränsad privat kommunikation mellan virtuella datorer, men tillåter bara inkommande anslutningar till fjärrskrivbord till virtuella Windows-datorer från Internet. Om du vill lära dig mer om att tillåta eller begränsa olika typer av kommunikation till och från virtuella datorer, går du vidare till nästa kurs.

> [!div class="nextstepaction"]
> [Filtrera nätverkstrafik](virtual-networks-create-nsg-arm-ps.md)
