---
title: "Skapa en Azure VM-skalningsuppsättning med PowerShell | Microsoft Docs"
description: "Skapa en Azure VM-skalningsuppsättning med PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7bb03323-8bcc-4ee4-9a3e-144ca6d644e2
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 1f8e66fac5b82698525794f0486dd0432c7421a7
ms.openlocfilehash: 7286fed39839675eb960b749f3235f83e36c5e9a


---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Skapa en Windows-skaluppsättning för virtuella datorer med Azure PowerShell
Här följer en ”fyll i tomrummen-strategi” för hur du skapar en skaluppsättning för virtuella datorer i Azure. I [översikten över skaluppsättningar för virtuella datorer](virtual-machine-scale-sets-overview.md) kan du läsa mer om skaluppsättningar.

Det bör ta cirka 30 minuter att slutföra stegen i den här artikeln.

## <a name="step-1-install-azure-powershell"></a>Steg 1: Installera Azure PowerShell
Se [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

## <a name="step-2-create-resources"></a>Steg 2: Skapa resurser
Skapa de resurser som krävs för den nya skaluppsättningen.

### <a name="resource-group"></a>Resursgrupp
En skaluppsättning för virtuella datorer måste ingå i en resursgrupp.

1. Hämta en lista över tillgängliga platser där resurser kan placeras:
   
        Get-AzureLocation | Sort Name | Select Name
2. Välj en plats som passar dig bäst och ersätt värdet **$locName** med platsens namn och skapa sedan variabeln:
   
        $locName = "location name from the list, such as Central US"
3. Ersätt värdet **$rgName** med namnet som du vill använda för den nya resursgruppen och skapa sedan variabeln: 
   
        $rgName = "resource group name"
4. Skapa resursgruppen:
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    Du bör se något som liknar det här exemplet:
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="virtual-network"></a>Virtuellt nätverk
Ett virtuellt nätverk krävs för de virtuella datorerna i skaluppsättningen.

1. Ersätt värdet **$subnetName** med namnet som du vill använda för undernätet i det virtuella nätverket och skapa sedan variabeln: 
   
        $subnetName = "subnet name"
2. Skapa undernätskonfigurationen:
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    Adressprefixet kan skilja sig från adressprefixet i det virtuella nätverket.
3. Ersätt värdet för **$netName** med namnet som du vill använda för det virtuella nätverket och skapa sedan variabeln: 
   
        $netName = "virtual network name"
4. Skapa det virtuella nätverket:
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Konfiguration av skaluppsättningen
Du har alla resurser som du behöver för konfigurationen av skaluppsättningen, så nu skapar vi den.  

1. Ersätt värdet för **$ipName** med namnet som du vill använda för IP-konfigurationen. Skapa sedan variabeln: 
   
        $ipName = "IP configuration name"
2. Skapa IP-konfigurationen:
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. Ersätt värdet för **$vmssConfig** med namnet som du vill använda för konfigurationen för skaluppsättningen och skapa sedan variabeln:   
   
        $vmssConfig = "Scale set configuration name"
4. Skapa konfigurationen för skaluppsättningen:
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    I det här exemplet visas en skaluppsättning som skapas med tre virtuella datorer. I [översikten över skaluppsättningar för virtuella datorer](virtual-machine-scale-sets-overview.md) finns mer information om kapaciteten för olika skaluppsättningar. Det här steget omfattar också storleken (kallas SkuName) för de virtuella datorerna i uppsättningen. Om du vill hitta en storlek som motsvarar dina behov kan du titta i [Storlekar för virtuella datorer](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Lägg till konfigurationen av nätverksgränssnittet i konfigurationen för skaluppsättningen:
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    Du bör se något som liknar det här exemplet:
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Operativsystemprofil
1. Ersätt värdet **$computerName** med prefixet för datornamnet som du vill använda och skapa sedan variabeln: 
   
        $computerName = "computer name prefix"
2. Ersätt värdet för **$adminName**-namnet för administratörskontot på de virtuella datorerna och skapa sedan variabeln:
   
        $adminName = "administrator account name"
3. Ersätt värdet **$adminPassword** med kontolösenordet och skapa sedan variabeln:
   
        $adminPassword = "password for administrator accounts"
4. Skapa operativsystemprofilen:
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Lagringsprofil
1. Ersätt värdet för **$storageProfile** med namnet som du vill använda för lagringsprofilen och skapa sedan variabeln:  
   
        $storageProfile = "storage profile name"
2. Skapa variablerna som definierar den avbildning som du ska använda:  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    Om du vill ha information om andra avbildningar du kan använda ska du titta i [Hitta och välj avbildningar av virtuella datorer i Azure med Windows PowerShell och Azure CLI](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

3. Skapa lagringsprofilen:
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Skaluppsättning för virtuella datorer
Slutligen kan du skapa skaluppsättningen.

1. Ersätt värdet för **$vmssName** med namnet på skaluppsättningen för den virtuella datorn och skapa sedan variabeln:
   
        $vmssName = "scale set name"
2. Skapa skaluppsättningen:
   
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss
   
    Det bör se ut ungefär som i det här exemplet där du kan se en slutförd distribution:
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Steg 3: Utforska resurser
Använd dessa resurser för att utforska den skaluppsättning för virtuella datorer som du skapade:

* Azure Portal – En begränsad mängd information är tillgänglig genom portalen.
* [Resursutforskaren i Azure](https://resources.azure.com/) – Det här verktyget är bäst när du vill utforska det aktuella tillståndet för skaluppsättningen.
* Azure PowerShell – Använd det här kommandot för att hämta information:
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>Nästa steg
* Hantera skaluppsättningen som du skapade med hjälp av informationen i [Hantera virtuella datorer i en skaluppsättning för virtuella datorer](virtual-machine-scale-sets-windows-manage.md)
* Överväg att konfigurera automatisk skalning av skaluppsättningen med hjälp av informationen i [Automatisk skalning och skaluppsättningar för virtuella datorer](virtual-machine-scale-sets-autoscale-overview.md)
* Läs mer om vertikal skalning i [Vertikal automatisk skalning med skaluppsättningar för virtuella datorer](virtual-machine-scale-sets-vertical-scale-reprovision.md)




<!--HONumber=Feb17_HO4-->


