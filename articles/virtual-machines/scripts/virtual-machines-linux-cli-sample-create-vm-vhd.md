---
title: Skriptexempel för Azure CLI – Skapa en virtuell dator med en virtuell hårddisk (VHD) | Microsoft Docs
description: Skriptexempel för Azure CLI – Skapa en virtuell dator med en virtuell hårddisk.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 271390757b7e79eb29c5a3c14ca9ee1b38b53e29
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "41919234"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Skapa en virtuell dator med virtuell hårddisk

Det här exemplet skapar en virtuell dator med en virtuell hårddisk (VHD).
En resursgrupp, ett lagringskonto och en container skapas innan skriptet skapar en virtuell dator genom att ladda upp den virtuella hårddisken till containern.
Skriptet ersätter den offentliga SSH-nyckeln med din offentliga nyckel, vilket ger dig åtkomst till den virtuella datorn.

Du behöver en startbar virtuell hårddisk (VHD). Skriptet söker efter `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_list) | Visar en lista med lagringskonton |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_check_name) | Kontrollerar att namnet på ett lagringskonto är giltigt och att det inte redan finns |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys#az_storage_account_keys_list) | Visar en lista över lagringskontonas nycklar |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_exists) | Kontrollerar om blobben finns |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container#az_storage_container_create) | Skapar en container i ett lagringskonto. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_upload) | Skapar en blobb i containern genom att ladda upp den virtuella hårddisken. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Används med `--query` för att kontrollera om namnet på den virtuella datorn är i bruk. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Skapar de virtuella datorerna. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az_vm_list-ip-addresses) | Hämtar IP-adressen till den virtuella dator som har skapats. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
