---
title: Kopiera en ögonblicks bild till ett lagrings konto i en annan region med hjälp av CLI
description: Skriptexempel för Azure CLI – Exportera/kopiera ögonblicksbild som VHD till ett lagringskonto i samma prenumeration eller i en annan region.
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
ms.custom: mvc,seodec18
ms.openlocfilehash: e59386acdf4590100e59fe49142715ea26b8d6d6
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321947"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Exportera/kopiera en ögonblicksbild till ett lagringskonto i annan region med CLI

Det här skriptet exporterar en hanterad ögonblicksbild till ett lagringskonto i annan region. Först genererar det SAS-URI för ögonblicksbilden och sedan använder det SAS-URI för att kopiera den till ett lagringskonto i en annan region. Använd det här skriptet för att behålla en säkerhetskopia av dina hanterade diskar i annan region för haveriberedskap. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att generera SAS-URI för en hanterad ögonblicksbild och kopierar ögonblicksbilden till ett lagringskonto med hjälp av SAS-URI. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az snapshot grant-access](/cli/azure/snapshot) | Genererar skrivskyddad SAS som används för att kopiera den underliggande VHD-filen till ett lagringskonto eller för att ladda ned den till en lokal plats  |
| [az storage blob copy start](/cli/azure/storage/blob/copy) | Kopierar en blob asynkront från ett lagringskonto till ett annat |

## <a name="next-steps"></a>Nästa steg

[Skapa en hanterad disk från en virtuell hårddisk](virtual-machines-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Skapa en virtuell dator från en hanterad disk](virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer och hanterade diskar finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
