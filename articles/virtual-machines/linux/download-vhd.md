---
title: Hämta en Linux-VHD från Azure | Microsoft Docs
description: Hämta en Linux-VHD med hjälp av Azure CLI och Azure-portalen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f72d49a3ab204ce64eb89d0f05630b640c138e0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61390386"
---
# <a name="download-a-linux-vhd-from-azure"></a>Hämta en Linux-VHD från Azure

I den här artikeln får du lära dig hur du hämtar en fil för Linux-virtuell hårddisk (VHD) från Azure med hjälp av Azure CLI och Azure-portalen. 

Om du inte redan har gjort det installerar [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell Hårddisk kan inte hämtas från Azure om den är ansluten till en aktiv virtuell dator. Du måste stoppa den virtuella datorn för att ladda ned en virtuell Hårddisk. Om du vill använda en virtuell Hårddisk som en [bild](tutorial-custom-images.md) för att skapa andra virtuella datorer med nya diskar, måste du avetablera och generalisera operativsystemet finns i filen och stoppa den virtuella datorn. Om du vill använda den virtuella Hårddisken som en disk för en ny instans av en befintlig virtuell dator eller en datadisk, behöver du bara stoppa och frigör den virtuella datorn.

Om du vill använda den virtuella Hårddisken som en bild för att skapa andra virtuella datorer, gör du följande:

1. Använd SSH, kontonamnet och den offentliga IP-adressen för den virtuella datorn för att ansluta till det och ta bort etableringen av den. Du kan hitta den offentliga IP-adressen med [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Den + user-parameter tar också bort senast etablerade användarkontot. Om du gräddning autentiseringsuppgifter i till den virtuella datorn, lämnar denna + user-parameter. I följande exempel tar bort det senast etablerade användarkontot:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Logga in på ditt Azure-konto med [az-inloggning](https://docs.microsoft.com/cli/azure/reference-index).
3. Stoppa och frigör den virtuella datorn.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalisera den virtuella datorn. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Om du vill använda den virtuella Hårddisken som en disk för en ny instans av en befintlig virtuell dator eller en datadisk, gör du följande:

1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  Klicka på **Virtual Machines** på navmenyn.
3.  Välj den virtuella datorn i listan.
4.  Bladet för den virtuella datorn och klicka på **stoppa**.

    ![Stoppa virtuell dator](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generera SAS-URL

För att hämta VHD-filen, måste du generera en [signatur för delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL: en. När URL: en genereras, tilldelas en förfallotid till URL: en.

1.  På menyn på bladet för den virtuella datorn, **diskar**.
2.  Välj disken som operativsystemet för den virtuella datorn och klicka sedan på **exportera**.
3.  Klicka på **generera URL: en**.

    ![Skapa webbadress](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Ladda ned VHD

1.  Klicka på Hämta VHD-filen under den URL som har genererats.

    ![Ladda ned VHD](./media/download-vhd/export-download.png)

2.  Du kan behöva klicka på **spara** i webbläsaren för att starta nedladdningen. Standardnamnet för VHD-filen är *abcd*.

    ![Klicka på Spara i webbläsaren](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [ladda upp och skapa en Linux VM från anpassad disk med Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Hantera Azure-diskar av Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

