---
title: Så här ändrar du storlek på en virtuell Linux-dator med Azure CLI
description: Så här skalar du upp eller skalar ned en virtuell Linux-dator genom att ändra den virtuella datorns storlek.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969261"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Ändra storlek på en virtuell Linux-dator med Azure CLI 

När du har etablerat en virtuell dator (VM) kan du skala den virtuella datorn uppåt eller nedåt genom att ändra [den virtuella datorns storlek][vm-sizes]. I vissa fall måste du frigöra den virtuella datorn först. Du måste frigöra den virtuella datorn om önskad storlek inte är tillgänglig i maskinvaruklustret som är värd för den virtuella datorn. I den här artikeln beskrivs hur du ändrar storlek på en Virtuell Linux-dator med Azure CLI. 

## <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator
Om du vill ändra storlek på en virtuell dator behöver du den senaste [Azure CLI](/cli/azure/install-az-cli2) installerad och inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

1. Visa listan över tillgängliga VM-storlekar i maskinvaruklustret där den virtuella datorn är värd för [az vm-list-vm-resize-options](/cli/azure/vm). I följande exempel visas vm-storlekar `myVM` för den `myResourceGroup` virtuella datorn som namnges i resursgruppsregionen:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Om önskad vm-storlek visas ändrar du storlek på den virtuella datorn med [az vm-storlek](/cli/azure/vm). I följande exempel ändras storleken `myVM` på `Standard_DS3_v2` den virtuella datorn till storleken:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Den virtuella datorn startas om under den här processen. Efter omstarten mappas ditt befintliga operativsystem och datadiskar om. Allt på den tillfälliga disken går förlorat.

3. Om den önskade vm-storleken inte anges måste du först frigöra den virtuella datorn med [az vm-deallocate](/cli/azure/vm). Den här processen gör att den virtuella datorn sedan kan ändra storlek till valfri storlek som är tillgänglig som regionen stöder och sedan startas. Följande steg frigör, ändrar storlek på och startar `myVM` sedan den `myResourceGroup`virtuella datorn som namnges i resursgruppen med namnet :
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Deallocating den virtuella datorn släpper också alla dynamiska IP-adresser som tilldelats den virtuella datorn. Operativsystemet och datadiskarna påverkas inte.

## <a name="next-steps"></a>Nästa steg
Om du vill ha ytterligare skalbarhet kör du flera VM-instanser och skalar ut. Mer information finns [i Skala Linux-datorer automatiskt i en skalningsuppsättning för virtuella datorer][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
