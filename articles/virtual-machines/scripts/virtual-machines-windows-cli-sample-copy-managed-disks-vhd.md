---
title: Kopiera en hanterad disk till ett lagrings konto – CLI-exempel
description: Azure CLI-exempel – Exportera eller kopiera en hanterad disk till ett lagringskonto.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 92c752c56c37b854bc793d897c664337cf8bfccd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463620"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Exportera/kopiera en hanterad disk till ett lagringskonto med hjälp av Azure CLI

Det här skriptet exporterar den underliggande virtuella hårddisken från en hanterad disk till ett lagringskonto i samma eller i en annan region. Först genererar skriptet SAS-URI:n för den hanterade disken och använder den sedan för att kopiera den virtuella hårddisken till ett lagringskonto. Använd det här skriptet för att kopiera dina hanterade diskar för regional utökning.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att generera SAS-URI:n för en hanterad disk och kopierar den underliggande virtuella hårddisken till ett lagringskonto med hjälp av SAS-URI:n. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Genererar skrivskyddad SAS som används för att kopiera den underliggande VHD-filen till ett lagringskonto eller för att ladda ned den till en lokal plats  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Kopierar en blob asynkront från ett lagringskonto till ett annat |

## <a name="next-steps"></a>Nästa steg

[Skapa en hanterad disk från en virtuell hårddisk](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler CLI-skript exempel för virtuella datorer och hanterade diskar finns i [Azures dokumentation](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)om virtuella Windows-datorer.
