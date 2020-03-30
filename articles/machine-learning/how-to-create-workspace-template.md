---
title: Skapa en arbetsyta med Azure Resource Manager-mall
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder en Azure Resource Manager-mall för att skapa en ny Azure Machine Learning-arbetsyta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: 9403cc05ed5b31f3b76c16c4232506e2ddc5da2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402919"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Använda en Azure Resource Manager-mall för att skapa en arbetsyta för Azure Machine Learning

I den här artikeln lär du dig flera sätt att skapa en Azure Machine Learning-arbetsyta med Azure Resource Manager-mallar. En Resource Manager-mall gör det enkelt att skapa resurser som en enda samordnad åtgärd. En mall är ett JSON-dokument som definierar de resurser som behövs för en distribution. Det kan också ange distributionsparametrar. Parametrar används för att ange indatavärden när du använder mallen.

Mer information finns i [Distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Krav

* En **Azure-prenumeration**. Om du inte har någon provar du den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* Om du vill använda en mall från en CLI behöver du antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-mall

Följande Resource Manager-mall kan användas för att skapa en Azure Machine Learning-arbetsyta och associerade Azure-resurser:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Den här mallen skapar följande Azure-tjänster:

* Azure-resursgrupp
* Azure Storage-konto
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-arbetsyta

Resursgruppen är den behållare som innehåller tjänsterna. De olika tjänsterna krävs av arbetsytan Azure Machine Learning.

Exempelmallen har två parametrar:

* Den **plats** där resursgruppen och resurstjänsterna ska skapas.

    Mallen använder den plats du väljer för de flesta resurser. Undantaget är application insights-tjänsten, som inte är tillgänglig på alla platser som de andra tjänsterna är. Om du väljer en plats där den inte är tillgänglig skapas tjänsten på platsen för södra centrala USA.

* **Arbetsytans namn**, som är det egna namnet på arbetsytan Azure Machine Learning.

    > [!NOTE]
    > Arbetsytans namn är skiftlägesokänsligt.

    Namnen på de andra tjänsterna genereras slumpmässigt.

> [!TIP]
> Medan mallen som är associerad med det här dokumentet skapar ett nytt Azure-behållarregister, kan du också skapa en ny arbetsyta utan att skapa ett behållarregister. En skapas när du utför en åtgärd som kräver ett behållarregister. Till exempel utbildning eller distribution av en modell.
>
> Du kan också referera till ett befintligt behållarregister eller lagringskonto i Azure Resource Manager-mallen i stället för att skapa ett nytt.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Mer information om mallar finns i följande artiklar:

* [Författare Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md)
* [Distribuera ett program med Azure Resource Manager-mallar](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices resurstyper](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Avancerad mall

Följande exempelmall visar hur du skapar en arbetsyta med tre inställningar:

* Aktivera inställningar för hög sekretess för arbetsytan
* Aktivera kryptering för arbetsytan
* Använder ett befintligt Azure Key Vault

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred to as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with the customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault Resource Manager ID."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault key uri."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
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
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
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
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    }
  ]
}
```

Om du vill ha ID:t för Key Vault och den viktiga URI som behövs i den här mallen kan du använda Azure CLI. Följande kommando är ett exempel på hur du använder Azure CLI för att hämta resurs-ID:t och URI för Key Vault:

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "[id, properties.vaultUri]"
```

Det här kommandot returnerar ett värde som liknar följande text. Det första värdet är ID och det andra är URI:

```text
[
  "/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault",
  "https://mykeyvault.vault.azure.net/"
]
```

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Följ stegen i [Distribuera resurser från anpassad mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). När du kommer till __skärmen Redigera mall__ klistrar du in mallen från det här dokumentet.
1. Välj __Spara__ om du vill använda mallen. Ange följande information och godkänn villkoren i listan:

   * Prenumeration: Välj den Azure-prenumeration som ska användas för dessa resurser.
   * Resursgrupp: Välj eller skapa en resursgrupp som innehåller tjänsterna.
   * Arbetsytans namn: Namnet som ska användas för arbetsytan Azure Machine Learning som ska skapas. Arbetsytans namn måste vara mellan 3 och 33 tecken. Den får endast innehålla alfanumeriska tecken och '-'.
   * Plats: Välj den plats där resurserna ska skapas.

Mer information finns i [Distribuera resurser från anpassad mall](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Det här exemplet förutsätter att du har `azuredeploy.json` sparat mallen i en fil som heter i den aktuella katalogen:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) och distribuera privat Resource [Manager-mall med SAS-token och Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Det här exemplet förutsätter att du har `azuredeploy.json` sparat mallen i en fil som heter i den aktuella katalogen:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md) och Distribuera privat Resource [Manager-mall med SAS-token och Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resursproviderfel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault-åtkomstprincip och Azure Resource Manager-mallar

När du använder en Azure Resource Manager-mall för att skapa arbetsytan och tillhörande resurser (inklusive Azure Key Vault), flera gånger. Till exempel använda mallen flera gånger med samma parametrar som en del av en kontinuerlig integration och distribution pipeline.

De flesta resursskapande åtgärder via mallar är idempotenta, men Key Vault rensar åtkomstprinciperna varje gång mallen används. Om du rensar åtkomstprinciperna bryts åtkomsten till Nyckelvalvet för alla befintliga arbetsytor som använder den. Till exempel kan stop/create-funktioner för Azure Notebooks VM misslyckas.  

För att undvika det här problemet rekommenderar vi något av följande tillvägagångssätt:

* Distribuera inte mallen mer än en gång för samma parametrar. Eller ta bort befintliga resurser innan du använder mallen för att återskapa dem.

* Undersök åtkomstprinciperna för Nyckelvalv och använd `accessPolicies` sedan dessa principer för att ange mallens egenskap. Om du vill visa åtkomstprinciperna använder du följande Azure CLI-kommando:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Mer information om `accessPolicies` hur du använder avsnittet i mallen finns i [objektreferensen AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Kontrollera om key vault-resursen redan finns. Om den gör det ska du inte återskapa den via mallen. Om du till exempel vill använda det befintliga nyckelvalvet i stället för att skapa ett nytt gör du följande ändringar i mallen:

    * **Lägg till** en parameter som accepterar ID:et för en befintlig Key Vault-resurs:

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

    * **Ta** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` bort raden `dependsOn` från avsnittet på arbetsytan. Ändra **Change** även `keyVault` posten `properties` i avsnittet på arbetsytan `keyVaultId` för att referera till parametern:

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

    Efter dessa ändringar kan du ange ID för den befintliga Key Vault-resursen när du kör mallen. Mallen återanvänds sedan nyckelvalvet `keyVault` genom att ange arbetsytans egenskap på dess ID.

    Om du vill hämta ID:t för Key Vault kan du referera till utdata för den ursprungliga mallkörningen eller använda Azure CLI. Följande kommando är ett exempel på hur du använder Azure CLI för att hämta resurs-ID:t Key Vault-

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Det här kommandot returnerar ett värde som liknar följande text:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Nästa steg

* [Distribuera resurser med Resource Manager-mallar och RESURSHANTERARENS REST API](../azure-resource-manager/templates/deploy-rest.md).
* [Skapa och distribuera Azure-resursgrupper via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
