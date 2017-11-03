---
title: "Tillgänglighetsuppsättningar självstudier för virtuella Windows-datorer i Azure | Microsoft Docs"
description: "Läs mer om Tillgänglighetsuppsättningarna för virtuella Windows-datorer i Azure."
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 503657d6e6e22560b94d4a1cbbc2a74651d1ee7a
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="how-to-use-availability-sets"></a>Hur du använder tillgänglighetsuppsättningar

I kursen får lära du att öka tillgängligheten och tillförlitligheten hos dina virtuella lösningar på Azure med hjälp av en funktion som kallas Tillgänglighetsuppsättningar. Tillgänglighetsuppsättningar se till att de virtuella datorerna som du distribuerar i Azure är fördelade på flera isolerade maskinvara kluster. Detta säkerställer att om ett maskinvaru- eller fel i Azure händer, underordnade uppsättning dina virtuella datorer som påverkas och att din lösning är tillgänglig och fungerar. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga storlekar på VM
> * Kontrollera Azure Advisor

Den här självstudien kräver Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="availability-set-overview"></a>Översikt över tillgänglighetsuppsättning

En Tillgänglighetsuppsättning är en logisk gruppering funktion som du kan använda i Azure för att säkerställa att VM-resurser som du placerar i den isolerade från varandra när de distribueras i ett Azure-datacenter. Azure säkerställer att de virtuella datorerna som du placerar i en Tillgänglighetsuppsättning körs över flera fysiska servrar, beräkna rack, lagringsenheter och nätverksväxlar. Om ett maskinvaru- eller Azure programvarufel inträffar bara en del av dina virtuella datorer som påverkas och tillämpningsprogrammet övergripande förblir upp och fortsätter att vara tillgängliga för dina kunder. Tillgänglighetsuppsättningar är en viktig funktion när du vill skapa tillförlitliga molnlösningar.

Nu ska vi titta en typisk VM-baserad lösning där du kan ha 4 frontend-webbservrar och använda 2 backend-VMs som värd för en databas. Med Azure, skulle du vill definiera två tillgänglighetsuppsättningar innan du distribuerar dina virtuella datorer: en tillgänglighetsuppsättning för skiktet web och en tillgänglighetsuppsättning för databasskiktet. När du skapar en ny virtuell dator som du kan ange tillgänglighetsuppsättning som en parameter till virtuellt datorn az skapar kommando och Azure automatiskt ser till att de virtuella datorerna som du skapar inom separat uppsättningen tillgängliga över flera fysiska maskinvaruresurser. Om den fysiska maskinvara som din webbserver eller databasen Server virtuella datorer körs på ett problem har uppstått, vet du att andra instanser av webbservern och databasen virtuella datorer fortsätter att köras eftersom de finns på olika typer av maskinvara.

Använd Tillgänglighetsuppsättningar när du vill distribuera tillförlitliga VM-baserade lösningar i Azure.

## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Du kan skapa en tillgänglighetsuppsättning med [ny AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). I detta exempel vi in både antalet domäner för uppdatering och fel på *2* för tillgänglighetsuppsättning namngivna *myAvailabilitySet* i den *myResourceGroupAvailability* resursgruppen.

Skapa en resursgrupp.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Skapa en hanterad tillgänglighetsuppsättning med [ny AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) med den **- sku justerad** parameter.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Skapa virtuella datorer i en tillgänglighetsuppsättning

Virtuella datorer måste skapas inom tillgänglighetsuppsättning för att kontrollera att de distribueras på rätt sätt i maskinvaran. Du kan inte lägga till en befintlig virtuell dator till en tillgänglighetsuppsättning när den har skapats. 

Maskinvaran på en plats är uppdelat i flera domäner för uppdatering och feldomäner. En **uppdateringsdomän** är en grupp virtuella datorer och underliggande fysiska maskinvara som kan startas på samma gång. Virtuella datorer i samma **feldomän** delar vanliga storage samt en gemensam käll- och strömbrytare. 

När du skapar en VM-konfiguration av [ny AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) du använder den `-AvailabilitySetId` parametern för att ange ID för tillgänglighetsuppsättningen.

Skapa två virtuella datorer med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) i tillgänglighetsuppsättningen.

```azurepowershell-interactive
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig
    
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup `
    -Location eastus `
    -Name myNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAvailability `
    -SecurityRules $nsgRuleRDP
    
# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name mySubnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -ComputerName myVM$i `
        -Credential $cred `
        -VM $vm `
        -Windows `
        -EnableAutoUpdate `
        -ProvisionVMAgent
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

Det tar några minuter att skapa och konfigurera både virtuella datorer. När du är klar har du två virtuella datorer distribuerade i den underliggande maskinvaran. 

Om du tittar på tillgänglighetsuppsättning i portalen genom att gå till resursgrupper > myResourceGroupAvailability > myAvailabilitySet, bör du se hur de virtuella datorerna är fördelade på 2 felet och uppdatera domäner.

![Tillgänglighetsuppsättning i portalen](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Sök efter tillgängliga storlekar på VM 

Du kan lägga till flera virtuella datorer tillgänglighetsuppsättning senare, men du behöver veta vilka VM-storlekar är tillgängliga på maskinvaran. Använd [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) att lista alla tillgängliga storlekar på maskinvaran kluster för tillgänglighetsuppsättningen.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="check-azure-advisor"></a>Kontrollera Azure Advisor 

Du kan också använda Azure Advisor för att få mer information om hur du kan förbättra tillgängligheten för din virtuella dator. Azure Advisor hjälper dig att följa de bästa metoderna för att optimera din Azure-distributioner. Den analyserar dina resurskonfigurationen och telemetri och rekommenderar lösningar som hjälper dig att förbättra kostnadseffektivitet, prestanda, hög tillgänglighet och säkerhet för dina Azure-resurser.

Logga in på den [Azure-portalen](https://portal.azure.com)väljer **fler tjänster**, och skriv **Advisor**. Advisor-instrumentpanelen innehåller anpassad rekommendationer för den valda prenumerationen. Mer information finns i [Kom igång med Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga storlekar på VM
> * Kontrollera Azure Advisor

Gå vidare till nästa kurs vill veta mer om virtuella datorer.

> [!div class="nextstepaction"]
> [Skapa en VM-skalningsuppsättning](tutorial-create-vmss.md)


