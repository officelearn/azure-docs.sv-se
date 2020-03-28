---
title: Skapa definition av hanterade program - Azure CLI
description: Innehåller ett Azure CLI-skriptexempel som skapar en hanterad programdefinition i prenumerationen.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: f4d5a0036ba44f7e0054db7ce820b91b0de629b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650193"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Skapa en definition för ett hanterat program med Azure CLI

Det här skriptet publicerar en definition för hanterade program i en tjänstkatalog. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommando för att skapa definitionen för det hanterade programmet. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az managedapp definition create](https://docs.microsoft.com/cli/azure/managedapp/definition#az-managedapp-definition-create) | Skapa en definition för det hanterade programmet. Ange det paket som innehåller filerna som krävs. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Azure Managed Application overview](../overview.md) (Översikt över Azure Managed Application).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
