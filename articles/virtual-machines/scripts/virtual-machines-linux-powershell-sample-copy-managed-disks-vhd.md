---
title: Exportera/kopiera den virtuella hård disken för en hanterad disk till ett lagrings konto i olika region – PowerShell-exempel
description: Skriptexempel för Azure PowerShell – Exportera/kopiera VHD för en hanterad disk till ett lagringskonto i samma region eller en annan region
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: ad143bd94e1a8a556ca8f08b8377ecffdbd8b017
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459747"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell"></a>Exportera/kopiera VHD för en hanterad disk till ett lagringskonto i en annan region med PowerShell

Det här skriptet exporterar den virtuella hårddisken från en hanterad disk till ett lagringskonto i en annan region. Först genererar det SAS-URI:n för den hanterade disken och använder den sedan för att kopiera den underliggande virtuella hårddisken till ett lagringskonto i en annan region. Använd det här skriptet för att kopiera hanterade diskar till en annan region för regional utökning.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att generera SAS-URI:er för en hanterad disk och kopierar den underliggande virtuella hårddisken till ett lagringskonto med hjälp av SAS-URI:n. Varje kommando i tabellen länkar till den kommandospecifika dokumentationen.

| Kommando | Obs! |
|---|---|
| [Grant-AzDiskAccess](https://docs.microsoft.com/powershell/module/az.compute/grant-azdiskaccess) | Skapar SAS-URI för en hanterad disk som används för att kopiera den underliggande virtuella hårddisken till ett lagringskonto. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Skapar en lagringskontokontext med kontonamnet och nyckeln. Den här kontexten kan användas för att utföra läs-/skrivåtgärder på lagringskontot. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Kopierar den underliggande virtuella hårddisken för en ögonblicksbild till ett lagringskonto |

## <a name="next-steps"></a>Nästa steg

[Skapa en hanterad disk från en virtuell hårddisk](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Skapa en virtuell dator från en hanterad disk](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).