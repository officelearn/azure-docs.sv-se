---
title: Skapa arbets ytor med Azure CLI
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure CLI för att skapa en ny Azure Machine Learning-arbetsyta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 06/19/2020
ms.openlocfilehash: f22ef4d1ebd9c4d3c226556c4ef28a873edd80ea
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119281"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Skapa en arbets yta för Azure Machine Learning med Azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar en Azure Machine Learning-arbetsyta med hjälp av Azure CLI. Azure CLI innehåller kommandon för att hantera Azure-resurser. Machine Learning-tillägget till CLI innehåller kommandon för att arbeta med Azure Machine Learning resurser.

## <a name="prerequisites"></a>Krav

* En **Azure-prenumeration**. Om du inte har en sådan kan du prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö**behöver du [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)kan CLI nås via webbläsaren och finns i molnet.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ansluta CLI till din Azure-prenumeration

> [!IMPORTANT]
> Om du använder Azure Cloud Shell kan du hoppa över det här avsnittet. Cloud Shell autentiserar automatiskt dig med det konto som du loggar in i din Azure-prenumeration.

Det finns flera sätt som du kan autentisera till din Azure-prenumeration från CLI. Den mest grundläggande är att interaktivt autentisera med hjälp av en webbläsare. För att autentisera interaktivt, öppna en kommando rad eller Terminal och Använd följande kommando:

