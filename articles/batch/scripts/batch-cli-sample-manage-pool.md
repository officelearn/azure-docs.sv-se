---
title: Azure CLI skriptexempel - hantera pooler i Batch | Microsoft Docs
description: Azure CLI skriptexempel - hantera pooler i Batch
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
ms.openlocfilehash: ae7eab97c1da1113b0248b74a9dd67de8ce49e36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-batch-pools-with-azure-cli"></a>Hantera Azure Batch-pooler med Azure CLI

Dessa skript visar några av verktyg som finns i Azure CLI för att skapa och hantera pooler för beräkningsnoder i Azure Batch-tjänsten.

> [!NOTE]
> Kommandona i det här exemplet skapar virtuella Azure-datorer. Virtuella datorer som körs kommer påförs kostnader till ditt konto. Ta bort de virtuella datorerna för att minimera dessa debiteringar, när du är klar kör exemplet. Se [Rensa pooler](#clean-up-pools).

Batch-pooler kan konfigureras på två sätt, antingen med Cloud Services-konfiguration (Windows) eller en virtuell dator-konfiguration (Windows och Linux). Exempel på skript nedan visar hur du skapa pooler med båda konfigurationerna.

## <a name="prerequisites"></a>Krav

- Installera Azure CLI med hjälp av instruktionerna i den [Azure CLI installationsguiden](https://docs.microsoft.com/cli/azure/install-azure-cli), om du inte redan har gjort.
- Skapa ett Batch-konto om du inte redan har ett. Se [skapa ett batchkonto med Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) för ett exempelskript som skapar ett konto.
- Konfigurera ett program att köras från en start-aktivitet om du inte gjort det ännu. Se [att lägga till program till Azure Batch med Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) för ett exempelskript som skapar ett program och överför ett programpaket till Azure.

## <a name="pool-with-cloud-service-configuration-sample-script"></a>Pool med cloud service configuration exempelskript

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Cloud Services Pools")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>Pool med exempelskript konfigurationen för virtuell dator

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Virtual Machine Pools")]

## <a name="clean-up-pools"></a>Rensa pooler

När du har kört ovan exempelskript kör du följande kommando för att ta bort pooler.
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa och ändra Batch pooler.
Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [logga in AZ batch-konto](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Autentisera mot en Batch-kontot.  |
| [Sammanfattning av AZ batch-program](https://docs.microsoft.com/cli/azure/batch/application/summary#az_batch_application_summary_list) | Lista över tillgängliga program i Batch-kontot.  |
| [Skapa AZ batch-pool](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Skapa en pool för virtuella datorer.  |
| [AZ batch-pool uppsättningen](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | Uppdatera egenskaperna för en pool.  |
| [listan för AZ batch pool nod-agent-SKU: er](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | Lista över tillgängliga noden agent SKU: er och bild information.  |
| [Ändra storlek AZ batch-pool](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | Ändra storlek på hur många virtuella datorer som körs i den angivna poolen.  |
| [Visa för AZ batch-pool](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | Visa egenskaperna för en pool.  |
| [ta bort AZ batch-pool](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_delete) | Ta bort den angivna poolen.  |
| [Aktivera AZ batch-pool Autoskala](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Aktivera automatisk skalning på poolen och tillämpa en formel.  |
| [inaktivera AZ batch-pool Autoskala](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Inaktivera automatisk skalning på poolen.  |
| [AZ batch nodlistan](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | Lista över alla compute-nod i den angivna poolen.  |
| [omstart av AZ batch nod](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | Starta om den angivna compute-nod.  |
| [ta bort AZ batch-nod](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | Ta bort de angivna noderna från den angivna poolen.  |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Batch CLI skriptexempel finns i den [Azure Batch CLI dokumentationen](../batch-cli-samples.md).

