---
title: Skapa avbildningar av virtuella datorer för Azure Stack Edge GPU-enhet
description: Beskriver hur du skapar virtuella Linux-eller Windows-avbildningar som ska användas med Azure Stack Edge GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: c7730b776ef1d7e1d5e372049395492641b839ff
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146019"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-device"></a>Skapa anpassade VM-avbildningar för din Azure Stack Edge-enhet

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

För att distribuera virtuella datorer på din Azure Stack Edge-enhet måste du kunna skapa anpassade VM-avbildningar som du kan använda för att skapa virtuella datorer. I den här artikeln beskrivs de steg som krävs för att skapa anpassade virtuella Windows-avbildningar som du kan använda för att distribuera virtuella datorer på din Azure Stack Edge-enhet.

## <a name="vm-image-workflow"></a>Arbets flöde för VM-avbildning

Arbets flödet kräver att du skapar en virtuell dator i Azure, anpassar den virtuella datorn, generaliserar och sedan laddar ned den virtuella hård disken som motsvarar den virtuella datorn. Den här generaliserade virtuella hård disken överförs till Azure Stack Edge, den hanterade disken skapas från den virtuella hård disken, avbildningen skapas från den hanterade disken och slutligen skapas virtuella datorer från avbildningen.   

Mer information finns i [distribuera en virtuell dator på din Azure Stack Edge-enhet med Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Skapa en anpassad VM-avbildning för Windows

Utför följande steg för att skapa en Windows VM-avbildning.

1. Skapa en virtuell Windows-dator. Mer information finns i [Självstudier: skapa och hantera virtuella Windows-datorer med Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Ladda ned en befintlig operativ system disk.

    - Följ stegen i [Hämta en virtuell hård disk](../virtual-machines/windows/download-vhd.md).

    - Använd följande `sysprep` kommando i stället för vad som beskrivs i föregående procedur.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Du kan också läsa [Översikt över Sysprep (System preparation)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Använd den här virtuella hård disken för att skapa och distribuera en virtuell dator på din Azure Stack Edge-enhet.

## <a name="create-a-linux-custom-vm-image"></a>Skapa en anpassad virtuell dator avbildning för Linux

Utför följande steg för att skapa en virtuell Linux-avbildning.

1. Skapa en virtuell Linux-dator. Mer information finns i [Självstudier: skapa och hantera virtuella Linux-datorer med Azure CLI](../virtual-machines/linux/tutorial-manage-vm.md).

2. [Ladda ned befintlig OS-disk](../virtual-machines/linux/download-vhd.md).

Använd den här virtuella hård disken för att skapa och distribuera en virtuell dator på din Azure Stack Edge-enhet. Du kan använda följande två Azure Marketplace-avbildningar för att skapa anpassade Linux-avbildningar:

|Objekt namn  |Beskrivning  |Utgivare  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu-servern är världens mest populära Linux för moln miljöer.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux är en av de mest populära Linux-distributionerna.     |credativ|

En fullständig lista över Azure Marketplace-avbildningar som kan fungera (för närvarande inte testas) finns på [Azure Marketplace-objekt som är tillgängliga för Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910).


## <a name="next-steps"></a>Nästa steg

[Distribuera virtuella datorer på din Azure Stack Edge-enhet](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
