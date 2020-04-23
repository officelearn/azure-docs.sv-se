---
title: Distribuera Azure-dedikerade värdar med Azure PowerShell
description: Distribuera virtuella datorer till dedikerade värdar med Azure PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: b90189c6ba5e51a24d0c248b5aa08e9a5e4bbd9b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082857"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Distribuera virtuella datorer till dedikerade värdar med Azure PowerShell

Den här artikeln guidar dig genom hur du skapar en [Azure-dedikerad värd](dedicated-hosts.md) som värd för dina virtuella datorer. 

Kontrollera att du har installerat Azure PowerShell version 2.8.0 eller senare och `Connect-AzAccount`att du är inloggad på ett Azure-konto med . 

## <a name="limitations"></a>Begränsningar

- Skalningsuppsättningar för virtuella datorer stöds för närvarande inte på dedikerade värdar.
- Storleken och maskinvarutyperna som är tillgängliga för dedikerade värdar varierar mellan olika regioner. Läs [värdprissidan](https://aka.ms/ADHPricing) om du vill veta mer.

## <a name="create-a-host-group"></a>Skapa en värdgrupp

En **värdgrupp** är en resurs som representerar en samling dedikerade värdar. Du skapar en värdgrupp i en region och en tillgänglighetszon och lägger till värdar i den. När du planerar för hög tillgänglighet finns det ytterligare alternativ. Du kan använda ett eller båda av följande alternativ med dina dedikerade värdar: 
- Sträcker sig över flera tillgänglighetszoner. I det här fallet måste du ha en värdgrupp i var och en av de zoner som du vill använda.
- Span över flera feldomäner som mappas till fysiska rack. 
 
I båda fallen måste du ange antalet feldomäner för värdgruppen. Om du inte vill spänna över feldomäner i gruppen använder du ett antal feldomäner på 1. 

Du kan också välja att använda både tillgänglighetszoner och feldomäner. I det här exemplet skapas en värdgrupp i zon 1 med två feldomäner. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Skapa en värd

Nu ska vi skapa en dedikerad värd i värdgruppen. Förutom ett namn för värden måste du ange SKU för värden. Host SKU fångar den VM-serien som stöds samt maskinvarugenereringen för din dedikerade värd.

Mer information om värdsskrona och priser finns i [Azure Dedicated Host pricing](https://aka.ms/ADHPricing).

Om du anger ett antal feldomäner för värdgruppen blir du ombedd att ange feldomänen för värden. I det här exemplet ställer vi in feldomänen för värden till 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator på den dedikerade värden. 

Om du angav en tillgänglighetszon när du skapar värdgruppen måste du använda samma zon när du skapar den virtuella datorn. I det här exemplet, eftersom vår värdgrupp är i zon 1, måste vi skapa den virtuella datorn i zon 1.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Om du skapar en virtuell dator på en värd som inte har tillräckligt med resurser skapas den virtuella datorn i ett misslyckat tillstånd. 

## <a name="check-the-status-of-the-host"></a>Kontrollera värdens status

Du kan kontrollera värdens hälsostatus och hur många virtuella datorer du fortfarande `-InstanceView` kan distribuera till värden med [GetAzHost](/powershell/module/az.compute/get-azhost) med parametern.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Utdata kommer att se ut ungefär så här:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="add-an-existing-vm"></a>Lägga till en befintlig virtuell dator 

Du kan lägga till en befintlig virtuell dator till en dedikerad värd, men den virtuella datorn måste först vara Stop\Deallocated. Innan du flyttar en virtuell dator till en dedikerad värd kontrollerar du att vm-konfigurationen stöds:

- Den virtuella datorns storlek måste vara i samma storlek som den dedikerade värden. Om din dedikerade värd är DSv3 kan storleken på den virtuella datorn vara Standard_D4s_v3, men det kan inte vara en Standard_A4_v2. 
- Den virtuella datorn måste finnas i samma region som den dedikerade värden.
- Den virtuella datorn kan inte ingå i en närhetsplaceringsgrupp. Ta bort den virtuella datorn från proximityplaceringsgruppen innan du flyttar den till en dedikerad värd. Mer information finns i [Flytta en virtuell dator från en närhetsplaceringsgrupp](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- Den virtuella datorn kan inte vara i en tillgänglighetsuppsättning.
- Om den virtuella datorn finns i en tillgänglighetszon måste den vara samma tillgänglighetszon som värdgruppen. Tillgänglighetszoninställningarna för den virtuella datorn och värdgruppen måste matcha.

Ersätt variablernas värden med din egen information.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Rensa

Du debiteras för dina dedikerade värdar även när inga virtuella datorer distribueras. Du bör ta bort alla värdar som du för närvarande inte använder för att spara kostnader.  

Du kan bara ta bort en värd när det inte längre finns virtuella datorer som använder den. Ta bort de virtuella datorerna med [Ta bort AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

När du har tagit bort de virtuella datorerna kan du ta bort värden med [Ta bort AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

När du har tagit bort alla dina värdar kan du ta bort värdgruppen med [Ta bort AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Du kan också ta bort hela resursgruppen i ett enda kommando med [Ta bort AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Detta tar bort alla resurser som skapats i gruppen, inklusive alla virtuella datorer, värdar och värdgrupper.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Nästa steg

- Det finns exempelmall, som finns [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och feldomäner för maximal återhämtning i en region.

- Du kan också distribuera dedikerade värdar med [Azure-portalen](dedicated-hosts-portal.md).
