---
title: Skapa en kopia av Linux-VM med Azure CLI 1.0 | Microsoft Docs
description: "Lär dig hur du skapar en kopia av den virtuella Azure Linux-datorn med Azure CLI 1.0 i Resource Manager-distributionsmodellen"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 62ae54f3596c9383cbf3b401fcfdb42ecfdee63c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Skapa en kopia av en Linux-dator som körs på Azure med Azure CLI 1.0
Den här artikeln visar hur du skapar en kopia av din Azure virtuell dator (VM kör Linux med hjälp av Resource Manager-distributionsmodellen). Först du kopiera över operativsystemet och datadiskar till en ny behållare och sedan konfigurera nätverksresurserna och skapa den nya virtuella datorn.

Du kan också [överför och skapa en virtuell dator från anpassade diskavbildning](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- Azure CLI 1.0 – vårt CLI för den klassiska distributionsmodellen och Resource Manager-distributionsmodellen (den här artikeln)
- [Azure CLI 2.0](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="before-you-begin"></a>Innan du börjar
Se till att du uppfyller följande krav innan du startar stegen:

* Du har den [Azure CLI](../../cli-install-nodejs.md) hämtas och installeras på din dator. 
* Du måste också information om dina befintliga virtuella Azure Linux-datorn:

| Information om källdatorn VM | Hämta den |
| --- | --- |
| VM-namn |`azure vm list` |
| Resursgruppens namn |`azure vm list` |
| Plats |`azure vm list` |
| Lagringskontonamnet |`azure storage account list -g <resourceGroup>` |
| Behållarens namn |`azure storage container list -a <sourcestorageaccountname>` |
| Namn på datakälla VM VHD-filen |`azure storage blob list --container <containerName>` |

* Du behöver göra vissa val om den nya virtuella datorn:   <br> -Behållarnamn   <br> Namn på virtuell dator-   <br> VM - storlek   <br> vNet - namn   <br> -Undernätsnamn   <br> -IP-namn   <br> NIC - namn

## <a name="login-and-set-your-subscription"></a>Logga in och ange din prenumeration
1. Logga in CLI.

    ```azurecli
    azure login
    ```
2. Kontrollera att du är i läget Resource Manager.

    ```azurecli
    azure config mode arm
    ```
3. Ange korrekt prenumeration. Du kan använda ”azure-konto ' att se alla dina prenumerationer.

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn
Stoppa och ta bort den Virtuella källdatorn. Du kan använda 'azure vm list' och hämta en lista över alla de virtuella datorerna i din prenumeration och resursen gruppnamn.

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>Kopiera den virtuella Hårddisken
Du kan kopiera den virtuella Hårddisken från källservern. lagring till målet med den `azure storage blob copy start`. I det här exemplet det dags att kopiera den virtuella Hårddisken till samma lagringskonto, men en annan behållare.

Om du vill kopiera den virtuella Hårddisken till en annan behållare i samma lagringskonto, skriver du:

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Konfigurera det virtuella nätverket för den nya virtuella datorn
Ange ett virtuellt nätverk och nätverkskort för den nya virtuella datorn. 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>Skapa ny virtuell dator
Nu kan du skapa en virtuell dator från den överförda virtuella hårddisken [med hjälp av en resource manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) eller via CLI genom att ange URI: N till den kopierade disken genom att skriva:

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>Nästa steg
Information om hur du använder Azure CLI för att hantera den nya virtuella datorn finns [Azure CLI-kommandona för Azure Resource Manager](../azure-cli-arm-commands.md).

