---
title: Ändra storlek på en Linux VM med Azure CLI | Microsoft Docs
description: Så här att skala upp eller ned en Linux-dator genom att ändra virtuella datorstorlek.
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
ms.openlocfilehash: 0b8802c91ceb59d391dc27a71da905de9c15a1dc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993239"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Ändra storlek på en Linux-dator med Azure CLI 

När du etablerar en virtuell dator (VM) kan du kan skala den virtuella datorn upp eller ned genom att ändra den [VM-storlek][vm-sizes]. I vissa fall måste du frigöra den virtuella datorn först. Du måste frigöra den virtuella datorn om önskad storlek inte är tillgänglig på klustret maskinvara som är värd för den virtuella datorn. Den här artikeln beskriver hur du ändrar storlek på en Linux VM med Azure CLI. 

## <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator
Om du vill ändra storlek på en virtuell dator, du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index#az_login).

1. Visa en lista över tillgängliga VM-storlekar på maskinvara klustret där den virtuella datorn finns med [az vm list-vm-resize-options](/cli/azure/vm#az_vm_list_vm_resize_options). I följande exempel visar en lista över VM-storlekar för den virtuella datorn med namnet `myVM` i resursgruppen `myResourceGroup` region:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Om önskad storlek för virtuell dator visas, ändra storlek på den virtuella datorn med [az vm ändra storlek på](/cli/azure/vm#az_vm_resize). I följande exempel ändrar storlek på den virtuella datorn med namnet `myVM` till den `Standard_DS3_v2` storlek:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Den virtuella datorn startas om under den här processen. Efter omstarten mappas ditt befintliga operativsystem och datadiskar. Allt på den temporära disken tas bort.

3. Om önskad storlek för virtuell dator inte visas, måste du först frigöra den virtuella datorn med [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Den här processen kan den virtuella datorn ändras till valfri storlek som är tillgängliga att regionen stöder och sedan startats. Följande steg frigöra, ändra storlek på och sedan starta den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Avallokera den virtuella datorn också Frigör några dynamiska IP-adresser som tilldelas den virtuella datorn. Operativsystemet och datadiskarna påverkas inte.

## <a name="next-steps"></a>Nästa steg
För extra skalbarhet, kör flera VM-instanser och skala ut. Mer information finns i [skala automatiskt Linux-datorer i Virtual Machine Scale Sets][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
