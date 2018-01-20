---
title: Hitta och ta bort ej anslutna Azure hanterade och ohanterade diskar | Microsoft Docs
description: "Hur vill du ta bort ej anslutna Azure hanterade och ohanterade (virtuella hårddiskar/sidblobbar) diskar med hjälp av Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Hitta och ta bort ej anslutna Azure hanterade och ohanterade diskar
När du tar bort en virtuell dator i Azure, raderas inte diskar som är kopplade till den som standard. Det förhindrar dataförlust på grund av virtuella datorer som har tagits bort av misstag men du kan fortsätta att betala för de ej anslutna diskarna i onödan. Använda den här artikeln för att hitta och ta bort alla ej anslutna diskar och spara kostnader. 


## <a name="find-and-delete-unattached-managed-disks"></a>Hitta och ta bort beräkningsfunktionen hanterade diskar 

Följande skript visar hur du hittar [hanterade diskar](managed-disks-overview.md) med hjälp av *av* egenskapen. Den loop genom alla hanterade diskar i en prenumeration och kontrollerar de *av* -egenskapen är null för att hitta beräkningsfunktionen hanterade diskar. *Av* egenskapen lagrar resurs-ID för den virtuella datorn som en hanterad Disk är ansluten.

Vi rekommenderar starkt du för första gången du kör skriptet genom att ange den *deleteUnattachedDisks* variabeln 0 om du vill visa alla ej anslutna diskar. När du har granskat de ej anslutna diskarna, kör du skriptet genom att ange *deleteUnattachedDisks* 1 för att ta bort alla ej anslutna diskar.

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
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Hitta och ta bort beräkningsfunktionen ohanterade diskar 

Ohanterad diskar är VHD-filer lagrade som [sidblobbar] (/ rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure Storage-konton](../../storage/common/storage-create-storage-account.md). Följande skript visar hur du hittar ohanterade diskar (sidblobbar) med den *LeaseStatus* egenskapen. Den loop genom alla ohanterade diskar i alla lagrings-konton i en prenumeration och kontrollerar om den *LeaseStatus* egenskapen låses upp om du vill hitta beräkningsfunktionen ohanterade diskar. *LeaseStatus* -egenskapen anges till låst om en ohanterad disk är kopplad till en virtuell dator.

Vi rekommenderar starkt du för första gången du kör skriptet genom att ange den *deleteUnattachedVHDs* variabeln 0 om du vill visa alla ej anslutna diskar. När du har granskat de ej anslutna diskarna, kör du skriptet genom att ange *deleteUnattachedVHDs* 1 för att ta bort alla ej anslutna diskar.


```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

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

[Ta bort lagringskontot](../../storage/common/storage-create-storage-account.md)




