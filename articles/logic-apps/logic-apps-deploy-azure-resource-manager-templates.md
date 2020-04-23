---
title: Distribuera mallar för logikappar
description: Lär dig hur du distribuerar Azure Resource Manager-mallar som skapats för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 432e22879ce0eba89f04a1084e2d4a93a487dd45
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086444"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Distribuera Azure Resource Manager-mallar för Azure Logic Apps

När du har skapat en Azure Resource Manager-mall för logikappen kan du distribuera mallen på följande sätt:

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [REST-API för Azure Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Distribuera via Azure-portalen

Om du vill distribuera en logikappmall automatiskt till Azure kan du välja följande knappen **Distribuera till Azure,** som loggar in på Azure-portalen och frågar dig efter information om logikappen. Du kan sedan göra nödvändiga ändringar i logikappmallen eller parametrarna.

[![Distribuera till Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Du uppmanas till exempel att ange följande information när du har loggat in på Azure-portalen:

* Azure-prenumerationsnamn
* Resursgrupp som du vill använda
* Plats för logikapp
* Logikappens namn
* En test-URI
* Godkännande av angivna villkor

Mer information finns i de här ämnena:

* [Översikt: Automatisera distributionen för logikappar med Azure Resource Manager-mallar](logic-apps-azure-resource-manager-templates-overview.md)
* [Distribuera resurser med Azure Resource Manager-mallar och Azure-portalen](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Distribuera med Visual Studio

Om du vill distribuera en logikappmall från ett Azure Resource Group-projekt som du har skapat med Visual Studio följer du dessa [steg för att distribuera logikappen till](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) Azure manuellt.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Distribuera med Azure PowerShell

Om du vill distribuera till en viss *Azure-resursgrupp*använder du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Mer information finns i de här ämnena:

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Om du vill distribuera till en viss *Azure-resursgrupp*använder du följande kommando:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Mer information finns i de här ämnena:

* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Distribuera med Azure DevOps

För att distribuera logikappmallar och hantera miljöer använder team ofta ett verktyg som [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) i [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines tillhandahåller en [Azure Resource Group Deployment-aktivitet](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) som du kan lägga till i alla bygg- eller versionspipelor. För att auktorisering ska kunna distribuera och generera versionspipelinen behöver du också [ett huvudnamn för](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD). Läs mer om [hur du använder tjänsthuvudnamn med Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Mer information om kontinuerlig integrering och kontinuerlig distribution (CI/CD) för Azure Resource Manager-mallar med Azure Pipelines finns i följande avsnitt:

* [Integrera Resource Manager-mallar med Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Självstudiekurs: Kontinuerlig integrering av Azure Resource Manager-mallar med Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Exempel: Ansluta till Azure Service Bus-köer från Azure Logic Apps och distribuera med Azure Pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till Azure Storage-konton från Azure Logic Apps och distribuera med Azure Pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Konfigurera en funktionsappåtgärd för Azure Logic Apps och distribuera med Azure Pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till ett integrationskonto från Azure Logic Apps och distribuera med Azure Pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Exempel: Dirigera Azure-pipelines med hjälp av Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Här är de allmänna stegen på hög nivå för att använda Azure Pipelines:

1. Skapa en tom pipeline i Azure Pipelines.

1. Välj de resurser du behöver för pipelinen, till exempel logikappmallen och mallparametrarna, som du genererar manuellt eller som en del av byggprocessen.

1. Leta reda på och lägga till azure **Resource Group Deployment-aktiviteten** för ditt agentjobb.

   ![Lägg till aktiviteten "Azure Resource Group Deployment"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Konfigurera med en [tjänsthuvudnamn](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Lägg till referenser till logikappmallen och mallparametrarna.

1. Fortsätt att bygga ut steg i utgivningsprocessen för andra miljöer, automatiserade test eller godkännare efter behov.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Auktorisera OAuth-anslutningar

Efter distributionen fungerar logikappen från på slutet med giltiga parametrar. Du måste dock fortfarande auktorisera oauth-anslutningar för att generera giltiga åtkomsttoken för [att autentisera dina autentiseringsuppgifter](../active-directory/develop/authentication-scenarios.md). Här är några sätt som du kan auktorisera OAuth-anslutningar:

* För automatiska distributioner kan du använda ett skript som ger medgivande för varje OAuth-anslutning. Här är ett exempel skript i GitHub i [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projektet.

* Om du vill auktorisera OAuth-anslutningar manuellt öppnar du logikappen i Logic App Designer, antingen i Azure-portalen eller i Visual Studio. I designern, auktorisera alla nödvändiga anslutningar.

Om du använder [ett huvudnamn](../active-directory/develop/app-objects-and-service-principals.md) för Azure Active Directory (Azure AD) i stället för att auktorisera anslutningar läser du om hur [du anger parametrar för tjänstens huvudnamn i logikappmallen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka logikappar](../logic-apps/monitor-logic-apps.md)
