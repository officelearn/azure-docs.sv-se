---
title: Expandera OS-disken på Linux VM med Azure CLI 1.0 | Microsoft Docs
description: Lär dig hur du expandera den virtuella disken operativsystem (OS) på en Linux VM som använder Azure CLI 1.0 och Resource Manager-distributionsmodellen
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: f81054727bb1f0e8ffa752783e866a72d573589d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30905341"
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Expandera OS-disk på en Linux VM som använder Azure CLI med Azure CLI 1.0
Standardstorleken för virtuell hårddisk för operativsystem (OS) är vanligtvis 30 GB på en Linux-dator (VM) i Azure. Du kan [lägga till datadiskar](add-disk.md) att tillhandahålla för ytterligare lagringsutrymme, men du kan också expandera OS-disk. Den här artikeln beskriver hur du expandera OS-disk för en Linux-VM med hjälp av ohanterade diskar med Azure CLI 1.0.

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#prerequisites) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](expand-disks.md) – vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="prerequisites"></a>Förutsättningar
Du behöver den [senaste Azure CLI 1.0](../../cli-install-nodejs.md) installerad och inloggad på ett [Azure-konto](https://azure.microsoft.com/pricing/free-trial/) med hjälp av hanteraren för filserverresurser på följande sätt:

```azurecli
azure config mode arm
```

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar *myResourceGroup* och *myVM*.

## <a name="expand-os-disk"></a>Expandera operativsystemets disk

1. Det går inte att utföra åtgärder på virtuella hårddiskar med den virtuella datorn körs. I följande exempel stoppar och tar bort den virtuella datorn med namnet *myVM* i resursgrupp med namnet *myResourceGroup*:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` Frigör inte beräkningsresurserna. Använd om du vill frigöra beräkningsresurser `azure vm deallocate`. Den virtuella datorn måste frigöras för att expandera den virtuella hårddisken.

2. Uppdatera storleken på en ohanterad OS disk med hjälp av den `azure vm set` kommando. I följande exempel uppdateras den virtuella datorn med namnet *myVM* i resursgrupp med namnet *myResourceGroup* ska *50* GB:

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. Starta den virtuella datorn på följande sätt:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH till den virtuella datorn med rätt autentiseringsuppgifter. Använd för att kontrollera att OS-disken har ändrats, `df -h`. Följande exempel visas den primära partitionen (*/dev/sda1*) är nu 50 GB:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Nästa steg
Om du behöver ytterligare lagringsutrymme du också [lägga till diskar till en Linux-VM](add-disk.md). Mer information om diskkryptering finns [kryptera diskar på en Linux VM som använder Azure CLI](encrypt-disks.md).
