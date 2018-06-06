---
title: Hitta och ta bort ej anslutna Azure hanterade och ohanterade diskar | Microsoft Docs
description: Hur vill du ta bort ej anslutna Azure hanterade och ohanterade (virtuella hårddiskar/sidblobbar) diskar med hjälp av Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ramankum
ms.openlocfilehash: 17262978c9600b75a1ddf945cf170fea6ac4f8ce
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34756940"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Hitta och ta bort ej anslutna Azure hanterade och ohanterade diskar
Diskar som är kopplade till den virtuella datorn inte att ta bort när du tar bort en virtuell dator (VM) i Azure, som standard. Den här funktionen hjälper till att förhindra dataförlust på grund av oavsiktlig borttagning av virtuella datorer. När en virtuell dator tas bort, fortsätter att betala för ej anslutna diskar. Den här artikeln visar hur du hittar och tar bort ej anslutna diskarna och minska onödiga kostnader. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Hanterade diskar: hitta och ta bort ej anslutna diskar 

Följande skript söker efter [hanterade diskar](managed-disks-overview.md) genom att undersöka värdet för den **av** egenskapen. När en hanterad disk som är ansluten till en virtuell dator i **av** -egenskapen innehåller resurs-ID för den virtuella datorn. När en hanterad disk är, den **av** -egenskapen är null. Skriptet undersöker alla hanterade diskar i en Azure-prenumeration. När skriptet hittar en hanterad disk med den **av** -egenskapen angetts till null, skriptet anger att disken är.

>[!IMPORTANT]
>Först kör skriptet genom att ange den **deleteUnattachedDisks** variabeln till 0. Den här åtgärden kan du söka efter och visa hanterade diskar.
>
>När du granskar alla diskar som kan köra skriptet igen och ange den **deleteUnattachedDisks** variabeln till 1. Den här åtgärden kan du ta bort alla ej ansluten hanterade diskar.
>

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Ohanterad diskar: hitta och ta bort ej anslutna diskar 

Ohanterad diskar är VHD-filer som lagras som [sidblobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure storage-konton](../../storage/common/storage-create-storage-account.md). Följande skript söker efter ohanterade diskar (sidblobbar) genom att undersöka värdet för den **LeaseStatus** egenskapen. När en ohanterad disk som är ansluten till en virtuell dator i **LeaseStatus** egenskap är inställd på **låst**. När en ohanterad disk är, den **LeaseStatus** egenskap är inställd på **olåst**. Skriptet undersöker alla ohanterade diskar i alla Azure storage-konton i en Azure-prenumeration. När skriptet hittar en ohanterad disk med en **LeaseStatus** egenskapen **olåst**, fastställs att disken är.

>[!IMPORTANT]
>Först kör skriptet genom att ange den **deleteUnattachedVHDs** variabeln till 0. Den här åtgärden kan du söka efter och visa alla beräkningsfunktionen ohanterade virtuella hårddiskar.
>
>När du granskar alla diskar som kan köra skriptet igen och ange den **deleteUnattachedVHDs** variabeln till 1. Den här åtgärden kan du ta bort alla ej ansluten ohanterade virtuella hårddiskar.
>

```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

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

Mer information finns i [ta bort lagringskontot](../../storage/common/storage-create-storage-account.md) och [identifiera frånkopplade diskar med PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)



