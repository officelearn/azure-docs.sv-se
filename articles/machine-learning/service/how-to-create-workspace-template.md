---
title: Använd en Azure Resource Manager mall för att skapa en arbets yta
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder en Azure Resource Manager-mall för att skapa en ny Azure Machine Learnings arbets yta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 0e78d9cfce59615a53534fe9815205e39f64853d
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868839"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Använd en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning-tjänsten

I den här artikeln får du lära dig flera sätt att skapa en Azure Machine Learning service-arbetsyta med Azure Resource Manager-mallar. En Resource Manager-mall gör det enkelt att skapa resurser som en enda, koordinerad åtgärd. En mall är ett JSON-dokument som definierar de resurser som behövs för en distribution. Den kan också ange distributions parametrar. Parametrar används för att ange indatavärden när du använder mallen.

Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Om du inte har en sådan kan du prova den [kostnads fria eller betalda versionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree).

* Om du vill använda en mall från en CLI behöver du antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-mall

Följande Resource Manager-mall kan användas för att skapa en Azure Machine Learning service-arbetsyta och tillhör ande Azure-resurser:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

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

Mer information om mallar finns i följande artiklar:

* [Redigera Azure Resource Manager mallar](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Distribuera ett program med Azure Resource Manager mallar](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resurs typer för Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Följ stegen i [distribuera resurser från den anpassade mallen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). När du kommer till skärmen __Redigera mall__ klistrar du in mallen från det här dokumentet.
1. Välj __Spara__ för att använda mallen. Ange följande information och godkänn de allmänna villkor som anges:

   * Prenumeration: Välj den Azure-prenumeration som ska användas för dessa resurser.
   * Resursgrupp: Välj eller skapa en resurs grupp som innehåller tjänsterna.
   * Namn på arbets yta: Namnet som ska användas för Azure Machine Learning arbets ytan som ska skapas. Arbets ytans namn måste innehålla mellan 3 och 33 tecken. Det får bara innehålla alfanumeriska tecken och "-".
   * Plats: Välj den plats där resurserna ska skapas.

     ![Mallparametrar i Azure Portal](media/how-to-create-workspace-template/template-parameters.png)

Mer information finns i [distribuera resurser från en anpassad mall](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

I det här exemplet förutsätter vi att du har sparat mallen till `azuredeploy.json` en fil med namnet i den aktuella katalogen:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Använda Azure CLI

I det här exemplet förutsätter vi att du har sparat mallen till `azuredeploy.json` en fil med namnet i den aktuella katalogen:

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

*  Distribuera inte mallen mer än en gång för samma parametrar. Eller ta bort de befintliga resurserna innan du använder mallen för att återskapa dem.
  
* Granska Key Vault åtkomst principer och Använd sedan dessa principer för att ställa in accessPolicies-egenskapen för mallen.
* Kontrol lera om Key Vault resursen redan finns. Om det gör det, ska du inte återskapa det via mallen. Du kan till exempel lägga till en parameter som gör att du kan inaktivera skapande av den Key Vault resursen om den redan finns.

## <a name="next-steps"></a>Nästa steg

* [Distribuera resurser med Resource Manager-mallar och Resource Manager-REST API](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Skapa och Distribuera Azure-resurs grupper via Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
