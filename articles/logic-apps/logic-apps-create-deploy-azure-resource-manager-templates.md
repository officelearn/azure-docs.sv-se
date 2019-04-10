---
title: Distribuera logic apps i Azure Resource Manager-mallar – Azure Logic Apps
description: Distribuera logic apps med hjälp av Azure Resource Manager-mallar
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: bbb10bf0174b6e06e28d171510345ed92b6642d9
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357060"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Distribuera logic apps i Azure Resource Manager-mallar

När du har skapat en Azure Resource Manager-mall för att distribuera logikappen kan du distribuera din mall på följande sätt:

* [Azure Portal](#portal)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Distribuera via Azure-portalen

För att automatiskt distribuera en mall för logikapp till Azure, kan du välja följande **distribuera till Azure** knapp, som loggar du in på Azure-portalen och du uppmanas att ange information om din logikapp. Du kan sedan göra nödvändiga ändringar till mall för logikapp eller parametrar.

[![Deploy till Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Till exempel uppmanas du för den här informationen när du loggar in på Azure Portal:

* Namn på Azure-prenumeration
* Resursgruppen som du vill använda
* Logic app-plats
* Logikappens namn
* Ett test URI
* Godkännande av de angivna allmänna villkoren

Mer information finns i [distribuera resurser med Azure Resource Manager-mallar och Azure-portalen](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Godkänna OAuth-anslutningar

Efter distributionen fungerar logikappen slutpunkt till slutpunkt med giltiga parametrar. Du måste dock fortfarande godkänna OAuth-anslutningar för att skapa en giltig åtkomsttoken. För automatisk distribution kan du använda ett skript som godkänner varje OAuth-anslutning, som detta [exempelskript i GitHub LogicAppConnectionAuth projektet](https://github.com/logicappsio/LogicAppConnectionAuth). Du kan också godkänna OAuth-anslutningar via Azure portal eller i Visual Studio genom att öppna logikappen i Logic Apps Designer.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Distribuera med Azure PowerShell

Distribuera till en specifik *Azure-resursgrupp*, Använd det här kommandot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Använd följande kommando om du vill distribuera till en specifik Azure-prenumeration:

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Distribuera till en specifik *Azure-resursgrupp*, Använd det här kommandot:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Använd följande kommando om du vill distribuera till en specifik Azure-prenumeration:

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Mer information finns i följande avsnitt: 

* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Distribuera med Azure DevOps

Om du vill distribuera mallar för logikappar och hantera miljöer, ofta använda ett verktyg som [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) i [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Pipelines som Azure tillhandahåller en [Azure Resursgruppsdistribution uppgift](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) att du kan lägga till någon version eller frigöra pipeline.
För att godkänna att distribuera och generera versionspipelinen, måste du också en Azure Active Directory (AD) [tjänstens huvudnamn](../active-directory/develop/app-objects-and-service-principals.md). Läs mer om [med tjänstens huvudnamn med Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Här följer allmänna anvisningar för att använda Azure Pipelines:

1. Skapa en tom pipeline i Azure-Pipelines.

1. Välj de resurser du behöver för pipelinen, t.ex din mall för logikapp och mallfilerna för parametrar, som skapas manuellt eller som en del av skapandeprocessen.

1. För din agent-jobbet, hitta och Lägg till den **Azure Resursgruppsdistribution** uppgift.

   ![Lägg till ”Azure Resursgruppsdistribution” aktivitet](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Konfigurera med en [tjänstens huvudnamn](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Lägg till referenser till din mall för logikapp och mallfilerna för parametrar.

1. Fortsätt att bygga ut steg i processen för versionen för alla andra miljö, automatiserad testning och godkännare efter behov.

## <a name="get-support"></a>Få support

Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)
