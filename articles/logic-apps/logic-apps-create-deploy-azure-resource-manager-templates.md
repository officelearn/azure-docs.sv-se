---
title: Skapa logikappar med Azure Resource Manager-mallar – Azure Logic Apps | Microsoft Docs
description: Skapa och distribuera arbetsflöden i logikappar med Azure Resource Manager-mallar i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 5a1cae376ab9db2b0c4b5e0e5514bf7745593433
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894588"
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Skapa och distribuera logikappar med Azure Resource Manager-mallar

Azure Logic Apps ger Azure Resource Manager-mallar som du kan använda, inte bara att skapa logikappar för automatisering av arbetsflöden, men också att definiera resurser och parametrar som används för distribution. Du kan använda den här mallen för dina egna affärsscenarier eller anpassa mallen så att den uppfyller dina krav. Läs mer om den [Resource Manager-mall för logic apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) och [Azure Resource Manager-mall strukturen och syntaxen](../azure-resource-manager/resource-group-authoring-templates.md). JSON-syntax och egenskaper finns i [Microsoft.Logic resurstyper](/azure/templates/microsoft.logic/allversions).

## <a name="define-the-logic-app"></a>Definiera logikappen

Det här exemplet logikappsdefinitionen körs en gång i timmen och pingar den plats som anges i den `testUri` parametern.
Mallen använder parametervärden för namn för logikappen (```logicAppName```) och på plats för att pinga för testning (```testUri```). Läs mer om [definierar dessa parametrar i mallen](#define-parameters). Mallen ställs även platsen för logikappen till samma plats som Azure-resursgruppen. 

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
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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

Här följer beskrivningar av parametrar i mallen:

| Parameter | Beskrivning | Exempel för JSON-definition | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Definierar namnet logikappens mallen som skapas. | "logicAppName": { "type": "string", "metadata": { "description": "myExampleLogicAppName" } } |
| `testUri` | Definierar platsen för att pinga för testning. | "testUri": { "type": "string", "defaultValue": "https://azure.microsoft.com/status/feed/"} | 
||||

Läs mer om [REST API för Logic Apps-arbetsflödet definitions- och egenskaper](https://docs.microsoft.com/rest/api/logic/workflows) och [att skapa logikappsdefinitioner med JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Distribuera automatiskt i logikappar

Om du vill skapa och distribuera automatiskt en logic app till Azure, Välj **distribuera till Azure** här:

[![Distribuera till Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Den här åtgärden loggar du in på Azure-portalen där du kan ange dina logic Apps information och göra ändringar i mallen eller parametrar. Till exempel uppmanas Azure-portalen du för dessa uppgifter:

* Namn på Azure-prenumeration
* Resursgruppen som du vill använda
* Logic app-plats
* Ett namn för din logikapp
* Ett test URI
* Godkännande av de angivna allmänna villkoren

## <a name="deploy-logic-apps-with-commands"></a>Distribuera logic apps med kommandon

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Azure CLI

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)
