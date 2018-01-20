---
title: Hitta och ta bort ej anslutna Azure hanterade och ohanterade diskar | Microsoft Docs
description: "Hur vill du ta bort ej anslutna Azure hanterade och ohanterade (virtuella hårddiskar/sidblobbar) diskar med hjälp av Azure CLI"
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Hitta och ta bort ej anslutna Azure hanterade och ohanterade diskar
När du tar bort en virtuell dator i Azure, raderas inte diskar som är kopplade till den som standard. Det förhindrar dataförlust på grund av virtuella datorer som har tagits bort av misstag men du kan fortsätta att betala för de ej anslutna diskarna i onödan. Använda den här artikeln för att hitta och ta bort alla ej anslutna diskar och spara kostnader. 


## <a name="find-and-delete-unattached-managed-disks"></a>Hitta och ta bort beräkningsfunktionen hanterade diskar 

Följande skript visar hur du hittar hanterade diskar med hjälp av egenskapen.  Den loop genom alla hanterade diskar i en prenumeration och kontrollerar om den *av* -egenskapen är null för att hitta beräkningsfunktionen hanterade diskar. *Av* egenskapen lagrar resurs-ID för den virtuella datorn som en hanterad Disk är ansluten. 

Vi rekommenderar starkt du för första gången du kör skriptet genom att ange den *deleteUnattachedDisks* variabeln 0 om du vill visa alla ej anslutna diskar. När du har granskat de ej anslutna diskarna, kör du skriptet genom att ange *deleteUnattachedDisks* 1 för att ta bort alla ej anslutna diskar.

 ```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done

```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Hitta och ta bort beräkningsfunktionen ohanterade diskar 

Ohanterad diskar är VHD-filer som lagras som [sidblobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure Storage-konton](../../storage/common/storage-create-storage-account.md). Följande skript visar hur du hittar ohanterade diskar (sidblobbar) med hjälp av egenskapen LeaseStatus. Den loop genom alla ohanterade diskar i alla lagrings-konton i en prenumeration och kontrollerar om den *LeaseStatus* egenskapen låses upp om du vill hitta beräkningsfunktionen ohanterade diskar. Den *LeaseStatus* -egenskapen anges till låst om en ohanterad disk är kopplad till en virtuell dator. 

Vi rekommenderar starkt du för första gången du kör skriptet genom att ange den *deleteUnattachedVHDs* variabeln 0 om du vill visa alla ej anslutna diskar. När du har granskat de ej anslutna diskarna, kör du skriptet genom att ange *deleteUnattachedVHDs* 1 för att ta bort alla ej anslutna diskar.


 ```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 

```

## <a name="next-steps"></a>Nästa steg

[Ta bort lagringskontot](../../storage/common/storage-create-storage-account.md)



