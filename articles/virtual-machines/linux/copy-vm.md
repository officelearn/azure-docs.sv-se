---
title: Kopiera en Virtuell Linux-dator med Azure CLI
description: Lär dig hur du skapar en kopia av din Virtuella Azure Linux-dator med Azure CLI och Managed Disks.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: ed8574133eafe751699e90ea8cae832ee649fb00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969585"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Skapa en kopia av en virtuell Linux-dator med hjälp av Azure CLI och Hanterade diskar

Den här artikeln visar hur du skapar en kopia av din virtuella Azure-dator (VM) som kör Linux med hjälp av Azure CLI och Azure Resource Manager-distributionsmodellen. 

Du kan också [ladda upp och skapa en virtuell dator från en virtuell hårddisk](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Krav

-   Installera [Azure CLI](/cli/azure/install-az-cli2).

-   Logga in på ett Azure-konto med [az-inloggning](/cli/azure/reference-index#az-login).

-   Har en Azure VM att använda som källa för ditt exemplar.

## <a name="stop-the-source-vm"></a>Stoppa käll-VM

Deallocate källan VM med hjälp av [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
I följande exempel frigörs den virtuella datorn med namnet *myVM* i resursgruppen *myResourceGroup:*

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Kopiera käll-VM

Om du vill kopiera en virtuell dator skapar du en kopia av den underliggande virtuella hårddisken. Den här processen skapar en specialiserad virtuell hårddisk (VHD) som en hanterad disk som innehåller samma konfiguration och inställningar som källdatorn.

Mer information om Azure Managed Disks finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Lista varje virtuell dator och namnet på dess OS-disk med [az vm-lista](/cli/azure/vm#az-vm-list). I följande exempel visas alla virtuella datorer i resursgruppen *myResourceGroup:*
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Kopiera disken genom att skapa en ny hanterad disk och med hjälp av [az disk create](/cli/azure/disk#az-disk-create). I följande exempel skapas en disk med namnet *myCopiedDisk* från den hanterade disken som heter *myDisk:*

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Verifiera de hanterade diskarna i resursgruppen med hjälp av [az-disklistan](/cli/azure/disk#az-disk-list). I följande exempel visas de hanterade diskarna i resursgruppen *myResourceGroup:*

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Följande valfria steg skapar ett nytt virtuellt nätverk, undernät, offentlig IP-adress och nätverkskort (NIC).

Om du kopierar en virtuell dator för felsökning eller ytterligare distributioner kanske du inte vill använda en virtuell dator i ett befintligt virtuellt nätverk.

Om du vill skapa en virtuell nätverksinfrastruktur för dina kopierade virtuella datorer följer du de närmaste stegen. Om du inte vill skapa ett virtuellt nätverk går du till [Skapa en virtuell dator](#create-a-vm).

1.  Skapa det virtuella nätverket med hjälp av [az nätverk vnet skapa](/cli/azure/network/vnet#az-network-vnet-create). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* och ett undernät med namnet *mySubnet:*

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Skapa en offentlig IP med hjälp av [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). I följande exempel skapas en offentlig IP med namnet *myPublicIP* med DNS-namnet *mypublicdns*. (Eftersom DNS-namnet måste vara unikt anger du ett unikt namn.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Skapa nätverkskortet med hjälp av [az network nic create](/cli/azure/network/nic#az-network-nic-create).
    I följande exempel skapas ett nätverkskort med namnet *myNic* som är kopplat till *undernätet mySubnet:*

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator med hjälp av [az vm create](/cli/azure/vm#az-vm-create).

Ange den kopierade hanterade disken som`--attach-os-disk`ska användas som OS-disk ( ), enligt följande:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du använder ett [delat bildgalleri](shared-images.md) för att hantera VM-avbildningar.
