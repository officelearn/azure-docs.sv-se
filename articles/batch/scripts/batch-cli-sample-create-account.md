---
title: "Azure CLI-skript exempel – skapa ett Batch-konto | Microsoft Docs"
description: "Azure CLI-skript exempel – skapa ett Batch-konto"
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
ms.openlocfilehash: fd2f4682a04c557b69bbfce115f41c54a96d462c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-cli"></a>Skapa ett batchkonto med Azure CLI

Det här skriptet skapar ett Azure Batch-konto och visar hur olika egenskaper för kontot som kan efterfrågas och uppdateras.

## <a name="prerequisites"></a>Krav

Installera Azure CLI med hjälp av instruktionerna i den [Azure CLI installationsguiden](https://docs.microsoft.com/cli/azure/install-azure-cli), om du inte redan har gjort.

## <a name="batch-account-sample-script"></a>Exempelskript för batch-konto

När du skapar en Batch-kontot som standard tilldelas dess datornoderna internt av Batch-tjänsten. Allokerade datornoderna omfattas av en separat kärnkvot och kontot kan autentiseras antingen via autentiseringsuppgifterna för delad nyckel eller ett Azure Active Dirctory-token.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="batch-account-using-user-subscription-sample-script"></a>Batch-kontot med användaren exempelskript för prenumeration

Du kan också välja om du vill ha skapa dess compute-noder i din egen Azure-prenumeration.
Konton som allokerar compute-noder i din prenumeration måste autentiseras via Azure Active Directory-token och datornoder allokerade räknas som en prenumerationens kvoter. Om du vill skapa ett konto i det här läget måste en ange en Key Vault-referens när du skapar kontot.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Create Account using User Subscription")]

## <a name="clean-up-deployment"></a>Rensa distribution

När du kör något av ovanstående exempelskript kör följande kommando för att ta bort resursgruppen och alla relaterade resurserna (inklusive Batch-konton, Azure Storage-konton och Azure nyckelvalv).

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, Batch-kontot och alla relaterade resurser. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ batch-kontot](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_create) | Skapar Batch-kontot.  |
| [AZ batch inställt](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_set) | Uppdaterar egenskaperna för Batch-kontot.  |
| [Visa för AZ batch-konto](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_show) | Hämtar information om det angivna Batch-kontot.  |
| [AZ batch-kontot nycklar lista](https://docs.microsoft.com/cli/azure/batch/account/keys#az_batch_account_keys_list) | Hämtar åtkomstnycklarna för det angivna Batch-kontot.  |
| [logga in AZ batch-konto](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Autentiseras mot det angivna Batch-kontot för ytterligare CLI interaktion.  |
| [Skapa AZ storage-konto](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Skapar ett lagringskonto. |
| [Skapa AZ keyvault](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Skapar ett nyckelvalv. |
| [Ange en AZ keyvault-princip](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | Uppdatera säkerhetsprincipen för angivna nyckelvalvet. |
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Batch CLI skriptexempel finns i den [Azure Batch CLI dokumentationen](../batch-cli-samples.md).
