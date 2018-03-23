---
title: Skriptexempel för Azure CLI – Hämta en hanterad resursgrupp och ändra storleken på virtuella datorer | Microsoft Docs
description: Skriptexempel för Azure CLI – Hämta en hanterad resursgrupp och ändra storleken på virtuella datorer
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 9c4032191738703b71319c05bce4f6bbbc61b44b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Hämta resurser från en hanterad resursgrupp och ändra storleken på virtuella datorer med Azure CLI

Detta skript hämtar resurser från en hanterad resursgrupp och ändrar storlek på de virtuella datorerna i resursgruppen.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att distribuera det hanterade programmet. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_list) | Visar en lista med hanterade program. Ange frågevärden om du vill förfina resultatvisningen. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az_resource_list) | Visar resurserna. Ange en resursgrupp och frågevärden om du vill förfina resultatvisningen. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az_vm_resize) | Uppdatera storleken på en virtuell dator. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Azure Managed Application overview](../overview.md) (Översikt över Azure Managed Application).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
