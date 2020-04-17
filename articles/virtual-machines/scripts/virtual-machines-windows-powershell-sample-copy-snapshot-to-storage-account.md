---
title: Exportera/kopiera ögonblicksbild som virtuell hårddisk till ett lagringskonto i en annan region - PowerShell-exempel
description: Skriptexempel för Azure PowerShell – Exportera/kopiera ögonblicksbild som VHD till ett lagringskonto i samma andra region
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 760b6ae2d791267053acdb1e216ab5daeab7c061
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459363"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Exportera/kopiera hanterade ögonblicksbilder som VHD till ett lagringskonto i en annan region med PowerShell

Det här skriptet exporterar en hanterad ögonblicksbild till ett lagringskonto i annan region. Först genererar det SAS-URI för ögonblicksbilden och sedan använder det SAS-URI för att kopiera den till ett lagringskonto i en annan region. Använd det här skriptet för att behålla en säkerhetskopia av dina hanterade diskar i annan region för haveriberedskap.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att generera SAS-URI för en hanterad ögonblicksbild och kopierar ögonblicksbilden till ett lagringskonto med hjälp av SAS-URI. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Grant-AzSnapshotAccess](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Skapar SAS-URI för den ögonblicksbild som används vid kopiering till ett lagringskonto. |
| [New-AzStorageContext](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontext) | Skapar en lagringskontokontext med kontonamnet och nyckeln. Den här kontexten kan användas för att utföra läs-/skrivåtgärder på lagringskontot. |
| [Start-AzStorageBlobCopy](https://docs.microsoft.com/powershell/module/az.storage/start-azstorageblobcopy) | Kopierar den underliggande virtuella hårddisken för en ögonblicksbild till ett lagringskonto |

## <a name="next-steps"></a>Nästa steg

[Skapa en hanterad disk från en virtuell hårddisk](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Skapa en virtuell dator från en hanterad disk](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
