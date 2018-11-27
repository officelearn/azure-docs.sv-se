---
title: Skriptexempel för Azure CLI – Kopiera (flytta) hanterade diskar till samma eller en annan prenumeration | Microsoft Docs
description: Skriptexempel för Azure CLI – Kopiera (flytta) hanterade diskar till samma eller en annan prenumeration
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: a514359edcf21d5882b2361d10c06214d8e39502
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274878"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Kopiera hanterade diskar till samma eller en annan prenumeration med CLI

Det här skriptet kopierar en hanterad disk till samma eller en annan prenumeration men inom samma region. Kopian fungerar bara när prenumerationerna ingår i samma AAD-klientorganisation.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ny hanterad disk i målprenumerationen med den hanterade källdiskens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#az_disk_show) | Hämtar alla egenskaper för en hanterad disk med hjälp av den hanterade diskens namn och resursgruppsegenskaper. ID-egenskapen används för att kopiera den hanterade disken till en annan prenumeration.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Kopierar en hanterad disk genom att skapa en ny hanterad disk i en annan prenumeration med hjälp av den överordnade diskens ID och namn.  |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en hanterad disk](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer och hanterade diskar finns i [Dokumentation för virtuella Azure Linux-datorer](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
