---
title: Konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar
description: Konvertera en Virtuell Windows-dator från ohanterade diskar till hanterade diskar med hjälp av PowerShell i distributionsmodellen för Resource Manager
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 8c180cfc597c0ade27b1fe8cca5a8751176ea12e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460127"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar

Om du har befintliga virtuella Datorer i Windows som använder ohanterade diskar kan du konvertera de virtuella datorerna så att de använder hanterade diskar via Azure [Managed Disks-tjänsten.](managed-disks-overview.md) Den här processen konverterar både OS-disken och alla anslutna datadiskar.

 

## <a name="before-you-begin"></a>Innan du börjar


* Granskningsplan [för migreringen till Hanterade diskar](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Granska [vanliga frågor och svar om migrering till Hanterade diskar](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* De ursprungliga virtuella hårddiskarna och lagringskontot som användes av den virtuella datorn före omvandlingen tas inte bort. De kan medföra ytterligare kostnader. För att undvika att debiteras för dem kan du ta bort de ursprungliga virtuella hårddiskblobbarna när du har kontrollerat att omvandlingen har slutförts. Om du behöver hitta dessa obundna diskar för att ta bort dem läser du vår artikel [Hitta och ta bort obundna Azure-hanterade och ohanterade diskar](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Konvertera virtuella datorer med en instans
Det här avsnittet beskriver hur du konverterar virtuella azure-datorer med en instans från ohanterade diskar till hanterade diskar. (Om dina virtuella datorer är i en tillgänglighetsuppsättning läser du nästa avsnitt.) 

1. Frigöra den virtuella datorn med hjälp av [cmdleten Stop-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) I följande exempel frigörs den `myVM` virtuella datorn `myResourceGroup`som namnges i resursgruppen: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Konvertera den virtuella datorn till hanterade diskar med hjälp av [cmdleten ConvertTo-AzVMManagedDisk.](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) Följande process konverterar den tidigare virtuella datorn, inklusive OS-disken och eventuella datadiskar, och startar den virtuella datorn:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Konvertera virtuella datorer i en tillgänglighetsuppsättning

Om de virtuella datorerna som du vill konvertera till hanterade diskar finns i en tillgänglighetsuppsättning måste du först konvertera tillgänglighetsuppsättningen till en hanterad tillgänglighetsuppsättning.

1. Konvertera tillgänglighetsuppsättningen med hjälp av [cmdleten Update-AzAvailabilitySet.](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) I följande exempel uppdateras `myAvailabilitySet` tillgänglighetsuppsättningen `myResourceGroup`som namnges i resursgruppen med namnet :

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Om regionen där tillgänglighetsuppsättningen finns bara har 2 hanterade feldomäner men antalet ohanterade feldomäner är 3, visar det här kommandot ett fel som liknar "Det angivna antalet feldomäner 3 måste hamna i intervallet 1 till 2." Lös felet genom att uppdatera feldomänen `Sku` `Aligned` till 2 och uppdatera till följande:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Frigöra och konvertera de virtuella datorerna i tillgänglighetsuppsättningen. Följande skriptavtallokalar varje virtuell dator med hjälp av [cmdleten Stop-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) konverterar den med Hjälp av [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)och startar om den automatiskt som bortsett från konverteringsprocessen:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Felsökning

Om det finns ett fel under konverteringen, eller om en virtuell dator är `ConvertTo-AzVMManagedDisk` i ett misslyckat tillstånd på grund av problem i en tidigare konvertering, kör cmdlet igen. Ett enkelt nytt försök avblockerar vanligtvis situationen.
Innan du konverterar, se till att alla VM-tillägg är i tillståndet Etablera lyckades eller konverteringen misslyckas med felkoden 409.

## <a name="convert-using-the-azure-portal"></a>Konvertera med Azure-portalen

Du kan också konvertera ohanterade diskar till hanterade diskar med Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn i listan över virtuella datorer i portalen.
3. Välj **Diskar** på menyn i bladet för den virtuella datorn.
4. Högst upp på **diskbladet** väljer du **Migrera till hanterade diskar**.
5. Om den virtuella datorn har en tillgänglighetsuppsättning visas en varning på bladet **Migrera till hanterade diskar** som du behöver för att konvertera tillgänglighetsuppsättningen först. Varningen bör ha en länk som du kan klicka på för att konvertera tillgänglighetsuppsättningen. När tillgänglighetsuppsättningen har konverterats eller om den virtuella datorn inte har en tillgänglighetsuppsättning klickar du på **Migrera** för att starta processen med att migrera diskarna till hanterade diskar.

Den virtuella datorn stoppas och startas om när migreringen har slutförts.

## <a name="next-steps"></a>Nästa steg

[Konvertera standardhanterade diskar till premium](convert-disk-storage.md)

Ta en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicksbilder](snapshot-copy-managed-disk.md).

