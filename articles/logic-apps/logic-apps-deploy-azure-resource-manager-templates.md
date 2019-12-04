---
title: Distribuera mallar för logikappar
description: Lär dig hur du distribuerar Azure Resource Manager mallar som skapats för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 73c4c8839bd1d9e37f3dc4f0802ad5a648b1026b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793042"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Distribuera Azure Resource Manager mallar för Azure Logic Apps

När du har skapat en Azure Resource Manager mall för din Logic app kan du distribuera mallen på följande sätt:

* [Azure-portalen](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure-pipeline](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Distribuera via Azure Portal

Om du vill distribuera en Logic app-mall automatiskt till Azure kan du klicka på knappen för att **distribuera till Azure** , som loggar in på Azure Portal och där du uppmanas att ange information om din Logic app. Du kan sedan göra nödvändiga ändringar i Logic app-mallen eller parametrar.

[![Distribuera till Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Du uppmanas till exempel att ange följande information när du har loggat in på Azure Portal:

* Namn på Azure-prenumeration
* Resurs grupp som du vill använda
* Plats för Logic app
* Logikappens namn
* En test-URI
* Godkännande av de angivna villkoren

Mer information finns i följande avsnitt:

* [Översikt: Automatisera distribution av Logic Apps med Azure Resource Manager mallar](logic-apps-azure-resource-manager-templates-overview.md)
* [Distribuera resurser med Azure Resource Manager mallar och Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Distribuera med Visual Studio

Om du vill distribuera en Logic app-mall från ett Azure-resurs grupps projekt som du skapade med Visual Studio, följer du dessa [steg för att manuellt distribuera din Logic app](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) till Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Distribuera med Azure PowerShell

Använd följande kommando för att distribuera till en *Azure-resurs grupp*:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Mer information finns i följande avsnitt:

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Använd följande kommando för att distribuera till en *Azure-resurs grupp*:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Mer information finns i följande avsnitt:

* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Distribuera med Azure DevOps

För att distribuera Logic app-mallar och hantera miljöer använder team ofta ett verktyg som [Azure-pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) i [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure-pipeliner tillhandahåller en [distributions uppgift för Azure-resursposter](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) som du kan lägga till i en pipeline för build eller release. För att du ska kunna distribuera och generera en release-pipeline behöver du också ett [huvud namn](../active-directory/develop/app-objects-and-service-principals.md)för Azure Active Directory (AD). Lär dig mer om att [använda tjänstens huvud namn med Azure-pipeliner](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Mer information om kontinuerlig integrering och kontinuerlig distribution (CI/CD) för Azure Resource Manager mallar med Azure pipelines finns i följande avsnitt och exempel:

* [Integrera Resource Manager-mallar med Azure-pipeline](../azure-resource-manager/vs-resource-groups-project-devops-pipelines.md)
* [Självstudie: kontinuerlig integrering för Azure Resource Manager mallar med Azure-pipeline](../azure-resource-manager/resource-manager-tutorial-use-azure-pipelines.md)
* [Exempel: Anslut till Azure Service Bus köer från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till Azure Storage konton från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Konfigurera en Function-app-åtgärd för Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till ett integrations konto från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

Här följer de allmänna stegen för att använda Azure-pipeliner:

1. Skapa en tom pipeline i Azure-pipeliner.

1. Välj de resurser som du behöver för pipelinen, till exempel din Logic app-mall och mallar för mallfiler, som du skapar manuellt eller som en del av bygg processen.

1. För Agent jobbet letar du reda på och lägger till **distributions uppgiften Azure resurs grupp** .

   ![Lägg till aktiviteten Azure Resource Group Deployment](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Konfigurera med ett [huvud namn för tjänsten](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Lägg till referenser till din Logic app-mall och mallfiler för mallar.

1. Fortsätt att bygga upp stegen i versions processen för alla andra miljöer, automatiserade tester och god kännare efter behov.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Auktorisera OAuth-anslutningar

Efter distributionen fungerar din Logic app från slut punkt till slut punkt med giltiga parametrar. Du måste dock fortfarande auktorisera alla OAuth-anslutningar för att generera giltiga åtkomsttoken för [autentisering av dina autentiseringsuppgifter](../active-directory/develop/authentication-scenarios.md). Här är några sätt som du kan auktorisera OAuth-anslutningar:

* Vid automatiserade distributioner kan du använda ett skript som ger godkännande för varje OAuth-anslutning. Här är ett exempel skript i GitHub i [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) -projektet.

* Om du vill auktorisera OAuth-anslutningar manuellt öppnar du din Logic app i Logic App Designer, antingen i Azure Portal eller i Visual Studio. I designern godkänner du de anslutningar som krävs.

Om du använder ett [huvud namn](../active-directory/develop/app-objects-and-service-principals.md) för tjänsten Azure Active Directory (Azure AD) i stället för att auktorisera anslutningar, lär du dig att [Ange parametrar för tjänstens huvud namn i din Logic app-mall](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)
