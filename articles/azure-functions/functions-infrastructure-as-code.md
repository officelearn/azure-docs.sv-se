---
title: Automatisera resursdistribution för en funktionsapp i Azure Functions | Microsoft Docs
description: Lär dig hur du skapar en Azure Resource Manager-mall som distribuerar din funktionsapp.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktioner, serverlös arkitektur, infrastruktur som kod, azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 488b3797c7e18855a60b84a77a05e4e0a5654475
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023676"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatisera resursdistribution för din funktionsapp i Azure Functions

Du kan använda en Azure Resource Manager-mall för att distribuera en funktionsapp. Den här artikeln beskriver de resurser som krävs och parametrar för att göra det. Du kan behöva distribuera ytterligare resurser, beroende på den [utlösare och bindningar](functions-triggers-bindings.md) i din funktionsapp.

Mer information om hur du skapar mallar finns i [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

Exempelmallar finns här:
- [Funktionsappen i förbrukningsplan]
- [Funktionsappen i Azure App Service-plan]

## <a name="required-resources"></a>Resurser som krävs

En funktionsapp kräver dessa resurser:

* En [Azure Storage](../storage/index.yml) konto
* En värdplan (förbrukningsplan eller App Service-plan)
* En funktionsapp 

JSON-syntax och egenskaper för dessa resurser finns i:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)

### <a name="storage-account"></a>Lagringskonto

Ett Azure storage-konto krävs för en funktionsapp. Du behöver ett konto för generell användning som har stöd för blobbar, tabeller, köer och filer. Mer information finns i [krav för Azure Functions lagringskonto](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

Dessutom egenskapen `AzureWebJobsStorage` måste anges som en appinställning i konfigurationen. Om funktionsappen inte använder Application Insights för övervakning, det bör också ange `AzureWebJobsDashboard` som en appinställning.

Azure Functions-runtime använder den `AzureWebJobsStorage` anslutningssträngen för att skapa interna köer.  När Application Insights inte är aktiverad, körningen använder den `AzureWebJobsDashboard` anslutningssträngen för att logga i Azure Table storage och power den **övervakaren** i portalen.

Dessa egenskaper anges i den `appSettings` samling i den `siteConfig` objekt:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
```    

### <a name="hosting-plan"></a>Värdplan

Definitionen av värdplanen varierar beroende på om du använder en förbrukning eller App Service-plan. Se [distribuera en funktionsapp på förbrukningsplanen](#consumption) och [distribuera en funktionsapp i App Service-planen](#app-service-plan).

### <a name="function-app"></a>Funktionsapp

Funktionen app-resursen definieras med hjälp av en resurs av typen **Microsoft.Web/Site** och typ **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Distribuera en funktionsapp på förbrukningsplanen

Du kan köra en funktionsapp i två olika lägen: förbrukningsplanen och App Service-planen. Med förbrukningsplanen beräkningskraften automatiskt när koden körs, skalas ut efter behov för att hantera belastningen och sedan skalas när koden inte körs. Så du behöver betala för virtuella datorer och du behöver inte reserverad kapacitet i förväg. Mer information om värdplaner finns [Azure Functions-förbrukning och App Service-planer](functions-scale.md).

En exempelmall av Azure Resource Manager finns i [funktionsappen i förbrukningsplan].

### <a name="create-a-consumption-plan"></a>Skapa en förbrukningsplan

En förbrukningsplan är en särskild typ av resurs ”serverklustret”. Du anger den med hjälp av den `Dynamic` värde för den `computeMode` och `sku` egenskaper:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

### <a name="create-a-function-app"></a>Skapa en funktionsapp

Dessutom kan en förbrukningsplan kräver två ytterligare inställningar i plats-konfiguration: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` och `WEBSITE_CONTENTSHARE`. De här egenskaperna konfigurera storage-konto och sökvägen där funktionskod och konfiguration lagras.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Distribuera en funktionsapp i App Service-plan

Din funktionsapp körs på dedikerade virtuella datorer på Basic, Standard och Premium-SKU: er, liknande till web apps i App Service-plan. Mer information om hur App Service-planen fungerar finns i den [Azure App Service-planer djupgående översikt över](../app-service/overview-hosting-plans.md). 

En exempelmall av Azure Resource Manager finns i [funktionsappen i Azure App Service-plan].

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Skapa en funktionsapp 

När du har valt ett alternativ för skalning kan du skapa en funktionsapp. Appen är behållare som innehåller alla funktioner.

En funktionsapp har många underordnade resurser som du kan använda i din distribution, inklusive inställningar och alternativ. Kanske du vill ta bort den **sourcecontrols** underordnade resursen och använda en annan [distributionsalternativet](functions-continuous-deployment.md) i stället.

> [!IMPORTANT]
> För att distribuera ditt program med hjälp av Azure Resource Manager, är det viktigt att förstå hur resurser som distribueras i Azure. I följande exempel översta konfigurationer tillämpas med hjälp av **siteConfig**. Det är viktigt att ställa in de här konfigurationerna på översta nivån, eftersom de förmedlar information till Functions-motorn för körning och distribution. Översta information krävs innan underordnat **sourcecontrols/web** resurs används. Även om det är möjligt att konfigurera inställningarna i den underordnade nivån **config/appSettings** resurs i vissa fall måste du distribuera funktionsappen *innan* **config/appSettings**  tillämpas. Till exempel när du använder functions med [Logikappar](../logic-apps/index.yml), dina funktioner är beroende av en annan resurs.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Den här mallen använder den [projekt](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) appen inställningar värde, som anger baskatalogen som distributionsmotorn funktioner (Kudu) söker efter distribuerbar kod. I vår lagringsplats är våra funktioner i en undermapp i den **src** mapp. Därför i föregående exempel vi anger värdet för app-inställningar till `src`. Om dina funktioner finns i roten av din lagringsplats, eller om du inte distribuerar från källkontroll, kan du ta bort det här värdet för app-inställningar.

## <a name="deploy-your-template"></a>Distribuera mallen

Du kan använda någon av följande sätt att distribuera din mall:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST-API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Distribuera till Azure-knappen

Ersätt ```<url-encoded-path-to-azuredeploy-json>``` med en [URL-kodade](https://www.bing.com/search?q=url+encode) version av rådata sökvägen till din `azuredeploy.json` -filen i GitHub.

Här är ett exempel som använder markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Här är ett exempel som använder HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du utvecklar och konfigurera Azure Functions.

* [Azure Functions, info för utvecklare](functions-reference.md)
* [Så här konfigurerar du inställningar för Azure-funktion](functions-how-to-use-azure-function-app-settings.md)
* [Skapa din första Azure-funktion](functions-create-first-azure-function.md)

<!-- LINKS -->

[Funktionsappen i förbrukningsplan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Funktionsappen i Azure App Service-plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
