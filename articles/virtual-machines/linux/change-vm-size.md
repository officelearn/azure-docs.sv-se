---
title: Hur du ändrar storlek på en Linux VM med Azure CLI 2.0 | Microsoft Docs
description: Så här skala upp eller ned en Linux-dator genom att ändra VM-storlek.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e9d15a6927c0fea8bc79d359817158c167433d25
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="resize-a-linux-virtual-machine-using-cli-20"></a>Ändra storlek på en Linux-dator med hjälp av CLI 2.0

När du etablera en virtuell dator (VM) du kan skala VM uppåt eller nedåt genom att ändra den [VM-storlek][vm-sizes]. I vissa fall måste du frigöra den virtuella datorn först. Du måste frigöra den virtuella datorn om önskad storlek inte är tillgänglig på klustret maskinvara som är värd för den virtuella datorn. Den här artikeln beskriver hur du ändrar storlek på en Linux VM med Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](change-vm-size-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator
Om du vill ändra storlek på en virtuell dator, måste senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/reference-index#az_login).

1. Visa listan över tillgängliga storlekar på VM maskinvara klustret där den virtuella datorn körs med [az vm-vm-storlek-alternativ för](/cli/azure/vm#az_vm_list_vm_resize_options). I följande exempel visar VM-storlekar för den virtuella datorn med namnet `myVM` i resursgruppen `myResourceGroup` region:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Om den önskade Virtuella datorstorleken visas ändra storlek på den virtuella datorn med [az vm ändra storlek på](/cli/azure/vm#az_vm_resize). I följande exempel ändrar storlek på den virtuella datorn med namnet `myVM` till den `Standard_DS3_v2` storlek:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Den virtuella datorn startas om under den här processen. Efter omstarten mappas befintlig OS- och datadiskar om. Allt på den tillfälliga disken går förlorad.

3. Om inte den önskade Virtuella datorstorleken visas måste du först ta bort den virtuella datorn med [az vm frigöra](/cli/azure/vm#az_vm_deallocate). Den här processen kan den virtuella datorn ska ändras till valfri storlek som är tillgängliga att regionen stöder och sedan startas. Följande steg frigöra, ändra storlek på och sedan starta den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Det har frigjorts VM också Frigör alla dynamiska IP-adresser som tilldelats den virtuella datorn. Operativsystemet och datadiskarna påverkas inte.

## <a name="next-steps"></a>Nästa steg
För ytterligare utbyggbarhet köra flera VM-instanser och skala ut. Mer information finns i [skala automatiskt Linux-datorer i en virtuell dator Skaluppsättning][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
