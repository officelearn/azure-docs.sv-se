---
title: Skapa en hanterad disk från en VHD-fil i samma konto – CLI-exempel
description: Exempelskript för Azure CLI – Skapa en hanterad disk från en VHD-fil i ett lagringskonto i samma prenumeration
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: d0795f67390faec4a08882adb1f2b15efcbc6619
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274885"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli-linux"></a>Skapa en hanterad disk från en VHD-fil i ett lagrings konto i samma prenumeration med CLI (Linux)

Det här skriptet skapar en hanterad disk från en VHD-fil i ett lagringskonto i samma prenumeration. Använd det här skriptet för att importera en specialiserad (inte generaliserad/syspreppad) VHD till en hanterad OS-disk för att skapa en virtuell dator. Du kan också använda den för att importera en data-VHD till en hanterad datadisk. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en hanterad disk från en VHD. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az disk create](/cli/azure/disk) | Skapar en hanterad disk från URI:n för en VHD-fil i ett lagringskonto i samma prenumeration |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator genom att koppla en hanterad disk som OS-disk](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer och hanterade diskar finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
