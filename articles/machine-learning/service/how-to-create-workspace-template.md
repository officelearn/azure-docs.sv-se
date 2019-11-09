---
title: Använd en Azure Resource Manager mall för att skapa en arbets yta
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder en Azure Resource Manager mall för att skapa en ny Azure Machine Learning arbets yta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: f20c7955856e2e848d331d857d2564649c837bb0
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847461"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Använd en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning

I den här artikeln får du lära dig flera sätt att skapa en Azure Machine Learning arbets yta med Azure Resource Manager-mallar. En Resource Manager-mall gör det enkelt att skapa resurser som en enda, koordinerad åtgärd. En mall är ett JSON-dokument som definierar de resurser som behövs för en distribution. Den kan också ange distributions parametrar. Parametrar används för att ange indatavärden när du använder mallen.

Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Om du inte har en sådan kan du prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* Om du vill använda en mall från en CLI behöver du antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-mall

Följande Resource Manager-mall kan användas för att skapa en Azure Machine Learning arbets yta och associerade Azure-resurser:

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
          "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
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
      "apiVersion": "2019-11-01",
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
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
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

Resurs gruppen är den behållare som innehåller tjänsterna. De olika tjänsterna krävs av Azure Machine Learning-arbetsytan.

Exempel mal len har två parametrar:

* Den **plats** där resurs gruppen och tjänsterna ska skapas.

    Mallen kommer att använda den plats du väljer för de flesta resurser. Undantaget är Application Insights tjänst, som inte är tillgänglig på alla platser som de andra tjänsterna är. Om du väljer en plats där den inte är tillgänglig kommer tjänsten att skapas på platsen södra centrala USA.

* **Namnet på arbets ytan**, som är det egna namnet på Azure Machine Learning arbets ytan.

    Namnen på de andra tjänsterna genereras slumpmässigt.

> [!TIP]
> Även om mallen som är associerad med det här dokumentet skapar en ny Azure Container Registry, kan du också skapa en ny arbets yta utan att skapa ett behållar register. Om det finns i behållar registret på arbets ytan skapas ett när du utför en åtgärd som kräver ett behållar register. Till exempel utbildning eller distribution av en modell.
>
> Du kan också referera till ett befintligt behållar register eller lagrings konto i Azure Resource Manager mall, i stället för att skapa ett nytt.

Mer information om mallar finns i följande artiklar:

* [Redigera Azure Resource Manager mallar](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Distribuera ett program med Azure Resource Manager mallar](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resurs typer för Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Följ stegen i [distribuera resurser från den anpassade mallen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). När du kommer till skärmen __Redigera mall__ klistrar du in mallen från det här dokumentet.
1. Välj __Spara__ för att använda mallen. Ange följande information och godkänn de allmänna villkor som anges:

   * Prenumeration: Välj den Azure-prenumeration som ska användas för dessa resurser.
   * Resurs grupp: Välj eller skapa en resurs grupp som ska innehålla tjänsterna.
   * Namn på arbets yta: namnet som ska användas för Azure Machine Learning arbets ytan som ska skapas. Arbets ytans namn måste innehålla mellan 3 och 33 tecken. Det får bara innehålla alfanumeriska tecken och "-".
   * Plats: Välj den plats där resurserna ska skapas.

Mer information finns i [distribuera resurser från en anpassad mall](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Använd Azure PowerShell

I det här exemplet förutsätter vi att du har sparat mallen till en fil med namnet `azuredeploy.json` i den aktuella katalogen:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Använda Azure CLI

I det här exemplet förutsätter vi att du har sparat mallen till en fil med namnet `azuredeploy.json` i den aktuella katalogen:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure CLI](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault åtkomst princip och Azure Resource Manager mallar

När du använder en Azure Resource Manager mall för att skapa arbets ytan och associerade resurser (inklusive Azure Key Vault), flera gånger. Du kan till exempel använda mallen flera gånger med samma parametrar som en del av en kontinuerlig integrering och distributions pipeline.

De flesta åtgärder för att skapa resurser via mallar är idempotenta, men Key Vault rensar åtkomst principerna varje gång mallen används. Om du rensar åtkomst principerna bryts åtkomsten till Key Vault för en befintlig arbets yta som använder den. Till exempel kan stoppa/skapa-funktioner i Azure Notebooks virtuella datorn Miss lyckas.  

För att undvika det här problemet rekommenderar vi en av följande metoder:

* Distribuera inte mallen mer än en gång för samma parametrar. Eller ta bort de befintliga resurserna innan du använder mallen för att återskapa dem.
  
* Granska Key Vault åtkomst principer och Använd sedan dessa principer för att ställa in accessPolicies-egenskapen för mallen.
* Kontrol lera om Key Vault resursen redan finns. Om det gör det, ska du inte återskapa det via mallen. Du kan till exempel lägga till en parameter som gör att du kan inaktivera skapande av den Key Vault resursen om den redan finns.

## <a name="next-steps"></a>Nästa steg

* [Distribuera resurser med Resource Manager-mallar och Resource Manager-REST API](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Skapa och Distribuera Azure-resurs grupper via Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
