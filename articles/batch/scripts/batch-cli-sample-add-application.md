---
title: Exempel på Azure CLI-skript – Lägga till ett program i Batch | Microsoft Docs
description: Exempel på Azure CLI-skript – Lägga till ett program i Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 80b9cb749be942f72459180182e7d74a45f6a943
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843110"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI-exempel: Lägga till ett program i ett Azure Batch-konto

Det här skriptet visar hur du lägger till ett program som ska användas med en pool eller uppgift i Azure Batch. Konfigurera ett program som ska läggas till i Batch-kontot genom att paketera den körbara filen tillsammans med eventuella beroenden i en zip-fil. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon.
Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Skapar ett lagringskonto. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Skapar Batch-kontot. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az batch application create](/cli/azure/batch/application#az_batch_application_create) | Skapar ett program.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch_application_package_create) | Lägger till ett programpaket i det angivna programmet.  |
| [az batch application set](/cli/azure/batch/application#az_batch_application_set) | Uppdaterar egenskaperna för ett program.  |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
