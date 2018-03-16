---
title: "Skapa logikappar från Azure Resource Manager-mallar | Microsoft Docs"
description: "Skapa och distribuera logik app arbetsflöden med Azure Resource Manager-mallar"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9e696f6e4614052456cf2b55123d98d61b8b3b9c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Skapa och distribuera logikappar med Azure Resource Manager-mallar

Med Azure Logikappar innehåller Azure Resource Manager-mallar som du kan använda, inte bara att skapa logikappar för automatisering av arbetsflöden, men också att definiera de resurser och parametrar som används för distribution. Du kan använda mallen för egna affärsscenarier eller anpassa mallen så att den uppfyller dina krav. Lär dig mer om den [Resource Manager-mall för logic apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) och [Azure Resource Manager mallstruktur och syntax](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Definiera logikappen

Det här exemplet logik app definition körs en gång i timmen och pingar den plats som anges i den `testUri` parameter.
Mallen använder parametervärden för programnamn logik (```logicAppName```) och platsen du vill pinga för testning (```testUri```). Lär dig mer om [definiera dessa parametrar i mallen för](#define-parameters). Mallen anger också platsen för logikappen till samma plats som Azure-resursgrupp. 

``` json
{
    "type": "Microsoft.Logic/workflows",
    "apiVersion": "2016-06-01",
    "name": "[parameters('logicAppName')]",
    "location": "[resourceGroup().location]",
    "tags": {
        "displayName": "LogicApp"
    },
    "properties": {
        "definition": {
            "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "testURI": {
                "type": "string",
                "defaultValue": "[parameters('testUri')]"
                }
            },
            "triggers": {
                "Recurrence": {
                    "type": "Recurrence",
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            },
            "actions": {
                "Http": {
                    "type": "Http",
                    "inputs": {
                        "method": "GET",
                        "uri": "@parameters('testUri')"
                    },
                    "runAfter": {}
                }
            },
            "outputs": {}
        },
        "parameters": {}
    }
}
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>Definiera parametrar

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Nedan följer beskrivningar för parametrarna i mallen:

| Parameter | Beskrivning | Exempel för JSON-definition | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Definierar namnet på logikappen som skapas. | ”logicAppName”: {”typ”: ”sträng”, ”metadata”: {”beskrivning”: ”myExampleLogicAppName”}} |
| `testUri` | Anger platsen för att pinga för testning. | ”testUri”: {”typ”: ”sträng”, ”defaultValue” ”:http://azure.microsoft.com/status/feed/”} | 
||||

Lär dig mer om [REST API för Logic Apps arbetsflödesdefinitionen och egenskaper](https://docs.microsoft.com/rest/api/logic/workflows) och [bygger på logiken app definitioner med JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Distribuera automatiskt i logikappar

Om du vill skapa och distribuera en logikapp automatiskt till Azure, Välj **till Azure** här:

[![Distribuera till Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Den här åtgärden loggar du in på Azure-portalen där du kan ange dina logic Apps information och göra ändringar i mallen eller parametrar. Till exempel efterfrågar Azure-portalen dessa uppgifter:

* Namn på Azure-prenumeration
* Resursgrupp som du vill använda
* Logik för platsen
* Ett namn för din logikapp
* Ett URI-test
* Godkännande av de angivna villkoren

## <a name="deploy-logic-apps-with-commands"></a>Distribuera logikappar med kommandon

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Azure CLI

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)