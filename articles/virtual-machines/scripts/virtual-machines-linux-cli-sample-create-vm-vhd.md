---
title: "Azure CLI Script exempel - skapa en virtuell dator med en virtuell Hårddisk | Microsoft Docs"
description: "Azure CLI-skript Sample - skapa en virtuell dator med hjälp av en virtuell hårddisk."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 6234473d9f7f0eb18ea85e52273eb82a9ce04da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Skapa en virtuell dator med en virtuell hårddisk

Det här exemplet skapar en virtuell dator med en virtuell Hårddisk.
En resursgrupp, ett lagringskonto och en behållare skapas och den skapar en virtuell dator genom att överföra den virtuella Hårddisken till behållaren.
Ersätter den ssh offentlig nyckel med den offentliga nyckeln så som du har åtkomst till den virtuella datorn.

Du behöver en startbar virtuell Hårddisk.
Du kan hämta den virtuella Hårddisken som vi använde från https://azclisamples.blob.core.windows.net/vhds/sample.vhd eller använda din egen VHD. Skriptet söker efter `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella, tillgänglighetsuppsättning, belastningsutjämnare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [AZ lagringskontolistan](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_list) | Visar storage-konton |
| [AZ check-lagringskontonamnet](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_check_name) | Kontrollerar att namnet på ett lagringskonto är giltig och att den inte redan finns |
| [AZ nycklar lagringskontolistan](https://docs.microsoft.com/cli/azure/storage/account/keys#az_storage_account_keys_list) | Visar en lista över nycklar för storage-konton |
| [AZ lagringsblob finns](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_exists) | Kontrollerar om blob finns |
| [Skapa AZ lagringsbehållare](https://docs.microsoft.com/cli/azure/storage/container#az_storage_container_create) | Skapar en behållare i ett lagringskonto. |
| [ladda upp AZ storage-blob](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_upload) | Skapar en blob-behållare genom att överföra den virtuella Hårddisken. |
| [AZ vm lista](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Används med `--query` kontrollera om VM-namn används. | 
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Skapar de virtuella datorerna. |
| [AZ vm åtkomst set linux-användare](https://docs.microsoft.com/cli/azure/vm/access#az_vm_access_set_linux_user) | Återställer SSH-nyckel om du vill ge den aktuella användaråtkomsten till den virtuella datorn. |
| [AZ vm lista-ip-adresser](https://docs.microsoft.com/cli/azure/vm#az_vm_list-ip-addresses) | Hämtar IP-adressen för den virtuella datorn som har skapats. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella CLI skriptexempel finns i den [virtuella Azure Linux-datorn dokumentationen](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
