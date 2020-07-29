---
title: Skript exempel för Azure CLI – distribuera ett hanterat program
description: Tillhandahåller skript för Azure CLI-exempel som distribuerar en Azure-hanterad applikations definition till prenumerationen.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 0c2fc3dc4e76f295dff94ebda71d54bad425b621
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056148"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Distribuera ett hanterat program för tjänstkatalogen med Azure CLI

Det här skriptet distribuerar en definition för hanterade program från tjänstkatalogen. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommando för att distribuera det hanterade programmet. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az managedapp create](/cli/azure/managedapp#az-managedapp-create) | Skapa ett hanterat program. Ange definitions-ID och parametrar för mallen. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Azure Managed Application overview](../overview.md) (Översikt över Azure Managed Application).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
