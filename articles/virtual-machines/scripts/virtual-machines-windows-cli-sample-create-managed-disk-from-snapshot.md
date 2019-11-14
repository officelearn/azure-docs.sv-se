---
title: Skriptexempel för Azure CLI – Skapa en hanterad disk från en ögonblicksbild
description: Skriptexempel för Azure CLI – Skapa en hanterad disk från en ögonblicksbild
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
ms.openlocfilehash: 5060dc81196acec65001910751498fbd8513936e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039968"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Skapa en hanterad disk från en ögonblicksbild med CLI

Det här skriptet skapar en hanterad disk från en ögonblicksbild. Du kan använda den för att återställa en virtuell dator från ögonblicksbilder av operativsystem och datadiskar. Skapa OS och hanterade datadiskar från respektive ögonblicksbilder, och skapa sedan en ny virtuell dator genom att koppla hanterade diskar. Du kan även återställa datadiskar på en befintlig virtuell dator genom att koppla datadiskar som skapats från ögonblicksbilder.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en hanterad disk från en ögonblicksbild. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Hämtar alla egenskaper för en ögonblicksbild med hjälp av ögonblicksbildens namn och resursgruppsegenskaper. ID-egenskapen används för att skapa en hanterad disk.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Skapar en hanterad disk med ett ögonblicksbilds-ID för en hanterad ögonblicksbild |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler CLI-skript exempel för virtuella datorer och hanterade diskar finns i [Azures dokumentation](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)om virtuella Windows-datorer.
