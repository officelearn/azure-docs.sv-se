---
title: Självstudie – Skapa och hantera virtuella Windows-datorer med Azure PowerShell | Microsoft Docs
description: I den här självstudien lär du dig hur du använder Azure PowerShell för att skapa och hantera virtuella Windows-datorer i Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8eeba3b38e4a78bc33b995ee06f76116601c4d12
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Självstudie: skapa och hantera virtuella Windows-datorer med Azure PowerShell

Med virtuella Azure-datorer får du en fullständigt konfigurerbar och flexibel datormiljö. I den här självstudien beskrivs den grundläggande distributionen av virtuella Azure-datorer, till exempel att välja en VM-storlek, välja en VM-avbildning och distribuera en virtuell dator. Lär dig att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välja och använda VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuella datorer

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt krävs Azure PowerShell-modulen version 5.7.0 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn. I följande exempel skapas en resursgrupp med namnet *myResourceGroupVM* i regionen *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

Resursgruppen som anges när du skapar eller ändrar en virtuell dator visas i hela den här självstudien.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

När du skapar en virtuell dator finns flera tillgängliga alternativ, som t.ex. avbildning av operativsystemet, nätverkskonfiguration och administrativa autentiseringsuppgifter. I det här exemplet skapas en virtuell dator med namnet *myVM*. Den kör som standard den senaste versionen av Windows Server 2016 Datacenter.

Ange användarnamnet och lösenordet för administratörskontot på den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Ansluta till virtuell dator

När distributionen har slutförts kan du skapa en fjärrskrivbordsanslutning med den virtuella datorn.

Kör följande kommandon för att returnera den offentliga IP-adressen för den virtuella datorn. Anteckna denna IP-adress så att du kan ansluta till den till din webbläsare för att testa webbanslutningen i ett framtida steg.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Använd följande kommando på den lokala datorn för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med *publicIPAddress* för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```powershell
mstsc /v:<publicIpAddress>
```

I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange det användarnamn och lösenord som du skapade för den virtuella datorn och klicka sedan på **OK**.

## <a name="understand-vm-images"></a>Förstå VM-avbildningar

