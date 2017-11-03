---
title: "Azure CLI-skriptexempel - köra ett jobb med Batch | Microsoft Docs"
description: "Azure CLI-skriptexempel - köra ett jobb med Batch"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: 73d93622d418359be421e043d0af4e4befc6f4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Jobb som körs på Azure Batch med Azure CLI

Det här skriptet skapar ett batchjobb och lägger till en rad uppgifter i jobbet. Här visas också hur du övervakar ett jobb och dess uppgifter. Slutligen visas hur man frågar Batch-tjänsten för information om jobbets uppgifter effektivt.

## <a name="prerequisites"></a>Krav

- Installera Azure CLI med hjälp av instruktionerna i den [Azure CLI installationsguiden](https://docs.microsoft.com/cli/azure/install-azure-cli), om du inte redan har gjort.
- Skapa ett Batch-konto om du inte redan har ett. Se [skapa ett batchkonto med Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) för ett exempelskript som skapar ett konto.
- Konfigurera ett program att köras från en start-aktivitet om du inte gjort det ännu. Se [att lägga till program till Azure Batch med Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) för ett exempelskript som skapar ett program och överför ett programpaket till Azure.
- Konfigurera en pool som jobbet ska köras. Se [hantera Azure Batch-pooler med Azure CLI](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool) för ett exempelskript som skapar en pool med en tjänstkonfiguration moln eller en konfiguration av virtuell dator.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-job"></a>Rensa jobb

När du har kört ovan exempelskript kör du följande kommando för att ta bort jobbet och alla aktiviteter. Observera att poolen måste tas bort separat. Se [hantera Azure Batch-pooler med Azure CLI](./batch-cli-sample-manage-pool.md) för mer information om hur du skapar och tar bort pooler.

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa ett batchjobb och uppgifter. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [logga in AZ batch-konto](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Autentisera mot en Batch-kontot.  |
| [Skapa AZ batchjobb](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | Skapar ett batchjobb.  |
| [AZ batch-jobbet uppsättningen](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | Uppdaterar egenskaperna för ett batchjobb.  |
| [AZ batch-jobbet visar](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | Hämtar information om batchjobb angivna.  |
| [Skapa AZ batchaktiviteten](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | Lägger till en aktivitet till angivna Batch-jobbet.  |
| [AZ batch uppgiften visa](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | Hämtar information om en aktivitet från det angivna Batch-jobbet.  |
| [uppgiftslista för AZ batch](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_list) | Listar de uppgifter som är associerade med det angivna jobbet.  |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Batch CLI skriptexempel finns i den [Azure Batch CLI dokumentationen](../batch-cli-samples.md).
