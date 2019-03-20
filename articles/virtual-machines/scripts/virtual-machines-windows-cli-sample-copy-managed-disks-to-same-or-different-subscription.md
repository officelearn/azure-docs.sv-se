---
title: Skriptexempel för Azure CLI – Kopiera (flytta) hanterade diskar till samma eller en annan prenumeration | Microsoft Docs
description: Skriptexempel för Azure CLI – Kopiera (flytta) hanterade diskar till samma eller en annan prenumeration
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
ms.openlocfilehash: abb051e9646d547907384ed06413439845a29d5e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249679"
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
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Hämtar alla egenskaper för en hanterad disk med hjälp av den hanterade diskens namn och resursgruppsegenskaper. ID-egenskapen används för att kopiera den hanterade disken till en annan prenumeration.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Kopierar en hanterad disk genom att skapa en ny hanterad disk i en annan prenumeration med hjälp av den överordnade diskens ID och namn.  |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare virtuella datorer och hanterade diskar CLI-skriptexempel finns i den [dokumentationen för Azure Windows virtuella datorer](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