```azurecli-interactive
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa anvisningarna på kommando raden. Anvisningarna omfattar att bläddra till [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och ange en auktoriseringskod.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

För andra metoder för autentisering, se [Logga in med Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Installera Machine Learning-tillägget

Om du vill installera Machine Learning-tillägget använder du följande kommando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Azure Machine Learning-arbetsytan är beroende av följande Azure-tjänster eller-entiteter:

> [!IMPORTANT]
> Om du inte anger en befintlig Azure-tjänst skapas en automatiskt när arbets ytan skapas. Du måste alltid ange en resurs grupp. När du kopplar ditt eget lagrings konto ser du till att både Azure blob-och Azure-filfunktioner är aktiverade och att hierarkiskt namn område (ADLS gen 2) är inaktiverat. Du kan alltid ansluta ditt eget lagrings konto senare när arbets ytan har skapats som data lager.

| Tjänst | Parameter för att ange en befintlig instans |
| ---- | ---- |
| **Azure-resurs grupp** | `-g <resource-group-name>`
| **Azure Storage-konto** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Arbets ytan Azure Machine Learning måste skapas i en resurs grupp. Du kan använda en befintlig resurs grupp eller skapa en ny. Använd följande kommando för att __skapa en ny resurs grupp__. Ersätt `<resource-group-name>` med det namn som ska användas för den här resurs gruppen. Ersätt `<location>` med den Azure-region som ska användas för den här resurs gruppen:

> [!TIP]
> Välj en region där Azure Machine Learning är tillgängligt. Mer information finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

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

Mer information om hur du arbetar med resurs grupper finns i [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Skapa nödvändiga resurser automatiskt

Om du vill skapa en ny arbets yta där __tjänsterna skapas automatiskt__, använder du följande kommando:

> [!TIP]
> Kommandona i det här avsnittet skapar en Basic Edition-arbetsyta. Om du vill skapa en företags arbets yta använder du `--sku enterprise` växeln med `az ml workspace create` kommandot. Mer information om Azure Machine Learning-versioner finns i [Azure Machine Learning](overview-what-is-azure-ml.md#sku).

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Namnet på arbets ytan är Skift läges okänsligt.

Utdata från det här kommandot liknar följande JSON:

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

### <a name="use-existing-resources"></a>Använd befintliga resurser

Om du vill skapa en arbets yta som använder befintliga resurser måste du ange ID: t för resurserna. Använd följande kommandon för att hämta ID: t för tjänsterna:

> [!IMPORTANT]
> Du behöver inte ange alla befintliga resurser. Du kan ange en eller flera. Du kan till exempel ange ett befintligt lagrings konto så skapas de andra resurserna av arbets ytan.

+ **Azure Storage konto**:`az storage account show --name <storage-account-name> --query "id"`

    Svaret från det här kommandot liknar följande text och är ID: t för ditt lagrings konto:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure Application insikter**:

    1. Installera Application Insights-tillägget:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Hämta ID för din Application Insight Service:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Svaret från det här kommandot liknar följande text och är ID: t för Application Insights-tjänsten:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**:`az keyvault show --name <key-vault-name> --query "ID"`

    Svaret från det här kommandot liknar följande text och är ID: t för nyckel valvet:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Svaret från det här kommandot liknar följande text och är ID: t för behållar registret:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Kontot för behållar registret måste ha [Administratörs kontot](/azure/container-registry/container-registry-authentication#admin-account) aktiverat innan det kan användas med en Azure Machine Learning-arbetsyta.

När du har ID: n för de resurser som du vill använda med arbets ytan använder du `az workspace create -w <workspace-name> -g <resource-group-name>` kommandot Base och lägger till parametrarna och ID: na för de befintliga resurserna. Följande kommando skapar till exempel en arbets yta som använder ett befintligt behållar register:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Utdata från det här kommandot liknar följande JSON:

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

## <a name="list-workspaces"></a>Lista arbets ytor

Om du vill visa en lista över alla arbets ytor för din Azure-prenumeration använder du följande kommando:

```azurecli-interactive
az ml workspace list
```

Utdata från det här kommandot liknar följande JSON:

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

Mer information finns i dokumentationen om [AZ ml-arbetsytans lista](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) .

## <a name="get-workspace-information"></a>Hämta information om arbets ytan

Om du vill hämta information om en arbets yta använder du följande kommando:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Utdata från det här kommandot liknar följande JSON:

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

Mer information finns i dokumentationen om [AZ ml-arbetsytan](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) .

## <a name="update-a-workspace"></a>Uppdatera en arbets yta

Om du vill uppdatera en arbets yta använder du följande kommando:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Utdata från det här kommandot liknar följande JSON:

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

Mer information finns i dokumentationen om [uppdatering av AZ ml-arbetsytan](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Dela en arbets yta med en annan användare

Om du vill dela en arbets yta med en annan användare i din prenumeration använder du följande kommando:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Mer information om rollbaserad åtkomst kontroll (RBAC) med Azure Machine Learning finns i [Hantera användare och roller](how-to-assign-roles.md).

Mer information finns i [AZ ml-arbetsytan resurs](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) dokumentation.

## <a name="sync-keys-for-dependent-resources"></a>Synkronisera nycklar för beroende resurser

Om du ändrar åtkomst nycklar för en av resurserna som används av din arbets yta tar det ungefär en timme innan arbets ytan synkroniseras med den nya nyckeln. Om du vill tvinga arbets ytan att synkronisera de nya nycklarna direkt använder du följande kommando:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Mer information om hur du ändrar nycklar finns i [Återskapa lagrings åtkomst nycklar](how-to-change-storage-access-key.md).

Mer information finns i dokumentationen för [Sync-Keys för AZ ml-arbetsytan](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

Använd följande kommando om du vill ta bort en arbets yta efter att den inte längre behövs:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Om du tar bort en arbets yta tas inte program insikter, lagrings konto, nyckel valv eller behållar registret som används av arbets ytan bort.

Du kan också ta bort resurs gruppen, som tar bort arbets ytan och alla andra Azure-resurser i resurs gruppen. Om du vill ta bort resurs gruppen använder du följande kommando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Mer information finns i [AZ ml-arbetsytan ta bort](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) dokumentation.

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resurs leverantörs fel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbets ytan

> [!WARNING]
> Det finns inte stöd för att flytta Azure Machine Learning arbets ytan till en annan prenumeration eller flytta den ägande prenumerationen till en ny klient. Detta kan orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Tar bort Azure Container Registry

I arbets ytan Azure Machine Learning används Azure Container Registry (ACR) för vissa åtgärder. En ACR-instans skapas automatiskt när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI-tillägget för Machine Learning finns i [AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) -dokumentationen.
