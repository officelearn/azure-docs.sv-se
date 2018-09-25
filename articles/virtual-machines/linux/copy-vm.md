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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 64b33fcd25582f6b1d3e7efe12aba85bb17c4cca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951210"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Skapa en kopia av en Linux VM med hjälp av Azure CLI och Managed Disks

Den här artikeln visar hur du skapar en kopia av din Azure-dator (VM) som kör Linux med Azure CLI och Azure Resource Manager-distributionsmodellen. 

Du kan också [ladda upp och skapa en virtuell dator från en virtuell Hårddisk](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Förutsättningar


-   Installera [Azure CLI](/cli/azure/install-az-cli2)

-   Logga in på ett Azure-konto med [az-inloggning](/cli/azure/reference-index#az_login).

-   Ha en Azure virtuell dator som ska användas som källa för din kopia.

## <a name="step-1-stop-the-source-vm"></a>Steg 1: Stoppa den Virtuella källdatorn


Frigör den Virtuella källdatorn med hjälp av [az vm deallocate](/cli/azure/vm#az_vm_deallocate).
I följande exempel bort den virtuella datorn med namnet **myVM** i resursgruppen **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Steg 2: Kopiera den Virtuella källdatorn


Om du vill kopiera en virtuell dator, kan du skapa en kopia av den underliggande virtuella hårddisken. Den här processen skapar en specialiserad virtuell Hårddisk som en hanterad Disk som innehåller samma konfiguration och inställningar som virtuella datorn.

Mer information om Azure Managed Disks finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Lista varje virtuell dator och namnet på dess OS-disk med [az vm list](/cli/azure/vm#az_vm_list). I följande exempel visar en lista över alla virtuella datorer i resursgruppen med namnet **myResourceGroup**:
    
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

1.  Kopiera disken genom att skapa en ny hanterad disk med hjälp av [az disk skapa](/cli/azure/disk#az_disk_create). I följande exempel skapas en disk med namnet **myCopiedDisk** från den hantera disken med namnet **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Kontrollera de hanterade diskarna nu i resursgruppen med hjälp av [az disk list](/cli/azure/disk#az_disk_list). I följande exempel visar en lista över hanterade diskar i resursgruppen med namnet **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Steg 3: Konfigurera ett virtuellt nätverk


Följande valfria steg skapar ett nytt virtuellt nätverk, undernät, offentliga IP-adressen och virtuella nätverkskort (NIC).

Om du kopierar en virtuell dator för felsökning syften eller ytterligare distributioner kan du inte vill använda en virtuell dator i ett befintligt virtuellt nätverk.

Om du vill skapa en virtuell nätverksinfrastruktur för dina kopierade virtuella datorer följer du de efterföljande stegen. Om du inte vill skapa ett virtuellt nätverk, gå vidare till [steg 4: skapa en virtuell dator](#step-4-create-a-vm).

1.  Skapa det virtuella nätverket med hjälp av [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med namnet **myVnet** och ett undernät med namnet **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Skapa en offentlig IP-adress med hjälp av [az nätverket offentliga ip-skapa](/cli/azure/network/public-ip#az_network_public_ip_create). I följande exempel skapas en offentlig IP-adress med namnet **myPublicIP** med DNS-namnet på **mypublicdns**. (DNS-namnet måste vara unikt, så ange ett unikt namn)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Skapa nätverkskort med hjälp av [az network nic skapa](/cli/azure/network/nic#az_network_nic_create).
    I följande exempel skapas ett nätverkskort med namnet **myNic** som är kopplad till den **mySubnet** undernät:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Steg 4: Skapa en virtuell dator

Nu kan du skapa en virtuell dator med hjälp av [az vm skapa](/cli/azure/vm#az_vm_create).

Ange den kopierade hanterad disken som ska användas som OS-disk (--bifoga-os-disk), enligt följande:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Nästa steg

Information om hur du använder Azure CLI för att hantera din nya virtuella dator finns [Azure CLI-kommandon för Azure Resource Manager](../azure-cli-arm-commands.md).
