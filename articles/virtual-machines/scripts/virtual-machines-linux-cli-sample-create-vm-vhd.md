---
title: Skript exempel för Azure CLI – skapa en virtuell dator med en virtuell hård disk
description: Skriptexempel för Azure CLI – Skapa en virtuell dator med en virtuell hårddisk.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6f5e30b0d6a072f9a40ae57ebe325461cdfe5bb1
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479650"
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

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account list](/cli/azure/storage/account) | Visar en lista med lagringskonton |
| [az storage account check-name](/cli/azure/storage/account) | Kontrollerar att namnet på ett lagringskonto är giltigt och att det inte redan finns |
| [az storage account keys list](/cli/azure/storage/account/keys) | Visar en lista över lagringskontonas nycklar |
| [az storage blob exists](/cli/azure/storage/blob) | Kontrollerar om blobben finns |
| [az storage container create](/cli/azure/storage/container) | Skapar en container i ett lagringskonto. |
| [az storage blob upload](/cli/azure/storage/blob) | Skapar en blobb i containern genom att ladda upp den virtuella hårddisken. |
| [az vm list](/cli/azure/vm) | Används med `--query` för att kontrollera om namnet på den virtuella datorn är i bruk. | 
| [az vm create](/cli/azure/vm/availability-set) | Skapar de virtuella datorerna. |
| [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) | Hämtar IP-adressen till den virtuella dator som har skapats. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
