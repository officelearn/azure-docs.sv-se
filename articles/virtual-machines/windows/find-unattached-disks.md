---
title: Hitta och ta bort icke anslutna Azure-hanterade och ohanterade diskar | Microsoft Docs
description: 'Så här att hitta och ta bort icke anslutna Azure hanterade och ohanterade (VHD: er/sidblob) diskar med hjälp av Azure PowerShell.'
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 705706b011464f9e1f437db7b608b3a73e7c3655
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727391"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Hitta och ta bort icke anslutna Azure-hanterade och ohanterade diskar

När du tar bort en virtuell dator (VM) i Azure, som standard alla diskar som är anslutna till den virtuella datorn inte tas bort. Den här funktionen hjälper till att förhindra dataförlust på grund av oavsiktlig borttagning av virtuella datorer. När en virtuell dator har tagits bort, fortsätter att betala för icke anslutna diskar. Den här artikeln visar hur du hitta och ta bort eventuella icke anslutna diskar och minska onödiga kostnader.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Hanterade diskar: Hitta och ta bort icke anslutna diskar

Följande skript söker efter ej anslutna [hanterade diskar](managed-disks-overview.md) genom att undersöka värdet för den **ManagedBy** egenskapen. När en hanterad disk som är ansluten till en virtuell dator kan den **ManagedBy** egenskapen innehåller resurs-ID för den virtuella datorn. När en hanterad disk är frånkopplad, den **ManagedBy** -egenskapen är null. Skriptet undersöker alla hanterade diskar i en Azure-prenumeration. När skriptet hittar en hanterad disk med den **ManagedBy** -egenskapen angetts till null, skriptet anger att disken är frånkopplad.

>[!IMPORTANT]
>Kör skriptet först genom att ange den **deleteUnattachedDisks** variabeln till 0. Den här åtgärden kan du hitta och visa alla ej anslutna hanterade diskar.
>
>När du har läst icke anslutna diskar, kör skriptet igen och ange den **deleteUnattachedDisks** variabeln till 1. Den här åtgärden kan du ta bort icke anslutna hanterade diskar.
>

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Ohanterade diskar: Hitta och ta bort icke anslutna diskar 

Ohanterade diskar är VHD-filer som lagras som [sidblobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure storage-konton](../../storage/common/storage-create-storage-account.md). Följande skript söker efter icke anslutna ohanterade diskar (sidblob) genom att undersöka värdet för den **LeaseStatus** egenskapen. När en ohanterad disk som är ansluten till en virtuell dator kan den **LeaseStatus** är inställd på **låst**. När en ohanterad disk är frånkopplad, den **LeaseStatus** är inställd på **olåst**. Skriptet undersöker alla ohanterade diskar i alla Azure storage-konton i en Azure-prenumeration. När skriptet hittar en ohanterad disk med en **LeaseStatus** egenskapen **olåst**, skriptet anger att disken är frånkopplad.

>[!IMPORTANT]
>Kör skriptet först genom att ange den **deleteUnattachedVHDs** variabeln till 0. Den här åtgärden kan du hitta och visa alla ej anslutna ohanterade virtuella hårddiskar.
>
>När du har läst icke anslutna diskar, kör skriptet igen och ange den **deleteUnattachedVHDs** variabeln till 1. Den här åtgärden kan du ta bort alla ej anslutna ohanterade virtuella hårddiskar.
>

```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0

$storageAccounts = Get-AzStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [ta bort lagringskonto](../../storage/common/storage-create-storage-account.md) och [identifiera frånkopplade diskar med PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)