På Azures marknadsplats finns många avbildningar av virtuella datorer som kan användas för att skapa en ny virtuell dator. I de föregående stegen skapades en virtuell dator med avbildningen Windows Server 2016 Datacenter. I det här steget används PowerShell-modulen för att söka på marknadsplatsen efter andra Windows-avbildningar som också kan användas som bas för nya virtuella datorer. Den här processen består av att hitta utgivare, erbjudande, SKU och eventuellt ett versionsnummer för att [identifiera](cli-ps-findimage.md#terminology) avbildningen. 

Använd kommandot [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) till att returnera en lista med avbildningsutgivare:

```powershell
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Använd [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) till att returnera en lista med avbildningserbjudanden. Den returnerade listan filtreras på den angivna utgivaren med det här kommandot:

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

Kommandot [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) filtrerar sedan på namnet på utgivaren och erbjudandet och returnerar en lista med avbildningsnamn.

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

Den här informationen kan användas till att distribuera en virtuell dator med en viss avbildning. Det här exemplet distribuerar en virtuell dator med hjälp av den senaste versionen av en avbildning av Windows Server 2016 med behållare.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Parametern `-AsJob` skapar den virtuella datorn som en bakgrundsaktivitet så att PowerShell-kommandotolkarna återgår till dig. Du kan visa information om bakgrundsjobb med cmdleten `Get-Job`.


## <a name="understand-vm-sizes"></a>Förstå VM-storlekar

Storleken på den virtuella datorn avgör hur mycket av beräkningsresurser som CPU, GPU och minne som är tillgängliga för den virtuella datorn. Virtuella datorer måste skapas med lämplig storlek för förväntad arbetsbelastning. Om arbetsbelastningen ökar kan man ändra storlek på den befintliga virtuella datorn.

### <a name="vm-sizes"></a>VM-storlekar

I följande tabell kategoriseras storlekarna i användningsfall.  
| Typ                     | Normala storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Generellt syfte](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Balanserat förhållande mellan processor och minne. Perfekt för utveckling eller test samt små till medelstora lösningar för program och data.  |
| [Beräkningsoptimerad](sizes-compute.md)   | Fs, F             | Högt förhållande mellan processor och minne. Bra för program med medelhög trafik, nätverkstillämpningar och batchprocesser.        |
| [Minnesoptimerad](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Högt förhållande mellan minne och kärna. Utmärkt för relationsdatabaser, mellanstora till stora cacheminnen och minnesinterna analyser.                 |
| [Lagringsoptimerad](sizes-storage.md)      | Ls                | Högt diskgenomflöde och I/O. Perfekt för stordata, SQL- och NoSQL-databaser.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | Virtuella specialdatorer som är avsedda för tung grafisk rendering och videoredigering.       |
| [Höga prestanda](sizes-hpc.md) | H, A8-11          | Virtuella datorer med de kraftfullaste processorerna och nätverksgränssnitt för stora dataflöden (RDMA). 


### <a name="find-available-vm-sizes"></a>Hitta tillgängliga VM-storlekar

Om du vill se en lista med VM-storlekar som är tillgängliga i en viss region kan du använda kommandot [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator

När en virtuell dator har distribuerats kan storleken ändras för att öka eller minska resurstilldelningen.

Kontrollera om önskad storlek är tillgänglig i den aktuella virtuella datorns kluster innan du ändrar storleken på en virtuell dator. Kommandot [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) returnerar en lista med storlekar. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Om önskad storlek är tillgänglig kan storleken på den virtuella datorn ändras från att vara igång, men den startas om under åtgärden.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

Om önskad storlek inte finns i det aktuella klustret måste den virtuella datorn frigöras innan åtgärden för att ändra storlek kan utföras. Observera att när den virtuella datorn sätts på igen kommer alla data på den tillfälliga disken tas bort och den offentliga IP-adressen ändras, såvida inte en statisk IP-adress används. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>Energisparlägen för VM

En virtuell Azure-dator kan ha en av många energinivåer. Det här tillståndet motsvarar aktuellt tillstånd för den virtuella datorn i hypervisor-programmet. 

### <a name="power-states"></a>Energinivåer

| Energinivå | Beskrivning
|----|----|
| Startar | Anger att den virtuella datorn startas. |
| Körs | Anger att den virtuella datorn körs. |
| Stoppas | Anger att den virtuella datorn stoppas. | 
| Stoppad | Anger att den virtuella datorn har stoppats. Observera att virtuella datorer i ett stoppat tillstånd fortfarande kan medföra beräkningsavgifter.  |
| Frigör | Anger att den virtuella datorn frigörs. |
| Frigjord | Anger att den virtuella datorn är helt frånkopplad från hypervisor-programmet, men att den fortfarande är tillgänglig i kontrollplanen. Virtuella datorer med tillståndet Frigjord medför inte några beräkningsavgifter. |
| - | Anger att energinivån för den virtuella datorn är okänd. |

### <a name="find-power-state"></a>Hitta energisparläge

Om du vill hämta tillståndet för en viss virtuell dator kan du använda kommandot [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Du måste ange ett giltigt namn för en virtuell dator och resursgrupp. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Resultat:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Administrativa uppgifter

Under livscykeln för en virtuell dator kan du vilja köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa skript för att automatisera repetitiva och komplicerade uppgifter. Med hjälp av Azure PowerShell kan många vanliga administrativa uppgifter köras från kommandoraden eller i skript.

### <a name="stop-virtual-machine"></a>Stoppa en virtuell dator

Stoppa och frigöra en virtuell dator med [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

Använd parametern -StayProvisioned om du vill behålla den virtuella datorn i ett etablerat tillstånd.

### <a name="start-virtual-machine"></a>Starta en virtuell dator

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>Ta bort resursgrupp

Om en resursgrupp tas bort, tas även alla resurser som ingår i gruppen bort.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om grundläggande VM-skapande och hantering, till exempel att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välja och använda VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuella datorer

Gå vidare till nästa självstudie om du vill lära dig mer om diskar i virtuella dator.  

> [!div class="nextstepaction"]
> [Skapa och hantera diskar i virtuella datorer](./tutorial-manage-data-disk.md)
