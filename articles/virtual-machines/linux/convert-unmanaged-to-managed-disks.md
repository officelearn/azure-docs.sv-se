---
title: Konvertera en virtuell Linux-dator från ohanterade diskar till hanterade diskar
description: Konvertera en virtuell Linux-dator från ohanterade diskar till hanterade diskar med hjälp av Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2774dcbd5fc5b01627b965c2c02d870412c8bf77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969699"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konvertera en virtuell Linux-dator från ohanterade diskar till hanterade diskar

Om du har befintliga virtuella Linux-datorer (VMs) som använder ohanterade diskar kan du konvertera de virtuella datorerna så att de använder [Azure Managed Disks](../linux/managed-disks-overview.md). Den här processen konverterar både OS-disken och alla anslutna datadiskar.

Den här artikeln visar hur du konverterar virtuella datorer med hjälp av Azure CLI. Om du behöver installera eller uppgradera den läser du [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Innan du börjar
* Granska [vanliga frågor och svar om migrering till Hanterade diskar](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* De ursprungliga virtuella hårddiskarna och lagringskontot som användes av den virtuella datorn före omvandlingen tas inte bort. De kan medföra ytterligare kostnader. För att undvika att debiteras för dem kan du ta bort de ursprungliga virtuella hårddiskblobbarna när du har kontrollerat att omvandlingen har slutförts. Om du behöver hitta dessa obundna diskar för att ta bort dem läser du vår artikel [Hitta och ta bort obundna Azure-hanterade och ohanterade diskar](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Konvertera virtuella datorer med en instans
Det här avsnittet beskriver hur du konverterar virtuella azure-datorer med en instans från ohanterade diskar till hanterade diskar. (Om dina virtuella datorer är i en tillgänglighetsuppsättning läser du nästa avsnitt.) Du kan använda den här processen för att konvertera de virtuella datorerna från ohanterade diskar (Premium) till premiumhanterade diskar eller från vanliga (HDD) ohanterade diskar till vanliga hanterade diskar.

1. Deallocate den virtuella datorn med hjälp av [az vm deallocate](/cli/azure/vm). I följande exempel frigörs den `myVM` virtuella datorn `myResourceGroup`som namnges i resursgruppen:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Konvertera den virtuella datorn till hanterade diskar med az [vm konvertera](/cli/azure/vm). Följande process konverterar den `myVM`virtuella datorn med namnet , inklusive OS-disken och alla datadiskar:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Starta den virtuella datorn efter konverteringen till hanterade diskar med az [vm-start](/cli/azure/vm). I följande exempel startar `myVM` den virtuella `myResourceGroup`datorn som namnges i resursgruppen .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Konvertera virtuella datorer i en tillgänglighetsuppsättning

Om de virtuella datorerna som du vill konvertera till hanterade diskar finns i en tillgänglighetsuppsättning måste du först konvertera tillgänglighetsuppsättningen till en hanterad tillgänglighetsuppsättning.

Alla virtuella datorer i tillgänglighetsuppsättningen måste frigöras innan du konverterar tillgänglighetsuppsättningen. Planera att konvertera alla virtuella datorer till hanterade diskar efter att själva tillgänglighetsuppsättningen har konverterats till en hanterad tillgänglighetsuppsättning. Starta sedan alla virtuella datorer och fortsätta fungera som vanligt.

1. Lista alla virtuella datorer i en tillgänglighetsuppsättning med hjälp av [listan för tillgänglighet för virtuell dator](/cli/azure/vm/availability-set). I följande exempel visas alla virtuella datorer `myAvailabilitySet` i tillgänglighetsuppsättningen som nämns i resursgruppen : `myResourceGroup`

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Deallocate alla virtuella datorer med hjälp av [az vm deallocate](/cli/azure/vm). I följande exempel frigörs den `myVM` virtuella datorn `myResourceGroup`som namnges i resursgruppen:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Konvertera tillgänglighetsuppsättningen med hjälp av [az vm-konvertering av tillgänglighetsuppsättning](/cli/azure/vm/availability-set). I följande exempel konverteras tillgänglighetsuppsättningen som namnges `myAvailabilitySet` i resursgruppen med namnet `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Konvertera alla virtuella datorer till hanterade diskar med az [vm konvertera](/cli/azure/vm). Följande process konverterar den `myVM`virtuella datorn med namnet , inklusive OS-disken och alla datadiskar:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Starta alla virtuella datorer efter konverteringen till hanterade diskar med az [vm-start](/cli/azure/vm). I följande exempel startar `myVM` den virtuella `myResourceGroup`datorn som heter i resursgruppen :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Konvertera med Azure-portalen

Du kan också konvertera ohanterade diskar till hanterade diskar med Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn i listan över virtuella datorer i portalen.
3. Välj **Diskar** på menyn i bladet för den virtuella datorn.
4. Högst upp på **diskbladet** väljer du **Migrera till hanterade diskar**.
5. Om den virtuella datorn har en tillgänglighetsuppsättning visas en varning på bladet **Migrera till hanterade diskar** som du behöver för att konvertera tillgänglighetsuppsättningen först. Varningen bör ha en länk som du kan klicka på för att konvertera tillgänglighetsuppsättningen. När tillgänglighetsuppsättningen har konverterats eller om den virtuella datorn inte har en tillgänglighetsuppsättning klickar du på **Migrera** för att starta processen med att migrera diskarna till hanterade diskar.

Den virtuella datorn stoppas och startas om när migreringen har slutförts.

## <a name="next-steps"></a>Nästa steg

Mer information om lagringsalternativ finns i [översikt över hanterade diskar i Azure](../windows/managed-disks-overview.md).
