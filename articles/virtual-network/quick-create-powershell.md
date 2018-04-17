---
title: Skapa ett virtuellt nätverk – snabbstart – Azure PowerShell | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal. Ett virtuellt nätverk gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera privat med varandra och med Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 3d4c8e130f96c1b89247fe0c092363c33032ec3d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av PowerShell

Ett virtuellt nätverk gör det möjligt för Azure-resurser, till exempel virtuella datorer (VM), att kommunicera privat med varandra och med Internet. I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket. Sedan ansluter du till en virtuell dator från Internet och kommunicerar privat mellan de två virtuella datorerna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här snabbstarten version 5.4.1 eller senare av AzureRM PowerShell-modulen. Kör ` Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du kan skapa ett virtuellt nätverk måste du skapa en resursgrupp som kan innehålla det virtuella nätverket. Skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt standardnätverk med namnet *myVirtualNetwork* på platsen *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Azure-resurser distribueras till ett undernät i ett virtuellt nätverk, så du behöver skapa ett undernät. Skapa en undernätskonfiguration med [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Skriv undernätskonfigurationen till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), som skapar undernätet i det virtuella nätverket:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). När du kör kommandot nedan uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn. Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Utdata som liknar följande exempelutdata returneras, och Azure börjar skapa den virtuella datorn i bakgrunden.

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

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med nästa steg förrän föregående kommando har körts och utdata har returnerats till PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) för att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den virtuella datorn *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ersätt `<publicIpAddress>` i följande kommando med den offentliga IP-adressen som returnerades från föregående kommando och ange sedan följande kommando: 

```
mstsc /v:<publicIpAddress>
```

En RDP-fil (Remote Desktop Protocol) skapas och laddas ned till datorn. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **More choices** (Fler alternativ) och sedan **Use a different account** (Använd ett annat konto) för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn. Välj **OK**. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

Från PowerShell på den virtuella datorn *myVm1* anger du `ping myvm2`. Ping misslyckas, eftersom ping som standard använder ICMP (Internet Control Message Protocol) och ICMP inte är tillåtet via Windows-brandväggen.

För att tillåta *myVm2* att pinga *myVm1* i ett senare steg anger du följande kommando från PowerShell, som tillåter inkommande ICMP via Windows-brandväggen:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Stäng fjärrskrivbordsanslutningen till *myVm1*. 

Utför stegen i [Ansluta till en virtuell dator från Internet](#connect-to-a-vm-from-the-internet) igen, men anslut till *myVm2*. 

Från en kommandotolk på den virtuella datorn *myVm2* anger du `ping myvm1`.

Du får svar från *myVm1*, eftersom du har tillåtit ICMP via Windows-brandväggen på den virtuella datorn *myVm1* i ett tidigare steg.

Stäng fjärrskrivbordsanslutningen till *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när de inte längre behövs:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett virtuellt standardnätverk och två virtuella datorer. Du har anslutit till en virtuell dator från Internet och kommunicerat privat mellan den virtuella datorn och en annan virtuell dator. Läs mer om virtuella nätverksinställningar i [Manage a virtual network](manage-virtual-network.md) (Hantera ett virtuellt nätverk). 

Som standard tillåter Azure obegränsad privat kommunikation mellan virtuella datorer, men tillåter endast inkommande fjärrskrivbordsanslutningar till virtuella Windows-datorer från Internet. Om du vill lära dig mer om hur du tillåter eller begränsar olika typer av nätverkskommunikation till och från virtuella datorer fortsätter du till självstudien [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md).
