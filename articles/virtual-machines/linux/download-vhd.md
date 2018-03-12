---
title: "Hämta en Linux-VHD från Azure | Microsoft Docs"
description: "Hämta en Linux VHD med Azure CLI och Azure portal."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: davidmu
ms.openlocfilehash: 20af28dd4caa6ee5487b9a2ed83715b9b16fad48
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="download-a-linux-vhd-from-azure"></a>Hämta en Linux-VHD från Azure

I den här artikeln får du lära dig hur du hämtar en [Linux virtuell hårddisk (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) filen från Azure med hjälp av Azure CLI och Azure-portalen. 

Virtuella datorer (VM) i Azure används [diskar](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som en plats att lagra ett operativsystem, program och data. Alla virtuella Azure-datorer har minst två diskar – en disk i Windows-operativsystem och en tillfällig disk. Operativsystemdisken har skapats ursprungligen från en avbildning och både operativsystemdisken och image är virtuella hårddiskar som lagras i ett Azure storage-konto. Virtuella datorer kan också ha en eller flera datadiskar som lagras också som virtuella hårddiskar.

Om du inte redan har gjort det installerar [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell Hårddisk kan inte hämtas från Azure om den är kopplad till en aktiv virtuell dator. Du måste stoppa den virtuella datorn för att ladda ned en virtuell Hårddisk. Om du vill använda en virtuell Hårddisk som en [bild](tutorial-custom-images.md) för att skapa andra virtuella datorer med nya diskar, måste du ta bort etableringen och generalisera operativsystemet finns i filen och stoppa den virtuella datorn. Om du vill använda den virtuella Hårddisken som en disk för en ny instans av en befintlig virtuell dator eller en datadisk, behöver du bara stoppa och ta bort den virtuella datorn.

Om du vill använda den virtuella Hårddisken som en bild för att skapa andra virtuella datorer, gör du följande:

1. Använda SSH, kontonamnet och offentliga IP-adressen för den virtuella datorn för att ansluta till den och ta bort etableringen av den. Den + user-parameter tar också bort det senaste kontot för etablerad användare. Om du gräddning autentiseringsuppgifter i för att den virtuella datorn, lämnar ut detta + user-parameter. I följande exempel tar bort det senaste kontot för etablerad användare:

    ```bash
    ssh azureuser@40.118.249.235
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Logga in på ditt Azure-konto med [az inloggningen](https://docs.microsoft.com/cli/azure/reference-index#az_login).
3. Stoppa och ta bort den virtuella datorn.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalisera den virtuella datorn. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Om du vill använda den virtuella Hårddisken som en disk för en ny instans av en befintlig virtuell dator eller en datadisk, gör du följande:

1.  Logga in på [Azure-portalen](https://portal.azure.com/).
2.  Klicka på **Virtual Machines** på navmenyn.
3.  Välj den virtuella datorn från listan.
4.  På bladet för den virtuella datorn klickar du på **stoppa**.

    ![Stoppa VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generera en SAS-URL

Om du vill hämta VHD-filen måste du generera en [signatur för delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL. När URL: en genereras tilldelas en förfallotid till URL: en.

1.  Klicka på menyn i bladet för den virtuella datorn, **diskar**.
2.  Välj disken som operativsystemet för den virtuella datorn och klicka sedan på **exportera**.
3.  Klicka på **generera URL**.

    ![Generera en URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Hämta VHD

1.  Klicka på Hämta VHD-filen under den URL som har genererats.

    ![Hämta VHD](./media/download-vhd/export-download.png)

2.  Du kan behöva klicka på **spara** i webbläsaren om du vill starta nedladdningen. Standardnamnet för VHD-filen är *abcd*.

    ![Klicka på Spara i webbläsaren](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [överför och skapa en Linux VM från anpassade disken med Azure CLI 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Hantera Azure-diskarna Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

