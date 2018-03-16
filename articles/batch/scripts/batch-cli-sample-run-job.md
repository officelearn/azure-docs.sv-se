---
title: "Exempel på Azure CLI-skript – Kör ett Batch-jobb | Microsoft Docs"
description: "Exempel på Azure CLI-skript – Kör ett jobb med Batch"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: d63854b213b33a3c26358df17761999f5270d7a2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI-exempel: Köra ett jobb och uppgifter med Azure Batch

Det här skriptet skapar ett Batch-jobb och lägger till en rad uppgifter i jobbet. Det visar även hur du övervakar ett jobb och dess uppgifter. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Skapar Batch-kontot. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Skapar en pool med beräkningsnoder.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | Skapar ett Batch-jobb.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | Lägger till en aktivitet i angivet Batch-jobb.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | Uppdaterar egenskaperna för ett Batch-jobb.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | Hämtar information om ett angivet Batch-jobb.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | Hämtar information om en uppgift från det angivna Batch-jobbet.  |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).