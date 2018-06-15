---
title: Azure PowerShell skriptexempel - Export/kopiera ögonblicksbild som VHD till ett lagringskonto i annan region | Microsoft Docs
description: Azure PowerShell skriptexempel - Export/kopiera ögonblicksbild som VHD till ett lagringskonto i samma annan region
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: be21a891121df1d645b430d87b572cde6c945d61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23879647"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Exportera eller kopieras hanterad ögonblicksbilder som VHD ett lagringskonto i en annan region med PowerShell

Det här skriptet exporterar en hanterad ögonblicksbild till ett lagringskonto i en annan region. Den första genererar ögonblicksbilden SAS-URI och sedan används för att kopiera den till ett lagringskonto i en annan region. Använd det här skriptet för att underhålla säkerhetskopiering hanterade diskar i annan region för katastrofåterställning.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att generera SAS URI för en hanterad ögonblicksbild och kopierar ögonblicksbilden till ett lagringskonto med hjälp av SAS-URI. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Bevilja AzureRmSnapshotAccess](/powershell/module/azurerm.compute/New-AzureRmDisk) | Genererar SAS-URI för en ögonblicksbild som används för att kopiera den till ett lagringskonto. |
| [Ny AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Skapar en kontexten för lagringskontot med kontonamnet och nyckeln. Den här kontexten kan användas för att utföra åtgärder för läsning och skrivning på lagringskontot. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Kopierar underliggande VHD från en ögonblicksbild till ett lagringskonto |

## <a name="next-steps"></a>Nästa steg

[Skapa en hanterad disk från en virtuell Hårddisk](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Skapa en virtuell dator från en hanterad disk](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare virtuella PowerShell-skript-exempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).