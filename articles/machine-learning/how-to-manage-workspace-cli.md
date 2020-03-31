---
title: Skapa arbetsytor med Azure CLI
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure CLI för att skapa en ny Azure Machine Learning-arbetsyta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 64c2e77ffc43ec98c13ce8c0cad002cdb2053241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296902"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Skapa en arbetsyta för Azure Machine Learning med Azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar en Azure Machine Learning-arbetsyta med Hjälp av Azure CLI. Azure CLI innehåller kommandon för hantering av Azure-resurser. Maskininlärningstillägget till CLI innehåller kommandon för att arbeta med Azure Machine Learning-resurser.

## <a name="prerequisites"></a>Krav

* En **Azure-prenumeration**. Om du inte har någon provar du den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö**behöver du [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)nås CLI via webbläsaren och finns i molnet.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Anslut CLI till din Azure-prenumeration

> [!IMPORTANT]
> Om du använder Azure Cloud Shell kan du hoppa över det här avsnittet. Molnskalet autentiserar automatiskt dig med det konto du loggar in på din Azure-prenumeration.

Det finns flera sätt som du kan autentisera till din Azure-prenumeration från CLI. Det mest grundläggande är att interaktivt autentisera med hjälp av en webbläsare. Om du vill autentisera interaktivt öppnar du en kommandorad eller terminal och använder följande kommando:

```azurecli-interactive
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa instruktionerna på kommandoraden. Instruktionerna innebär att du [https://aka.ms/devicelogin](https://aka.ms/devicelogin) surfar på och anger en auktoriseringskod.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Andra metoder för autentisering finns [i Logga in med Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Installera maskininlärningstillägget

Så här installerar du maskininlärningstillägget:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Arbetsytan Azure Machine Learning är beroende av följande Azure-tjänster eller enheter:

> [!IMPORTANT]
> Om du inte anger en befintlig Azure-tjänst skapas en automatiskt när arbetsytan skapas. Du måste alltid ange en resursgrupp.

| Tjänst | Parameter för att ange en befintlig instans |
| ---- | ---- |
| **Azure-resursgrupp** | `-g <resource-group-name>`
| **Azure-lagringskonto** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure-behållarregister** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Arbetsytan Azure Machine Learning måste skapas i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en ny. Om du vill __skapa en ny resursgrupp__använder du följande kommando. Ersätt `<resource-group-name>` med namnet som ska användas för den här resursgruppen. Ersätt `<location>` med Azure-regionen som ska användas för den här resursgruppen:

> [!TIP]
> Du bör välja en region där Azure Machine Learning är tillgängligt. Mer information finns i [Produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Svaret från det här kommandot liknar följande JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Mer information om hur du arbetar med resursgrupper finns i [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Skapa automatiskt nödvändiga resurser

Om du vill skapa en ny arbetsyta där __tjänsterna skapas automatiskt__använder du följande kommando:

> [!TIP]
> Kommandona i det här avsnittet skapar en grundläggande arbetsyta för utgåvor. Om du vill skapa en `--sku enterprise` företagsarbetsyta använder du växeln `az ml workspace create` med kommandot. Mer information om Azure Machine Learning-versioner finns i [Vad är Azure Machine Learning](overview-what-is-azure-ml.md#sku).

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Arbetsytans namn är skiftlägesokänsligt.

Utdata för det här kommandot liknar följande JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>Använda befintliga resurser

Om du vill skapa en arbetsyta som använder befintliga resurser måste du ange ID för resurserna. Använd följande kommandon för att hämta ID:et för tjänsterna:

> [!IMPORTANT]
> Du behöver inte ange alla befintliga resurser. Du kan ange en eller flera. Du kan till exempel ange ett befintligt lagringskonto och arbetsytan skapar de andra resurserna.

+ **Azure-lagringskonto:**`az storage account show --name <storage-account-name> --query "id"`

    Svaret från det här kommandot liknar följande text och är ID:et för ditt lagringskonto:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Insikter för Azure-program:**

    1. Installera tillägget programinsikter:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Få ID för din programinsiktstjänst:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Svaret från det här kommandot liknar följande text och är ID för tjänsten programinsikter:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault:**`az keyvault show --name <key-vault-name> --query "ID"`

    Svaret från det här kommandot liknar följande text och är ID för nyckelvalvet:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure-behållarregister:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Svaret från det här kommandot liknar följande text och är ID för behållarregistret:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Behållarregistret måste ha [administratörskontot](/azure/container-registry/container-registry-authentication#admin-account) aktiverat innan det kan användas med en Azure Machine Learning-arbetsyta.

När du har ID:erna för de resurser som du vill använda `az workspace create -w <workspace-name> -g <resource-group-name>` med arbetsytan använder du baskommandot och lägger till parametern och ID:erna för de befintliga resurserna. Följande kommando skapar till exempel en arbetsyta som använder ett befintligt behållarregister:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Utdata för det här kommandot liknar följande JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Lista arbetsytor

Om du vill visa en lista över alla arbetsytor för din Azure-prenumeration använder du följande kommando:

```azurecli-interactive
az ml workspace list
```

Utdata för det här kommandot liknar följande JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Mer information finns i dokumentationen till [az ml-arbetsytan.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list)

## <a name="get-workspace-information"></a>Hämta information om arbetsytan

Om du vill ha information om en arbetsyta använder du följande kommando:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Utdata för det här kommandot liknar följande JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Mer information finns i dokumentationen för [az ml-arbetsytan.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show)

## <a name="update-a-workspace"></a>Uppdatera en arbetsyta

Så här uppdaterar du en arbetsyta:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Utdata för det här kommandot liknar följande JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Mer information finns i dokumentationen för uppdatering av [az ml-arbetsytan.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)

## <a name="share-a-workspace-with-another-user"></a>Dela en arbetsyta med en annan användare

Om du vill dela en arbetsyta med en annan användare i prenumerationen använder du följande kommando:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Mer information om rollbaserad åtkomstkontroll (RBAC) med Azure Machine Learning finns i [Hantera användare och roller](how-to-assign-roles.md).

Mer information finns i dokumentationen för [az ml-arbetsytans resursdelning.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)

## <a name="sync-keys-for-dependent-resources"></a>Synkronisera nycklar för beroende resurser

Om du ändrar åtkomstnycklar för en av de resurser som används av arbetsytan använder du följande kommando för att synkronisera de nya nycklarna med arbetsytan:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Mer information om hur du ändrar nycklar finns i [Återskapa lagringsåtkomstnycklar](how-to-change-storage-access-key.md).

Mer information finns i dokumentationen för [az ml-synkroniseringsnycklar för arbetsytan.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

Om du vill ta bort en arbetsyta när den inte längre behövs använder du följande kommando:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Om du tar bort en arbetsyta tas inte programinsikt, lagringskonto, nyckelvalv eller behållarregister som används av arbetsytan bort.

Du kan också ta bort resursgruppen, som tar bort arbetsytan och alla andra Azure-resurser i resursgruppen. Om du vill ta bort resursgruppen använder du följande kommando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Mer information finns i dokumentationen för [ta bort az ml-arbetsytan.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete)

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resursproviderfel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbetsytan

> [!WARNING]
> Det går inte att flytta din Azure Machine Learning-arbetsyta till en annan prenumeration, eller flytta den ägande prenumerationen till en ny klientorganisation. Om du gör det kan det orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Ta bort Azure-behållarregistret

Arbetsytan Azure Machine Learning använder Azure Container Registry (ACR) för vissa åtgärder. Det kommer automatiskt att skapa en ACR-instans när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI-tillägget för maskininlärning finns i [az ml-dokumentationen.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)
