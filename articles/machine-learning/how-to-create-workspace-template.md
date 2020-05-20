---
title: Skapa en arbets yta med Azure Resource Manager mall
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder en Azure Resource Manager mall för att skapa en ny Azure Machine Learning arbets yta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: eae10b7ae8cd14fd120e969c39c05a8ba2525003
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681542"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Använd en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

I den här artikeln får du lära dig flera sätt att skapa en Azure Machine Learning arbets yta med Azure Resource Manager-mallar. En Resource Manager-mall gör det enkelt att skapa resurser som en enda, koordinerad åtgärd. En mall är ett JSON-dokument som definierar de resurser som behövs för en distribution. Den kan också ange distributions parametrar. Parametrar används för att ange indatavärden när du använder mallen.

Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Krav

* En **Azure-prenumeration**. Om du inte har en sådan kan du prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* Om du vill använda en mall från en CLI behöver du antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-mall

Följande Resource Manager-mall kan användas för att skapa en Azure Machine Learning arbets yta och associerade Azure-resurser:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Den här mallen skapar följande Azure-tjänster:

* Azure-resurs grupp
* Azure Storage-konto
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-arbetsyta

Resurs gruppen är den behållare som innehåller tjänsterna. De olika tjänsterna krävs av Azure Machine Learning-arbetsytan.

Exempel mal len har två parametrar:

* Den **plats** där resurs gruppen och tjänsterna ska skapas.

    Mallen kommer att använda den plats du väljer för de flesta resurser. Undantaget är Application Insights tjänst, som inte är tillgänglig på alla platser som de andra tjänsterna är. Om du väljer en plats där den inte är tillgänglig kommer tjänsten att skapas på platsen södra centrala USA.

* **Namnet på arbets ytan**, som är det egna namnet på Azure Machine Learning arbets ytan.

    > [!NOTE]
    > Namnet på arbets ytan är Skift läges okänsligt.

    Namnen på de andra tjänsterna genereras slumpmässigt.

> [!TIP]
> Även om mallen som är associerad med det här dokumentet skapar en ny Azure Container Registry, kan du också skapa en ny arbets yta utan att skapa ett behållar register. En kommer att skapas när du utför en åtgärd som kräver ett behållar register. Till exempel utbildning eller distribution av en modell.
>
> Du kan också referera till ett befintligt behållar register eller lagrings konto i Azure Resource Manager mall, i stället för att skapa ett nytt.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Mer information om mallar finns i följande artiklar:

