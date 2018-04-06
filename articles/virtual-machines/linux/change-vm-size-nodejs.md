---
title: Hur du ändrar storlek på en Linux VM med Azure CLI 1.0 | Microsoft Docs
description: Så här skala upp eller ned en Linux-dator genom att ändra VM-storlek.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7bb5ad082336c92a397f2590d9b06e94552c8744
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="resize-a-linux-vm-with-azure-cli-10"></a>Ändra storlek på en virtuell Linux-dator med Azure CLI 1.0

## <a name="overview"></a>Översikt

När du etablera en virtuell dator (VM) du kan skala VM uppåt eller nedåt genom att ändra den [VM-storlek][vm-sizes]. I vissa fall måste du frigöra den virtuella datorn först. Detta kan inträffa om den nya storleken inte är tillgänglig på klustret maskinvara som är värd för den virtuella datorn.

Den här artikeln visar hur du ändrar storlek på en Linux VM som använder den [Azure CLI][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#resize-a-linux-vm) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributionsmodellen resurshantering


## <a name="resize-a-linux-vm"></a>Ändra storlek på en virtuell Linux-dator
Utför följande steg om du vill ändra en virtuell dator.

1. Kör följande kommando för CLI. Det här kommandot visar storlek på Virtuella datorer som är tillgängliga på maskinvara klustret där den virtuella datorn finns.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Om önskad storlek visas kör du följande kommando för att ändra storlek på den virtuella datorn.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    Den virtuella datorn startas om under den här processen. Efter omstarten ska befintlig OS- och datadiskar som avbildas. Allt på den tillfälliga disken går förlorade.
   
    Använd den `--enable-boot-diagnostics` aktiverar [starta diagnostik][boot-diagnostics], för att logga eventuella fel som rör start.
3. Annars önskad storlek inte visas kör följande kommandon för att frigöra den virtuella datorn, ändra storlek på den och sedan starta om den virtuella datorn.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > Det har frigjorts VM också Frigör alla dynamiska IP-adresser som tilldelats den virtuella datorn. Operativsystemet och datadiskarna påverkas inte.
   > 
   > 

## <a name="next-steps"></a>Nästa steg
För ytterligare utbyggbarhet köra flera VM-instanser och skala ut. Mer information finns i [skala automatiskt Linux-datorer i en virtuell dator Skaluppsättning][scale-set]. 

<!-- links -->

[azure-cli]:../../cli-install-nodejs.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
