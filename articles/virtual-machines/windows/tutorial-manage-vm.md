---
title: Skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen | Microsoft Docs
description: "Självstudiekurs – skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2237f2e5cb67df019d0975e764602babe7f4c8f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen

Virtuella datorer i Azure ger en fullständigt konfigurerbara och flexibel datormiljö. Den här kursen ingår grundläggande virtuella Azure-datorn distribution objekt, till exempel välja en VM-storlek, välja en VM-avbildning och distribuera en virtuell dator. Lär dig att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välj och Använd VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuell dator


[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas innan en virtuell dator. I det här exemplet en resursgrupp med namnet *myResourceGroupVM* skapas i den *EastUS* region. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

Resursgruppen har angetts när du skapar eller ändrar en VM som kan ses i hela den här kursen.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

En virtuell dator måste vara ansluten till ett virtuellt nätverk. Du kan kommunicera med den virtuella datorn med en offentlig IP-adress via ett nätverkskort.

### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>Skapa offentlig IP-adress

Skapa en offentlig IP-adress med [ny AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>Skapa nätverkskort

Skapa ett nätverkskort med [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp

En Azure [nätverkssäkerhetsgruppen](../../virtual-network/virtual-networks-nsg.md) (NSG) styr inkommande och utgående trafik för en eller flera virtuella datorer. Regler för nätverkssäkerhetsgrupper Tillåt eller neka nätverkstrafik på en specifik port eller ett intervall. De här reglerna kan också inkludera en källadress-prefix så att endast trafik i en fördefinierad källa kan kommunicera med en virtuell dator. För att komma åt IIS-webbserver som du installerar måste du lägga till en regel för inkommande NSG.

Så här skapar du en regel för inkommande NSG [Lägg till AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig). I följande exempel skapas en NSG regeln med namnet *myNSGRule* som öppnar port *3389* för den virtuella datorn:

```azurepowershell-interactive
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

Skapa en NSG med hjälp av *myNSGRule* med [ny AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

Lägg till NSG: N till undernät i det virtuella nätverket med [Set AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

Uppdatera det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```azurepowershell-interactive
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

När du skapar en virtuell dator, är flera alternativ tillgängliga, till exempel operativsystemavbildningen, storlek och administrativa autentiseringsuppgifter för disken. I det här exemplet skapas en virtuell dator med namnet *myVM* kör den senaste versionen av Windows Server 2016 Datacenter.

Ange användarnamn och lösenord för administratörskontot på den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa den första konfigurationen för den virtuella datorn med [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig):

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Lägg till information om operativsystemet i konfigurationen av virtuella datorn med [Set AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem):

```azurepowershell-interactive
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Lägga till bilden i konfigurationen av virtuella datorn med [Set AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage):

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

Lägg till disk operativsysteminställningar i konfigurationen av virtuella datorn med [Set AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk):

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

Lägg till nätverkskortet som du skapade tidigare till konfigurationen av virtuella datorn med [Lägg till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```azurepowershell-interactive
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>Ansluta till en virtuell dator

När distributionen har slutförts kan du skapa en fjärrskrivbordsanslutning med den virtuella datorn.

Kör följande kommandon för att returnera den offentliga IP-adressen för den virtuella datorn. Anteckna denna IP-adress så att du kan ansluta till den till din webbläsare för att testa webbanslutningen i ett framtida steg.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

Använd följande kommando på den lokala datorn för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med *publicIPAddress* för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>Förstå VM-avbildningar

Azure marketplace innehåller många avbildningar av virtuella datorer som kan användas för att skapa en ny virtuell dator. I de föregående stegen skapades en virtuell dator med Windows Server 2016-Datacenter-avbildning. I det här steget används i PowerShell-modulen för att söka på marketplace för andra Windows-avbildningar kan också som bas för nya virtuella datorer. Den här processen består av att söka efter utgivare, erbjudande och Avbildningsnamnet (Sku). 

Använd den [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) kommando för att returnera en lista över avbildningen utgivare.  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Använd den [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) att returnera en lista med bild erbjudanden. Den returnerade listan filtreras på den angivna utgivaren med det här kommandot. 

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

Den [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) kommandot filtrerar sedan på namnet på utgivaren och erbjudandet att returnera en lista över namn på bilder.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Den här informationen kan användas för att distribuera en virtuell dator med en viss bild. Det här exemplet anger avbildningens namn på det Virtuella datorobjektet. I föregående exempel finns i den här självstudiekursen fullständig distributionssteg.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>Förstå VM-storlekar

Storlek på en virtuell dator bestämmer hur mycket av beräkningsresurser som Processorn och GPU-minne som är tillgängliga för den virtuella datorn. Virtuella datorer måste skapas med lämplig storlek för förväntat arbetsbelastning. Om belastningen ökar, kan en befintlig virtuell dator ändras.

### <a name="vm-sizes"></a>VM-storlekar

I följande tabell kategoriserar storlekar i användningsfall.  

| Typ                     | Storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Generellt syfte         |DSv2, Dv2, DS, D, Av2, A0 7| Belastningsutjämnade CPU-till-minne. Idealiskt för dev / test och små till medelstora lösningar för program och data.  |
| Beräkningsoptimerad      | FS, F             | Hög CPU-till-minne. Bra för medelhög trafik program, nätverksinstallationer och batchprocesser.        |
| Minnesoptimerad       | GS, G, DSv2, DS, Dv2, D   | Hög minne-till-core. Perfekt för relationsdatabaser, medelstora till stora cacheminnen och analyser i minnet.                 |
| Lagringsoptimerad       | Ls                | Högt diskgenomflöde och I/O. Perfekt för stordata, SQL- och NoSQL-databaser.                                                         |
| GPU           | NV NC            | Särskilda virtuella datorer som mål för tunga grafisk återgivning och redigering av video.       |
| Hög prestanda | H, A8-11          | Våra mest kraftfulla CPU virtuella datorer med valfritt hög genomströmning nätverksgränssnitt (RDMA). 


### <a name="find-available-vm-sizes"></a>Hitta tillgängliga storlekar på VM

Om du vill se en lista över storlekar på VM tillgängliga i en viss region, Använd den [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) kommando.

```azurepowershell-interactive
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator

När en virtuell dator har distribuerats, kan den ändras för att öka eller minska resursallokering.

Kontrollera om önskad storlek är tillgängligt på den aktuella virtuella datorns klustret innan du ändrar storlek på en virtuell dator. Den [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) kommando returnerar en lista över storlekar. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

Om önskad storlek är tillgänglig, kan den virtuella datorn ändras från ett slås på tillstånd, men den startas under åtgärden.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

Om önskad storlek inte är i det aktuella klustret måste den virtuella datorn frigörs innan åtgärden Ändra storlek kan ske. Observera att när den virtuella datorn är påslagen tillbaka alla data på disken för temporär tas bort och den offentliga IP-ändra såvida inte en statisk IP-adress används. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>Energisparfunktioner för VM

En virtuell Azure-dator kan ha en av många energisparfunktioner. Det här tillståndet representerar det aktuella tillståndet för den virtuella datorn för hypervisor-programmet. 

### <a name="power-states"></a>Energisparfunktioner

| Energisparläge | Beskrivning
|----|----|
| Startar | Anger den virtuella datorn startas. |
| Körs | Anger att den virtuella datorn körs. |
| Stoppas | Anger att den virtuella datorn har stoppats. | 
| Stoppad | Anger att den virtuella datorn har stoppats. Observera att virtuella datorer i ett stoppat tillstånd fortfarande avgifter beräkning.  |
| Det har frigjorts | Anger att den virtuella datorn har flyttats. |
| Frigöra | Anger att den virtuella datorn är helt tas bort från hypervisor-programmet men fortfarande tillgängliga i kontrollplan. Virtuella datorer med tillståndet Deallocated inte avgifter beräkning. |
| - | Anger att energisparläge för den virtuella datorn är okänt. |

### <a name="find-power-state"></a>Hitta energiläge

Använd för att hämta tillståndet för en viss virtuell dator i [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) kommando. Se till att ange ett giltigt namn för en virtuell dator och resursgruppen. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName myResourceGroupVM `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Resultat:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Administrativa uppgifter

Under livscykeln för en virtuell dator kan du vill köra hanteringsuppgifter, till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa skript för att automatisera repetitiva och komplicerade uppgifter. Med hjälp av Azure PowerShell, kan många vanliga administrativa uppgifter köras från kommandoraden eller i skript.

### <a name="stop-virtual-machine"></a>Stoppa den virtuella datorn

Stoppa och ta bort en virtuell dator med [stoppa AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

Använd parametern - StayProvisioned om du vill behålla den virtuella datorn i ett etablerat tillstånd.

### <a name="start-virtual-machine"></a>Starta den virtuella datorn

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>Ta bort resursgrupp

En resursgrupp också tar du bort alla resurser som ingår i.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
```

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig om grundläggande VM skapande och hantering, till exempel hur du:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välj och Använd VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuell dator

Gå vidare till nästa kurs vill veta mer om Virtuella diskar.  

> [!div class="nextstepaction"]
> [Skapa och hantera Virtuella diskar](./tutorial-manage-data-disk.md)
