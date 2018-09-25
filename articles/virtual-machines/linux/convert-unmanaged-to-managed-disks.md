---
title: Konvertera en Linux-dator i Azure från ohanterade diskar till managed disks – Azure Managed Disks | Microsoft Docs
description: Så här konverterar du en Linux VM från ohanterade diskar till hanterade diskar med hjälp av Azure CLI i Resource Manager-distributionsmodellen
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.openlocfilehash: 62ac70134c1bdf9d178d59723ff73561049a4bbf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993090"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konvertera en virtuell Linux-dator från ohanterade diskar till managed disks

Om du har befintliga Linux-datorer (VM) som använder ohanterade diskar kan du konvertera de virtuella datorerna ska använda [Azure Managed Disks](../linux/managed-disks-overview.md). Den här processen konverterar både operativsystemdisken och kopplade datadiskar.

Den här artikeln visar hur du konverterar virtuella datorer med hjälp av Azure CLI. Om du behöver installera eller uppgradera den kan du läsa [installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Innan du börjar
* Granska [vanliga frågor om migrering till Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Konvertera en instans virtuella datorer
Det här avsnittet beskriver hur du konverterar en instans virtuella Azure-datorer från ohanterade diskar till hanterade diskar. (Om dina virtuella datorer finns i en tillgänglighetsuppsättning, se nästa avsnitt.) Du kan använda den här processen för att konvertera de virtuella datorerna från premium (SSD) ohanterade diskar till premium-hanterade diskar, eller från standard (HDD) ohanterade diskar till hanterade standarddiskar.

1. Frigör den virtuella datorn med hjälp av [az vm deallocate](/cli/azure/vm#az_vm_deallocate). I följande exempel bort den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Konvertera den virtuella datorn till hanterade diskar med hjälp av [az vm konvertera](/cli/azure/vm#az_vm_convert). Följande process konverterar den virtuella datorn med namnet `myVM`, inklusive OS-disken och eventuella datadiskar:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Starta den virtuella datorn efter omvandlingen till hanterade diskar med hjälp av [az vm start](/cli/azure/vm#az_vm_start). Följande exempel startar den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Konvertera virtuella datorer i en tillgänglighetsuppsättning

Om de virtuella datorer som du vill konvertera till hanterade diskar är i en tillgänglighetsuppsättning, måste du först omvandla tillgänglighetsuppsättningen till en hanterad tillgänglighetsuppsättning.

Alla virtuella datorer i tillgänglighetsuppsättningen måste frigöras innan du omvandla tillgänglighetsuppsättningen. Om du tänker konvertera alla virtuella datorer till managed disks när tillgänglighetsuppsättningen själva har konverterats till en hanterad tillgänglighetsuppsättning. Sedan starta de virtuella datorerna och fortsätta fungera som vanligt.

1. Lista över alla virtuella datorer i en tillgänglighetsuppsättning med hjälp av [az vm availability-set list](/cli/azure/vm/availability-set#az_vm_availability_set_list). I följande exempel visar en lista över alla virtuella datorer i tillgänglighetsuppsättningen med namnet `myAvailabilitySet` i resursgruppen med namnet `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Frigör alla virtuella datorer med hjälp av [az vm deallocate](/cli/azure/vm#az_vm_deallocate). I följande exempel bort den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Omvandla tillgänglighetsuppsättningen med hjälp av [az vm availability-set convert](/cli/azure/vm/availability-set#az_vm_availability_set_convert). I följande exempel konverterar tillgänglighetsuppsättningen med namnet `myAvailabilitySet` i resursgruppen med namnet `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Konvertera alla virtuella datorer till managed disks med hjälp av [az vm konvertera](/cli/azure/vm#az_vm_convert). Följande process konverterar den virtuella datorn med namnet `myVM`, inklusive OS-disken och eventuella datadiskar:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Starta de virtuella datorerna efter omvandlingen till hanterade diskar med hjälp av [az vm start](/cli/azure/vm#az_vm_start). Följande exempel startar den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om lagringsalternativ finns i [översikt över Azure Managed Disks](../windows/managed-disks-overview.md).
