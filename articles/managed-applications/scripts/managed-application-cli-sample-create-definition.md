---
title: "Azure CLI-skript sample - skapa en definition för hanterade | Microsoft Docs"
description: "Azure CLI-skript sample - skapa en definition för hanterade program"
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
ms.openlocfilehash: 6cfc51e5787ad6ab100638d0965b69232cda070a
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Skapa en definition för hanterade program med Azure CLI

Det här skriptet publicerar en definition för hanterade program till en tjänstkatalog. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder du följande kommando för att skapa definition för hanterade program. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ managedapp definition](https://docs.microsoft.com/cli/azure/managedapp/definition#az_managedapp_definition_create) | Skapa en definition för hanterade program. Ange det paket som innehåller filerna som krävs. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program, se [översikt över Azure Managed Application](../overview.md).
* Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).
