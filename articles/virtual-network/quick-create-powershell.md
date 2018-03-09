---
title: "Skapa ett virtuellt nätverk i Azure - PowerShell | Microsoft Docs"
description: "Snabbt lära dig skapa ett virtuellt nätverk med PowerShell. Ett virtuellt nätverk gör det möjligt för flera olika typer av Azure-resurser till privat kommunicera med varandra."
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
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 22fcdbda85f3ea336c3926e04d408935ed069c25
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Skapa ett virtuellt nätverk med PowerShell

I den här artikeln får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket för att testa privata nätverkskommunikation mellan dem.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt i den här artikeln kräver AzureRM PowerShell Modulversion 5.1.1 eller senare. Du hittar den installerade versionen genom att köra ` Get-Module -ListAvailable AzureRM`. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*. Alla Azure-resurser skapas inom en Azure-plats (eller en region).

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk för standard heter *myVirtualNetwork* i den *EastUS* plats:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

Alla virtuella nätverk har en eller flera adressprefix tilldelad. Adressutrymmet anges i CIDR-notering. Adressutrymme 10.0.0.0/24 omfattar 10.0.0.0-10.0.0.254. Virtuella nätverk har noll eller flera undernät i dem. Resurser har distribuerats i ett undernät i ett virtuellt nätverk. 

Skapa en konfiguration av undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Alla undernät har ett adressprefix som finns inom adressprefix för det virtuella nätverket. I det här exemplet skapas en konfiguration av undernät med samma adressprefix som det virtuella nätverket adressprefix:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Även om adressprefixet undernät omfattar 10.0.0.0-10.0.0.254 kan endast adresser 10.0.0.4-10.0.0.254 finns, eftersom Azure reserverar de första fyra adresserna (0-3) och den sista adressen i varje undernät. Eftersom undernät adressprefixet är samma som det virtuella nätverk adressprefixet, kan bara ett undernät finnas i det här virtuella nätverket.

Skriva undernät-konfigurationen till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), vilket skapar undernätet:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="test-network-communication"></a>Testa nätverkskommunikation

Ett virtuellt nätverk gör det möjligt för flera typer av Azure-resurser till privat kommunicera med varandra. En typ av resurs som du kan distribuera till ett virtuellt nätverk är en virtuell dator. Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera privata kommunikationen mellan dem i ett senare steg.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn. Platsen som en virtuell dator skapas i måste vara på samma plats som det virtuella nätverket finns i. Den virtuella datorn är inte nödvändigt att vara i samma resursgrupp som den virtuella datorn, även om det är i den här artikeln. Den `-AsJob` parameter gör det möjligt för kommandot att köras i bakgrunden så du kan fortsätta med nästa uppgift.

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

Azure DHCP tilldelar automatiskt 10.0.0.4 till den virtuella datorn när den skapas, eftersom det är den första tillgängliga adressen i den *standard* undernät.

Skapa en andra virtuell dator. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
Det tar några minuter att skapa den virtuella datorn. När du skapat returnerar Azure utdata om den skapade virtuella datorn. Även om inte returnerade resultatet är Azure tilldelas *10.0.0.5* till den *myVm2* virtuella datorn eftersom den var den nästa tillgängliga adressen i undernätet.

### <a name="connect-to-a-virtual-machine"></a>Ansluta till en virtuell dator

Använd den [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) kommando för att returnera den offentliga IP-adressen för en virtuell dator. Azure tilldelar en offentliga Internet dirigerbara IP-adress till varje virtuell dator, som standard. Offentlig IP-adress har tilldelats den virtuella datorn från en [adresspool som tilldelats varje Azure-region](https://www.microsoft.com/download/details.aspx?id=41653). Medan Azure vet vilken offentliga IP-adress har tilldelats en virtuell dator, har operativsystemet som körs på en virtuell dator inga medvetenhet om någon offentlig IP-adress som tilldelats. I följande exempel returneras den offentliga IP-adressen för den *myVm1* virtuell dator:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbords-session med den *myVm1* virtuell dator från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returneras från det föregående kommandot.

```
mstsc /v:<publicIpAddress>
```

En Remote Desktop Protocol (RDP)-fil skapas, hämtas till datorn och öppnas. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn och klicka sedan på **OK**. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

### <a name="validate-communication"></a>Validera kommunikation

Försök att pinga en Windows virtuell dator misslyckas eftersom ping inte tillåts via Windows-brandväggen som standard. Att tillåta ping till *myVm1*, anger du följande kommando från en kommandotolk:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Validera kommunikation med *myVm2*, anger du följande kommando från en kommandotolk den *myVm1* virtuella datorn. Ange de autentiseringsuppgifter som du använde när du skapade den virtuella datorn och slutföra anslutningen:

```
mstsc /v:myVm2
```

Anslutning till fjärrskrivbord är lyckas eftersom både virtuella datorer har den privata IP-adresser tilldelas från den *standard* undernät och eftersom fjärrskrivbord är öppen via Windows-brandväggen som standard. Du kan ansluta till *myVm2* av värdnamn eftersom automatiskt ger DNS-namnmatchning för alla värdar inom ett virtuellt nätverk i Azure. Från en kommandotolk pinga min *myVm1*, från *myVm2*.

```
ping myvm1
```

Ping lyckas eftersom du tillåts via Windows-brandväggen på den *myVm1* virtuell dator i föregående steg. För att bekräfta utgående kommunikation till Internet, anger du följande kommando:

```
ping bing.com
```

Du får fyra svar från bing.com. Som standard kan en virtuell dator i ett virtuellt nätverk kommunicera utgående till Internet.

Avsluta fjärrskrivbords-sessionen. 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser som den innehåller:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en standard virtuellt nätverk med ett undernät i den här artikeln. Mer information om hur du skapar en anpassad virtuellt nätverk med flera undernät, även fortsättningsvis självstudierna för att skapa egna virtuella nätverk.

> [!div class="nextstepaction"]
> [Skapa en anpassad virtuellt nätverk](virtual-networks-create-vnet-arm-ps.md)
