---
title: Exempel på Azure CLI-skript – Skapa Batch-konto – Batch-tjänsten | Microsoft Docs
description: Exempel på Azure CLI-skript – Skapa Batch-konto i Batch-tjänstläge
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
ms.openlocfilehash: 86a04393761b79d76339c111d5c5c8b76dc983c2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431216"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI-exempel: Skapa ett Batch-konto i Batch-tjänstläge

Det här skriptet skapar ett Azure Batch-konto i Batch-tjänstläget och visar hur du ställer frågor eller uppdaterar olika egenskaper för kontot. När du skapar ett Batch-konto i standardläget för Batch-tjänsten tilldelas dess beräkningsnoder internt av Batch-tjänsten. Tilldelade beräkningsnoder regleras av en separat vCPU-kvort (kärna) och kontot kan autentiseras antingen via autentiseringsuppgifter för delad nyckel eller en Azure Active Directory-token.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Skapar Batch-kontot. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Skapar ett lagringskonto. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Uppdaterar egenskaperna för Batch-kontot.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Hämtar information om det angivna Batch-kontot.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Hämtar åtkomstnycklar för det angivna Batch-kontot.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
