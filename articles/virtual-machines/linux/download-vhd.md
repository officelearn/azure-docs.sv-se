---
title: Ladda ned en Linux-VHD från Azure
description: Ladda ned en Linux-VHD med Azure CLI och Azure Portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 02c3ee483e6a31960fd5123070a49f568ac4c690
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968785"
---
# <a name="download-a-linux-vhd-from-azure"></a>Ladda ned en Linux-VHD från Azure

I den här artikeln får du lära dig hur du laddar ned en virtuell hård disk fil (VHD) från Azure med hjälp av Azure CLI och Azure Portal. 

Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2)om du inte redan gjort det.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell hård disk kan inte laddas ned från Azure om den är ansluten till en virtuell dator som körs. Du måste stoppa den virtuella datorn för att kunna ladda ned en virtuell hård disk. Om du vill använda en virtuell hård disk som en [avbildning](tutorial-custom-images.md) för att skapa andra virtuella datorer med nya diskar måste du avetablera och generalisera operativ systemet som finns i filen och stoppa den virtuella datorn. Om du vill använda den virtuella hård disken som en disk för en ny instans av en befintlig virtuell dator eller data disk, behöver du bara stoppa och frigöra den virtuella datorn.

Slutför följande steg för att använda den virtuella hård disken som en avbildning för att skapa andra virtuella datorer:

1. Använd SSH, konto namnet och den offentliga IP-adressen för den virtuella datorn för att ansluta till den och avetablera den. Du kan hitta den offentliga IP-adressen med [AZ Network Public-IP show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Parametern + User tar också bort det senast etablerade användar kontot. Om du arbetar med inloggnings uppgifter för den virtuella datorn lämnar du denna + User-parameter. I följande exempel tar vi bort det senast etablerade användar kontot:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Logga in på ditt Azure-konto med [AZ-inloggning](https://docs.microsoft.com/cli/azure/reference-index).
3. Stoppa och frigör den virtuella datorn.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalisera den virtuella datorn. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Slutför följande steg för att använda den virtuella hård disken som en disk för en ny instans av en befintlig virtuell dator eller data disk:

1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  På den vänstra menyn väljer du **Virtual Machines**.
3.  Välj den virtuella datorn i listan.
4.  På sidan för den virtuella datorn väljer du **stoppa**.

    ![Stoppa VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Skapa SAS-URL

Om du vill hämta VHD-filen måste du generera en URL för [signatur för delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . När URL: en genereras tilldelas URL: en förfallo tid.

1.  På menyn på sidan för den virtuella datorn väljer du **diskar**.
2.  Välj operativ system disk för den virtuella datorn och välj sedan **disk export**.
3.  Välj **skapa URL**.

    ![Generera URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Hämta VHD

1.  Under den URL som genererades väljer du **Hämta VHD-filen**.
**
    ![Ladda ned VHD-](./media/download-vhd/export-download.png)

2.  Du kan behöva välja **Spara** i webbläsaren för att starta nedladdningen. Standard namnet för VHD-filen är *ABCD*.

    ![Välj Spara i webbläsaren](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [laddar upp och skapar en virtuell Linux-dator från en anpassad disk med Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Hantera Azure-diskar i Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

