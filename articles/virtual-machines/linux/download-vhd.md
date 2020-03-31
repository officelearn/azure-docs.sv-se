---
title: Ladda ned en Virtuell Linux-hårddisk från Azure
description: Ladda ned en Virtuell Linux-hårddisk med Azure CLI och Azure-portalen.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 02c3ee483e6a31960fd5123070a49f568ac4c690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968785"
---
# <a name="download-a-linux-vhd-from-azure"></a>Ladda ned en Virtuell Linux-hårddisk från Azure

I den här artikeln får du lära dig hur du hämtar en VIRTUELLD -fil (Linux Virtual Hard Disk) från Azure med Azure CLI- och Azure-portalen. 

Om du inte redan har gjort det installerar du [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell hårddisk kan inte hämtas från Azure om den är kopplad till en virtuell dator som körs. Du måste stoppa den virtuella datorn för att hämta en virtuell hårddisk. Om du vill använda en virtuell hårddisk som en [avbildning](tutorial-custom-images.md) för att skapa andra virtuella datorer med nya diskar måste du avetablera och generalisera operativsystemet som finns i filen och stoppa den virtuella datorn. Om du vill använda den virtuella hårddisken som en disk för en ny instans av en befintlig virtuell dator eller datadisk behöver du bara stoppa och frigöra den virtuella datorn.

Så här använder du den virtuella hårddisken som en avbildning för att skapa andra virtuella datorer:

1. Använd SSH, kontonamnet och den offentliga IP-adressen för den virtuella datorn för att ansluta till den och avetablera den. Du kan hitta den offentliga [IP-adressen med az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Parametern +user tar också bort det senast etablerade användarkontot. Om du bakar kontoautentiseringsuppgifter till den virtuella datorn utelämnar du den här +användarparametern. I följande exempel tas det senast etablerade användarkontot bort:

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

Så här använder du den virtuella hårddisken som en disk för en ny instans av en befintlig virtuell dator eller datadisk:

1.  Logga in på [Azure-portalen](https://portal.azure.com/).
2.  Välj **Virtuella datorer**på menyn till vänster .
3.  Välj den virtuella datorn i listan.
4.  På sidan för den virtuella datorn väljer du **Stoppa**.

    ![Stoppa virtuell dator](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generera SAS-URL

Om du vill hämta VHD-filen måste du skapa en [SAS-URL (Shared Access Signature).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) När URL:en genereras tilldelas url:en förfallotid en förfallotid till webbadressen.

1.  Välj **Diskar**på menyn på sidan för den virtuella datorn.
2.  Välj operativsystemdisken för den virtuella datorn och välj sedan **Diskexport**.
3.  Välj **Generera URL**.

    ![Generera URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Ladda ner VHD

1.  Under url:en som genererades väljer du **Hämta VHD-filen**.
**
    ![Ladda ner VHD](./media/download-vhd/export-download.png)

2.  Du kan behöva välja **Spara** i webbläsaren för att starta hämtningen. Standardnamnet för VHD-filen är *abcd*.

    ![Välj Spara i webbläsaren](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [laddar upp och skapar en Virtuell Linux-dator från anpassad disk med Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Hantera Azure-diskar i Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

