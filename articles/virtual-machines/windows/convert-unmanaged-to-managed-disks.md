---
title: "Konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar - hanterade diskar i Azure | Microsoft Docs"
description: "Konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar med PowerShell i Resource Manager-distributionsmodellen"
services: virtual-machines-windows
documentationcenter: 
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
ms.date: 06/23/2017
ms.author: cynthn
ms.openlocfilehash: 445117371fde91d0a0fcb96f06e42e2033692789
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2017
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar

Om du har befintliga virtuella Windows-datorer (VM) som använder ohanterade diskar, kan du konvertera virtuella datorer om du vill använda hanterade diskar via den [Azure hanterade diskar](managed-disks-overview.md) service. Den här processen konverterar både OS-disken och eventuella anslutna hårddiskar.

Den här artikeln visar hur du konverterar virtuella datorer med hjälp av Azure PowerShell. Om du behöver installera eller uppgradera den, se [installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Innan du börjar


* Granska [planera för migrering till hanterade diskar](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Konvertera single instance virtuella datorer
Det här avsnittet beskriver hur du konverterar single instance virtuella Azure-datorer från ohanterade diskar till hanterade diskar. (Om dina virtuella datorer i en tillgänglighetsuppsättning, finns i nästa avsnitt.) 

1. Frigör den virtuella datorn med hjälp av den [stoppa AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet. I följande exempel tar bort den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`: 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Konvertera den virtuella datorn till hanterade diskar med hjälp av den [ConvertTo AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) cmdlet. Följande process konverterar tidigare VM, inklusive OS-disken och eventuella hårddiskar:

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Starta den virtuella datorn efter konvertering till hanterade diskar med hjälp av [Start AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). I följande exempel startar om den tidigare:

  ```azurepowershell-interactive
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Konvertera virtuella datorer i en tillgänglighetsuppsättning

Om de virtuella datorer som du vill konvertera till hanterade diskar är i en tillgänglighetsuppsättning, måste du först konvertera tillgänglighetsuppsättning till en hanterad tillgänglighetsuppsättningen.

1. Konvertera tillgänglighetsuppsättning med hjälp av den [uppdatering AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) cmdlet. I följande exempel uppdateras tillgänglighetsuppsättning namngivna `myAvailabilitySet` i resursgrupp med namnet `myResourceGroup`:

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Om den region där din tillgänglighetsuppsättning finns har bara 2 hanterade feldomäner men antalet ohanterade feldomäner 3, det här kommandot visar ett fel liknar ”det angivna feldomänsantalet 3 måste ligga i intervallet 1 till 2”. Åtgärda felet genom att uppdatera feldomänen till 2 och uppdatera `Sku` till `Aligned` på följande sätt:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Frigöra och konvertera virtuella datorer i tillgänglighetsuppsättningen. Följande skript tar bort varje virtuell dator med hjälp av den [stoppa AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet, konverterar den med hjälp av [ConvertTo AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk), och startar om den med hjälp av [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm):

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Felsökning

Om det uppstår ett fel under konverteringen, eller om en virtuell dator är i ett felaktigt tillstånd på grund av problem i en tidigare konvertering, kör den `ConvertTo-AzureRmVMManagedDisk` cmdlet igen. En enkel försök avblockeras vanligtvis situationen.


## <a name="next-steps"></a>Nästa steg

[Konvertera hanterade standarddiskar till premium](convert-disk-storage.md)

Ta en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicksbilder](snapshot-copy-managed-disk.md).

