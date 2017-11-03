---
title: "Azure CLI skript exempel – ha en hanterad resursgrupp och ändra storlek på virtuella datorer | Microsoft Docs"
description: "Azure CLI skriptexempel - ha en hanterad resursgrupp och ändra storlek på virtuella datorer"
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
ms.openlocfilehash: c78d2646471e40d60972cf91cb5bbd351f71a66c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Hämta resurser i en hanterad resursgrupp och ändra storlek på virtuella datorer med Azure CLI

Detta skript hämtar resurser från en hanterad resursgruppen och ändrar storlek på virtuella datorer i resursgruppen.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att distribuera det hanterade programmet. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ managedapp lista](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_list) | Lista över hanterade program. Ange frågevärden om du vill begränsa resultaten. |
| [lista över resurser AZ](https://docs.microsoft.com/cli/azure/resource#az_resource_list) | Lista över resurser. Ange en resurs grupp och fråga värden om du vill begränsa resultatet. |
| [AZ vm-storlek](https://docs.microsoft.com/cli/azure/vm#az_vm_resize) | Uppdatera storleken på en virtuell dator. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program, se [översikt över Azure Managed Application](../overview.md).
* Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).
