---
title: Konvertera en Windows-dator från ohanterade diskar till managed disks – Azure Managed Disks | Microsoft Docs
description: Så här konverterar du en virtuell Windows-dator från ohanterade diskar till hanterade diskar med hjälp av PowerShell i Resource Manager-distributionsmodellen
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: 6e7d4a0ab6d79e1615f921965fb3d77998eaf90c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39000955"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konvertera en Windows-dator från ohanterade diskar till managed disks

Om du har befintliga Windows virtuella datorer (VM) som använder ohanterade diskar kan du konvertera de virtuella datorerna för att använda hanterade diskar via den [Azure Managed Disks](managed-disks-overview.md) service. Den här processen konverterar både operativsystemdisken och kopplade datadiskar.

Den här artikeln visar hur du konverterar virtuella datorer med hjälp av Azure PowerShell. Om du behöver installera eller uppgradera den kan du läsa [installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Innan du börjar


* Granska [planera för migrering till Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Granska [vanliga frågor om migrering till Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Konvertera en instans virtuella datorer
Det här avsnittet beskriver hur du konverterar en instans virtuella Azure-datorer från ohanterade diskar till hanterade diskar. (Om dina virtuella datorer finns i en tillgänglighetsuppsättning, se nästa avsnitt.) 

1. Frigör den virtuella datorn med hjälp av den [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet. I följande exempel bort den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`: 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Konvertera den virtuella datorn till hanterade diskar med hjälp av den [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) cmdlet. Följande process konverterar den föregående virtuella datorn, inklusive OS-disken och eventuella datadiskar och startar den virtuella datorn:

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```



## <a name="convert-vms-in-an-availability-set"></a>Konvertera virtuella datorer i en tillgänglighetsuppsättning

Om de virtuella datorer som du vill konvertera till hanterade diskar är i en tillgänglighetsuppsättning, måste du först omvandla tillgänglighetsuppsättningen till en hanterad tillgänglighetsuppsättning.

1. Omvandla tillgänglighetsuppsättningen med hjälp av den [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) cmdlet. I följande exempel uppdaterar tillgänglighetsuppsättningen med namnet `myAvailabilitySet` i resursgruppen med namnet `myResourceGroup`:

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Om den region där din tillgänglighetsuppsättning finns har bara 2 hanterade feldomäner men antalet ohanterade feldomäner är 3, det här kommandot visar ett fel som liknar ”den angivna feldomänsantal 3 måste ligga i intervallet 1 till 2”. Lös felet genom att uppdatera feldomänen till 2 och uppdatera `Sku` till `Aligned` på följande sätt:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Frigöra och konvertera de virtuella datorerna i tillgänglighetsuppsättningen. Följande skript Frigör varje virtuell dator med hjälp av den [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet, konverterar den med hjälp av [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk), och startar om automatiskt så upp processen :

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Felsökning

Om det finns ett fel under konverteringen, eller om en virtuell dator är i ett felaktigt tillstånd på grund av problem i en föregående konvertering, kör den `ConvertTo-AzureRmVMManagedDisk` cmdlet igen. Ett enkelt återförsök avblockeras vanligtvis situationen.
Innan du konverterar, kontrollera att alla VM-tillägg finns i ”lyckades” Etableringsstatus eller konverteringen misslyckas med felkoden 409.


## <a name="convert-using-the-azure-portal"></a>Konvertera med hjälp av Azure portal

Du kan också konvertera ohanterade diskar till hanterade diskar med Azure portal.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj den virtuella datorn från listan över virtuella datorer i portalen.
3. I bladet för den virtuella datorn, väljer **diskar** på menyn.
4. Överst på den **diskar** bladet väljer **migrera till managed disks**.
5. Om den virtuella datorn är i en tillgänglighetsuppsättning, är en varning på den **migrera till managed disks** bladet som du vill omvandla tillgänglighetsuppsättningen först. Varningen ska ha en länk som du kan klicka på för att omvandla tillgänglighetsuppsättningen. När tillgänglighetsuppsättningen har omvandlats eller om den virtuella datorn inte är i en tillgänglighetsuppsättning klickar du på **migrera** att starta processen med att migrera dina diskar till hanterade diskar. 

Den virtuella datorn stoppas och startas om när migreringen är klar.

## <a name="next-steps"></a>Nästa steg

[Konvertera hanterade standarddiskar till premium](convert-disk-storage.md)

Ta en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicksbilder](snapshot-copy-managed-disk.md).

