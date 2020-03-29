---
title: Hitta och ta bort obundna Azure-hanterade och ohanterade diskar
description: Så här hittar och tar du bort obundna Azure-hanterade och ohanterade (VHDs/sidblobar) diskar med hjälp av Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: article
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6cf48c53e7b5c1cc8537abeda164460de66abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945148"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Hitta och ta bort obundna Azure-hanterade och ohanterade diskar med Azure CLI
När du tar bort en virtuell dator (VM) i Azure tas som standard inte alla diskar som är kopplade till den virtuella datorn bort. Den här funktionen hjälper till att förhindra dataförlust på grund av oavsiktlig borttagning av virtuella datorer. När en virtuell dator har tagits bort fortsätter du att betala för obundna diskar. Den här artikeln visar hur du hittar och tar bort obundna diskar och minskar onödiga kostnader. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Hanterade diskar: Hitta och ta bort obundna diskar 

Följande skript söker efter obundna [hanterade diskar](managed-disks-overview.md) genom att undersöka värdet för egenskapen **ManagedBy.** När en hanterad disk är kopplad till en virtuell dator innehåller egenskapen **ManagedBy** resurs-ID för den virtuella datorn. När en hanterad disk är obunden är egenskapen **ManagedBy** null. Skriptet undersöker alla hanterade diskar i en Azure-prenumeration. När skriptet hittar en hanterad disk med egenskapen **ManagedBy** inställd på null avgör skriptet att disken är okopplad.

>[!IMPORTANT]
>Kör först skriptet genom att ange variabeln **deleteUnattachedDisks** till 0. Med den här åtgärden kan du hitta och visa alla obundna hanterade diskar.
>
>När du har granskat alla obundna diskar kör du skriptet igen och ställer in variabeln **deleteUnattachedDisks** till 1. Med den här åtgärden kan du ta bort alla obundna hanterade diskar.
>

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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Ohanterade diskar: Hitta och ta bort obundna diskar 

Ohanterat diskar är VHD-filer som lagras som [sidblobar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure-lagringskonton](../../storage/common/storage-create-storage-account.md). Följande skript söker efter oanslutna ohanterade ohanterade diskar (sidblobar) genom att undersöka värdet för egenskapen **LeaseStatus.** När en ohanterad disk är kopplad till en virtuell dator anges egenskapen **LeaseStatus** till **Låst**. När en ohanterad disk är okopplad är egenskapen **LeaseStatus** inställd **på Olåst**. Skriptet undersöker alla ohanterade diskar i alla Azure-lagringskonton i en Azure-prenumeration. När skriptet hittar en ohanterad disk med egenskapen **LeaseStatus** inställd **på Olåst**avgör skriptet att disken är okopplad.

>[!IMPORTANT]
>Kör först skriptet genom att ange variabeln **deleteUnattachedVHDs** till 0. Med den här åtgärden kan du hitta och visa alla obundna ohanterade virtuella hårddiskar.
>
>När du har granskat alla obundna diskar kör du skriptet igen och ställer in variabeln **deleteUnattachedVHDs** till 1. Med den här åtgärden kan du ta bort alla obundna ohanterade virtuella hårddiskar.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

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

[Ta bort lagringskonto](../../storage/common/storage-create-storage-account.md)



