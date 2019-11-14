---
title: Exempelskript för Azure CLI – Skapa en hanterad disk från en VHD-fil i ett lagringskonto i samma prenumeration
description: Exempelskript för Azure CLI – Skapa en hanterad disk från en VHD-fil i ett lagringskonto i samma prenumeration
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
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 12f8a8edc995a5b3e07244eb5c54835fa6dd832d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039946"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Skapa en hanterad disk från en VHD-fil i ett lagringskonto i samma prenumeration med CLI

Det här skriptet skapar en hanterad disk från en VHD-fil i ett lagringskonto i samma prenumeration. Använd det här skriptet för att importera en specialiserad (inte generaliserad/syspreppad) VHD till en hanterad OS-disk för att skapa en virtuell dator. Du kan också använda den för att importera en data-VHD till en hanterad datadisk.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en hanterad disk från en VHD. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Skapar en hanterad disk från URI:n för en VHD-fil i ett lagringskonto i samma prenumeration |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler CLI-skript exempel för virtuella datorer och hanterade diskar finns i [Azures dokumentation](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)om virtuella Windows-datorer.
