---
title: "Skriptexempel Azure CLI - kopia (flytta) ögonblicksbild av en hanterad disk till samma eller en annan prenumeration med CLI | Microsoft Docs"
description: "Skriptexempel Azure CLI - kopia (flytta) ögonblicksbild av en hanterad disk till samma eller en annan prenumeration med CLI"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 7c301a314ee946bb9199650bb7f674b8dce7c141
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Kopiera ögonblicksbild av en hanterad disk till samma eller en annan prenumeration med CLI

Det här skriptet kopierar en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration. Använd det här skriptet för att flytta en ögonblicksbild till annan prenumeration i samma region som den överordnade ögonblicksbilden.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en ögonblicksbild i målprenumerationen använder ögonblicksbilden käll-Id. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ ögonblicksbild visa](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Hämtar alla egenskaper för en ögonblicksbild med hjälp av namn och egenskaper för resursgrupp av ögonblicksbilden. ID-egenskapen används för att kopiera ögonblicksbilden till en annan prenumeration.  |
| [Skapa AZ ögonblicksbild](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_create) | Kopierar en ögonblicksbild genom att skapa en ögonblicksbild av en annan prenumeration med Id och namnet på den överordnade ögonblicksbilden.  |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en ögonblicksbild](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella datorer och hanterade diskar CLI skriptexempel finns i den [virtuella Azure Linux-datorn dokumentationen](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