* [Redigera Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md)
* [Distribuera ett program med Azure Resource Manager mallar](../azure-resource-manager/templates/deploy-powershell.md)
* [Resurs typer för Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Avancerad mall

Följande exempel-mall visar hur du skapar en arbets yta med tre inställningar:

* Aktivera inställningar för hög konfidentialitet för arbets ytan
* Aktivera kryptering för arbets ytan
* Använder en befintlig Azure Key Vault för att hämta Kundhanterade nycklar

Mer information finns i avsnittet om [kryptering i vilo](concept-enterprise-security.md#encryption-at-rest)läge.

> [!IMPORTANT]
> Det finns vissa särskilda krav som din prenumeration måste uppfylla innan du använder den här mallen:
> * __Azure Machine Learning__ programmet måste vara __deltagare__ i din Azure-prenumeration.
> * Du måste ha en befintlig Azure Key Vault som innehåller en krypterings nyckel.
> * Du måste ha en åtkomst princip i Azure Key Vault som beviljar __Get__-, __wrap__-och __unwrap__ -åtkomst till __Azure Cosmos DB__ programmet.
> * Azure Key Vault måste vara i samma region där du planerar att skapa Azure Machine Learning-arbetsytan.

Använd följande kommandon __för att lägga till Azure Machine Learning-appen som deltagare__:

1. Använd följande kommando för att autentisera till Azure från CLI:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Använd följande kommando för att hämta objekt-ID: t för Azure Machine Learning-appen. Värdet kan vara ett annat för var och en av dina Azure-prenumerationer:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Det här kommandot returnerar objekt-ID: t, som är ett GUID.

1. Använd följande kommando om du vill lägga till objekt-ID som deltagare i din prenumeration. Ersätt `<object-ID>` med GUID från föregående steg. Ersätt `<subscription-ID>` med namnet eller ID: t för din Azure-prenumeration:

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

Om du __vill lägga till en nyckel till din Azure Key Vault__använder du informationen i avsnittet [lägga till en nyckel, ett hemligt eller certifikat i nyckel valvet](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) i artikeln __Hantera Key Vault med Azure CLI__ .

__Om du vill lägga till en åtkomst princip i nyckel valvet använder du följande kommandon__:

1. Använd följande kommando för att hämta objekt-ID: t för Azure Cosmos DB-appen. Värdet kan vara ett annat för var och en av dina Azure-prenumerationer:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Det här kommandot returnerar objekt-ID: t, som är ett GUID.

1. Använd följande kommando för att ange principen. Ersätt `<keyvault-name>` med namnet på den befintliga Azure Key Vault. Ersätt `<object-ID>` med GUID från föregående steg:

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

Använd följande steg __för att hämta värdena__ för `cmk_keyvault` (ID för Key Vault) och `resource_cmk_uri` PARAMETRARNA (nyckel-URI) som behövs för den här mallen:

1. Använd följande kommando för att hämta Key Vault-ID:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Det här kommandot returnerar ett värde som liknar `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault` .

1. Använd följande kommando för att hämta värdet för URI: n för den hanterade nyckeln för kunden:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Det här kommandot returnerar ett värde som liknar `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` .

__Exempel mal len__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> När en arbets yta har skapats kan du inte ändra inställningarna för konfidentiella data, kryptering, nyckel valv-ID eller nyckel identifierare. Om du vill ändra dessa värden måste du skapa en ny arbets yta med de nya värdena.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Följ stegen i [distribuera resurser från den anpassade mallen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). När du kommer till skärmen __Redigera mall__ klistrar du in mallen från det här dokumentet.
1. Välj __Spara__ för att använda mallen. Ange följande information och godkänn de allmänna villkor som anges:

   * Prenumeration: Välj den Azure-prenumeration som ska användas för dessa resurser.
   * Resurs grupp: Välj eller skapa en resurs grupp som ska innehålla tjänsterna.
   * Namn på arbets yta: namnet som ska användas för Azure Machine Learning arbets ytan som ska skapas. Arbets ytans namn måste innehålla mellan 3 och 33 tecken. Det får bara innehålla alfanumeriska tecken och "-".
   * Plats: Välj den plats där resurserna ska skapas.

Mer information finns i [distribuera resurser från en anpassad mall](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

I det här exemplet förutsätter vi att du har sparat mallen till en fil med namnet `azuredeploy.json` i den aktuella katalogen:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Använda Azure CLI

I det här exemplet förutsätter vi att du har sparat mallen till en fil med namnet `azuredeploy.json` i den aktuella katalogen:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resurs leverantörs fel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault åtkomst princip och Azure Resource Manager mallar

När du använder en Azure Resource Manager mall för att skapa arbets ytan och associerade resurser (inklusive Azure Key Vault), flera gånger. Du kan till exempel använda mallen flera gånger med samma parametrar som en del av en kontinuerlig integrering och distributions pipeline.

De flesta åtgärder för att skapa resurser via mallar är idempotenta, men Key Vault rensar åtkomst principerna varje gång mallen används. Om du rensar åtkomst principerna bryts åtkomsten till Key Vault för en befintlig arbets yta som använder den. Till exempel kan stoppa/skapa-funktioner i Azure Notebooks virtuella datorn Miss lyckas.  

För att undvika det här problemet rekommenderar vi en av följande metoder:

* Distribuera inte mallen mer än en gång för samma parametrar. Eller ta bort de befintliga resurserna innan du använder mallen för att återskapa dem.

* Granska Key Vault åtkomst principer och Använd sedan dessa principer för att ange `accessPolicies` mallens egenskap. Om du vill visa åtkomst principerna använder du följande Azure CLI-kommando:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Mer information om hur du använder `accessPolicies` avsnittet i mallen finns i referens för [AccessPolicyEntry-objekt](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Kontrol lera om Key Vault resursen redan finns. Om det gör det, ska du inte återskapa det via mallen. Om du till exempel vill använda den befintliga Key Vault i stället för att skapa en ny, gör du följande ändringar i mallen:

    * **Lägg till** en parameter som godkänner ID: t för en befintlig Key Vault-resurs:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Ta bort** avsnittet som skapar en Key Vault-resurs:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Ta bort** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` raden från `dependsOn` avsnittet i arbets ytan. **Ändra** också `keyVault` posten i `properties` avsnittet i arbets ytan så att den refererar till `keyVaultId` parametern:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Efter dessa ändringar kan du ange ID: t för den befintliga Key Vault-resursen när du kör mallen. Mallen återanvänds sedan Key Vault genom att ställa in `keyVault` arbets ytans egenskap till dess ID.

    Om du vill hämta ID: t för Key Vault kan du referera till utdata från den ursprungliga mallen, köra eller använda Azure CLI. Följande kommando är ett exempel på hur du använder Azure CLI för att hämta Key Vault resurs-ID:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Det här kommandot returnerar ett värde som liknar följande text:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Nästa steg

* [Distribuera resurser med Resource Manager-mallar och Resource Manager-REST API](../azure-resource-manager/templates/deploy-rest.md).
* [Skapa och Distribuera Azure-resurs grupper via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
