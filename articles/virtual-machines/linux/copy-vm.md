---
title: Kopiera en Linux VM med Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en kopia av din virtuell Linux-dator med hjälp av Azure CLI och Managed Disks.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: abc8c09a51104c81b827afb7055531df98691714
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328756"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Skapa en kopia av en Linux VM med hjälp av Azure CLI och Managed Disks

Den här artikeln visar hur du skapar en kopia av din Azure-dator (VM) som kör Linux med hjälp av Azure CLI och Azure Resource Manager-distributionsmodellen. 

Du kan också [ladda upp och skapa en virtuell dator från en virtuell Hårddisk](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Nödvändiga komponenter

-   Installera [Azure CLI](/cli/azure/install-az-cli2).

-   Logga in på ett Azure-konto med [az-inloggning](/cli/azure/reference-index#az-login).

-   Ha en Azure virtuell dator som ska användas som källa för din kopia.

## <a name="stop-the-source-vm"></a>Stoppa den Virtuella källdatorn

Frigör den Virtuella källdatorn med hjälp av [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
I följande exempel bort den virtuella datorn med namnet *myVM* i resursgruppen *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Kopiera den Virtuella källdatorn

Om du vill kopiera en virtuell dator, kan du skapa en kopia av den underliggande virtuella hårddisken. Den här processen skapar en specialiserad virtuell hårddisk (VHD) som en hanterad Disk som innehåller samma konfiguration och inställningar som virtuella datorn.

Mer information om Azure Managed Disks finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Lista varje virtuell dator och namnet på dess OS-disk med [az vm list](/cli/azure/vm#az-vm-list). I följande exempel visar en lista över alla virtuella datorer i resursgruppen med namnet *myResourceGroup*:
    
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

1.  Kopiera disken genom att skapa en ny hanterad disk och genom att använda [az disk skapa](/cli/azure/disk#az-disk-create). I följande exempel skapas en disk med namnet *myCopiedDisk* från den hantera disken med namnet *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Kontrollera de hanterade diskarna nu i resursgruppen med hjälp av [az disk list](/cli/azure/disk#az-disk-list). I följande exempel visar en lista över hanterade diskar i resursgruppen med namnet *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Följande valfria steg skapar ett nytt virtuellt nätverk, undernät, offentliga IP-adressen och virtuella nätverkskort (NIC).

Om du kopierar en virtuell dator för felsökning syften eller ytterligare distributioner kan du inte vill använda en virtuell dator i ett befintligt virtuellt nätverk.

Om du vill skapa en virtuell nätverksinfrastruktur för dina kopierade virtuella datorer följer du de efterföljande stegen. Om du inte vill skapa ett virtuellt nätverk, gå vidare till [skapa en virtuell dator](#create-a-vm).

1.  Skapa det virtuella nätverket med hjälp av [az network vnet skapa](/cli/azure/network/vnet#az-network-vnet-create). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* och ett undernät med namnet *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Skapa en offentlig IP-adress med hjälp av [az nätverket offentliga ip-skapa](/cli/azure/network/public-ip#az-network-public-ip-create). I följande exempel skapas en offentlig IP-adress med namnet *myPublicIP* med DNS-namnet på *mypublicdns*. (Eftersom DNS-namnet måste vara unikt, ange ett unikt namn.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Skapa nätverkskortet med hjälp av [az network nic skapa](/cli/azure/network/nic#az-network-nic-create).
    I följande exempel skapas ett nätverkskort med namnet *myNic* som är kopplad till den *mySubnet* undernät:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator med hjälp av [az vm skapa](/cli/azure/vm#az-vm-create).

Ange den kopierade hanterad disken som ska användas som OS-disk (`--attach-os-disk`), enligt följande:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Nästa steg

Information om hur du använder Azure CLI för att hantera din nya virtuella dator finns [Azure CLI-kommandon för Azure Resource Manager](../azure-cli-arm-commands.md).
