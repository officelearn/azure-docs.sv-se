---
title: Kopiera en Linux VM som använder Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en kopia av din virtuella Azure Linux-datorn med hjälp av Azure CLI och hanterade diskar.
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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 8d250f1289c3757d5ea862a1c195dde6f8efb0eb
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938272"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Skapa en kopia av en Linux-VM med hjälp av Azure CLI och hanterade diskar


Den här artikeln visar hur du skapar en kopia av din Azure virtuell dator (VM kör Linux med hjälp av Azure CLI 2.0 och Azure Resource Manager-distributionsmodellen). 

Du kan också [överför och skapa en virtuell dator från en virtuell Hårddisk](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Förutsättningar


-   Installera [Azure CLI 2.0](/cli/azure/install-az-cli2)

-   Logga in på ett Azure-konto med [az inloggningen](/cli/azure/reference-index#az_login).

-   Ha en Azure VM ska användas som källa för din kopia.

## <a name="step-1-stop-the-source-vm"></a>Steg 1: Stoppa den Virtuella källdatorn


Frigör den Virtuella källdatorn med hjälp av [az vm frigöra](/cli/azure/vm#az_vm_deallocate).
I följande exempel tar bort den virtuella datorn med namnet **myVM** i resursgruppen **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Steg 2: Kopiera den Virtuella källdatorn


Om du vill kopiera en virtuell dator kan du skapa en kopia av den underliggande virtuella hårddisken. Den här processen skapar en särskild virtuell Hårddisk som en hanterad Disk som innehåller samma konfiguration och inställningar som den Virtuella källdatorn.

Mer information om Azure Managed Disks finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Lista över varje virtuell dator och namnet på dess OS-disken med [az vm listan](/cli/azure/vm#az_vm_list). I följande exempel visar en lista över alla virtuella datorer i resursgruppen med namnet **myResourceGroup**:
    
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

1.  Kopiera disken genom att skapa en ny hanteras disk med hjälp av [az disk skapa](/cli/azure/disk#az_disk_create). I följande exempel skapas en disk med namnet **myCopiedDisk** från hanterade disken med namnet **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Kontrollera hanterade diskar nu i resursgruppen med hjälp av [az Disklista](/cli/azure/disk#az_disk_list). I följande exempel visar de hantera diskarna i resursgruppen med namnet **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Steg 3: Konfigurera ett virtuellt nätverk


Följande valfria steg skapar du ett nytt virtuellt nätverk, undernät, offentliga IP-adressen och virtuella nätverksgränssnittskortet (NIC).

Om du kopierar en virtuell dator för att felsöka ändamål eller ytterligare distributioner kan du inte vill använda en virtuell dator i ett befintligt virtuellt nätverk.

Om du vill skapa en virtuell nätverksinfrastruktur för de kopierade virtuella datorerna, följer du följande steg. Om du inte vill skapa ett virtuellt nätverk går du till [steg 4: skapa en virtuell dator](#step-4-create-a-vm).

1.  Skapa det virtuella nätverket med hjälp av [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med namnet **myVnet** och ett undernät med namnet **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Skapa en offentlig IP-adress med hjälp av [az nätverket offentliga IP-skapa](/cli/azure/network/public-ip#az_network_public_ip_create). I följande exempel skapas en offentlig IP-adress med namnet **myPublicIP** med DNS-namnet på **mypublicdns**. (DNS-namnet måste vara unikt, så ange ett unikt namn)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Skapa nätverkskortet använder [az nätverket nic skapa](/cli/azure/network/nic#az_network_nic_create).
    I följande exempel skapas ett nätverkskort med namnet **myNic** som är kopplad till den **mySubnet** undernät:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Steg 4: Skapa en virtuell dator

Nu kan du skapa en virtuell dator med hjälp av [az vm skapa](/cli/azure/vm#az_vm_create).

Ange den kopierade hantera disken ska användas som OS-disken (--bifoga-os-disk), enligt följande:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Nästa steg

Information om hur du använder Azure CLI för att hantera den nya virtuella datorn finns [Azure CLI-kommandona för Azure Resource Manager](../azure-cli-arm-commands.md).
