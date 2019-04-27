---
title: Använda en Azure Resource Manager-mall för att skapa en arbetsyta
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder en Azure Resource Manager-mall för att skapa en ny arbetsyta i Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 7349998325e56d5ebb78de5ca30c0127f09102aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819558"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Du skapar en arbetsyta för Azure Machine Learning-tjänsten med en Azure Resource Manager-mall

I den här artikeln lär du dig flera olika sätt att skapa en arbetsyta för Azure Machine Learning-tjänsten som använder Azure Resource Manager-mallar. En Resource Manager-mallen gör det enkelt att skapa resurser som en enda, samordnad åtgärd. En mall är ett JSON-dokument som definierar de resurser som behövs för en distribution. Det kan även ange distributionsparametrarna. Parametrar används för att ange databasvärden när du använder mallen.

Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En **Azure-prenumeration**. Om du inte har någon kan du prova den [kostnadsfri eller betald version av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree).

* Om du vill använda en mall från en CLI, måste du antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-mall

Följande Resource Manager-mall kan användas för att skapa en arbetsyta för Azure Machine Learning-tjänsten och tillhörande Azure-resurser:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Azure Machine Learning service workspace."
            }
        },
        "location": {
            "type": "string",
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
                "description": "The location where the workspace will be created."
            }
        }
    },
    "variables": {
        "storageAccount": {
            "name": "[concat('sa',uniqueString(resourceGroup().id))]",
            "type": "Standard_LRS"
        },
        "keyVault": {
            "name": "[concat('kv',uniqueString(resourceGroup().id))]",
            "tenantId": "[subscription().tenantId]"
        },
        "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
        "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "name": "[variables('storageAccount').name]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[variables('storageAccount').type]"
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
                "supportHttpsTrafficOnly": true
            }
        },
        {
            "name": "[variables('keyVault').name]",
            "type": "Microsoft.KeyVault/vaults",
            "apiVersion": "2018-02-14",
            "location": "[parameters('location')]",
            "properties": {
                "tenantId": "[variables('keyVault').tenantId]",
                "sku": {
                    "name": "standard",
                    "family": "A"
                },
                "accessPolicies": []
            }
        },
        {
            "name": "[variables('applicationInsightsName')]",
            "type": "Microsoft.Insights/components",
            "apiVersion": "2015-05-01",
            "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
            "kind": "web",
            "properties": {
                "Application_Type": "web"
            }
        },
        {
            "name": "[variables('containerRegistryName')]",
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2017-10-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "adminUserEnabled": true
            }
        },
        {
            "name": "[parameters('workspaceName')]",
            "type": "Microsoft.MachineLearningServices/workspaces",
            "apiVersion": "2018-11-19",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('storageAccount').name]",
                "[variables('keyVault').name]",
                "[variables('applicationInsightsName')]",
                "[variables('containerRegistryName')]"
            ],
            "identity": {
                "type": "systemAssigned"
            },
            "properties": {
                "friendlyName": "[parameters('workspaceName')]",
                "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVault').name)]",
                "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
                "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
                "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccount').name)]"
            }
        }
    ]
}
```

Den här mallen skapar följande Azure-tjänster:

* Azure-resursgrupp
* Azure Storage-konto
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-arbetsyta

Resursgruppen är behållare som innehåller tjänsterna. De olika tjänsterna krävs av Azure Machine Learning-arbetsytan.

Exempel-mallen har två parametrar:

* Den **plats** där resursgruppen och tjänster kommer att skapas.

    Mallen använder den plats som du väljer för de flesta resurser. Undantaget är Application Insights-tjänsten, som inte är tillgänglig i alla platser som de andra tjänsterna. Om du väljer en plats där den inte är tillgänglig kan kommer tjänsten att skapas på platsen USA, södra centrala.

* Den **Arbetsytenamn**, vilket är det egna namnet för Azure Machine Learning-arbetsytan.

    Namnen på de andra tjänsterna genereras slumpmässigt.

Mer information om mallar finns i följande artiklar:

* [Skapa Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Distribuera ett program med Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.MachineLearningServices resurstyper](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Följ stegen i [distribuera resurser från anpassad mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). När du når den __redigera mallen__ skärmen, klistra in mall från det här dokumentet.
1. Välj __spara__ att använda mallen. Ange följande information och samtycker till att de angivna allmänna villkoren:

   * Prenumeration: Välj den Azure-prenumerationen för dessa resurser.
   * Resursgrupp: Välj eller skapa en resursgrupp som innehåller tjänsterna.
   * Arbetsytans namn: Namnet ska användas för Azure Machine Learning-arbetsytan som ska skapas. Arbetsytans namn måste vara mellan 3 och 33 tecken. Det får bara innehålla alfanumeriska tecken och '-'.
   * Plats: Välj den plats där resurserna som ska skapas.

     ![Mallparametrar i Azure portal](media/how-to-create-workspace-template/template-parameters.png)

Mer information finns i [distribuera resurser från anpassad mall](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Det här exemplet förutsätts att du har sparat mallen till en fil med namnet `azuredeploy.json` i den aktuella katalogen:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) och [distribuera privat Resource Manager-mall med SAS-token och Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Använda Azure CLI

Det här exemplet förutsätts att du har sparat mallen till en fil med namnet `azuredeploy.json` i den aktuella katalogen:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) och [distribuera privat Resource Manager-mall med SAS-token och Azure CLI](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>Nästa steg

* [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Skapa och distribuera Azure-resursgrupper via Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
