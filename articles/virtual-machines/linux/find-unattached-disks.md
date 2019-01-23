---
title: Hitta och ta bort icke anslutna Azure-hanterade och ohanterade diskar | Microsoft Docs
description: 'Så här att hitta och ta bort icke anslutna Azure hanterade och ohanterade (VHD: er/sidblob) diskar med hjälp av Azure CLI.'
services: virtual-machines-linux
documentationcenter: ''
author: ramankumarlive
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ramankum
ms.component: disks
ms.openlocfilehash: 58a54b7e867b625adf76d6287d5067c326c80f17
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462801"
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Ohanterade diskar: Hitta och ta bort icke anslutna diskar 

Ohanterade diskar är VHD-filer som lagras som [sidblobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure storage-konton](../../storage/common/storage-create-storage-account.md). Följande skript söker efter icke anslutna ohanterade diskar (sidblob) genom att undersöka värdet för den **LeaseStatus** egenskapen. När en ohanterad disk som är ansluten till en virtuell dator kan den **LeaseStatus** är inställd på **låst**. När en ohanterad disk är frånkopplad, den **LeaseStatus** är inställd på **olåst**. Skriptet undersöker alla ohanterade diskar i alla Azure storage-konton i en Azure-prenumeration. När skriptet hittar en ohanterad disk med en **LeaseStatus** egenskapen **olåst**, skriptet anger att disken är frånkopplad.

>[!IMPORTANT]
>Kör skriptet först genom att ange den **deleteUnattachedVHDs** variabeln till 0. Den här åtgärden kan du hitta och visa alla ej anslutna ohanterade virtuella hårddiskar.
>
>När du har läst icke anslutna diskar, kör skriptet igen och ange den **deleteUnattachedVHDs** variabeln till 1. Den här åtgärden kan du ta bort alla ej anslutna ohanterade virtuella hårddiskar.
>

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

[Ta bort lagringskonto](../../storage/common/storage-create-storage-account.md)



