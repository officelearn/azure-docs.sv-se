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
ms.date: 04/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: abe497ed96515e8194fb2ddefd8e7f4cb9908758
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205138"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Du skapar en arbetsyta för Azure Machine Learning-tjänsten med en Azure Resource Manager-mall

I den här artikeln lär du dig flera olika sätt att skapa en arbetsyta för Azure Machine Learning-tjänsten som använder Azure Resource Manager-mallar. En Resource Manager-mallen gör det enkelt att skapa resurser som en enda, samordnad åtgärd. En mall är ett JSON-dokument som definierar de resurser som behövs för en distribution. Det kan även ange distributionsparametrarna. Parametrar används för att ange databasvärden när du använder mallen.

Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En **Azure-prenumeration**. Om du inte har någon kan du prova den [kostnadsfri eller betald version av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree).

* Om du vill använda en mall från en CLI, måste du antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-mall

Följande Resource Manager-mall kan användas för att skapa en arbetsyta för Azure Machine Learning-tjänsten och tillhörande Azure-resurser:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

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
