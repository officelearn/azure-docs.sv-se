---
title: Kopiera hanterade diskar till ett exempel på en prenumerations-CLI
description: Skriptexempel för Azure CLI – Kopiera (flytta) hanterade diskar till samma eller en annan prenumeration
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
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
ms.openlocfilehash: 4b1516e492b13426bed6cae8637abd5c713bcd63
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010146"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Kopiera hanterade diskar till samma eller en annan prenumeration med CLI

Det här skriptet kopierar en hanterad disk till samma eller en annan prenumeration men inom samma region. Kopian fungerar bara när prenumerationerna ingår i samma AAD-klientorganisation.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ny hanterad disk i målprenumerationen med den hanterade källdiskens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az disk show](/cli/azure/disk) | Hämtar alla egenskaper för en hanterad disk med hjälp av den hanterade diskens namn och resursgruppsegenskaper. ID-egenskapen används för att kopiera den hanterade disken till en annan prenumeration.  |
| [az disk create](/cli/azure/disk) | Kopierar en hanterad disk genom att skapa en ny hanterad disk i en annan prenumeration med hjälp av den överordnade diskens ID och namn.  |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler CLI-skript exempel för virtuella datorer och hanterade diskar finns i [Azures dokumentation](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)om virtuella Windows-datorer.
