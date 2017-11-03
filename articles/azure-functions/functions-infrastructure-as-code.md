---
title: "Automatisera distributionen resurs för en funktionsapp i Azure Functions | Microsoft Docs"
description: "Lär dig hur du skapar en Azure Resource Manager-mall som distribuerar appen funktion."
services: Functions
documtationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, serverlösa arkitektur, infrastruktur som kod, azure resource manager"
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: e6b3deb9353ba07d693d71822d37a1761dd70d67
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatisera resurs distribution för din funktionsapp i Azure Functions

Du kan använda en Azure Resource Manager-mall för att distribuera en funktionsapp. Den här artikeln beskrivs de resurser som krävs och parametrar för att göra detta. Du kan behöva distribuera ytterligare resurser, beroende på den [utlösare och bindningar](functions-triggers-bindings.md) i funktionen appen.

Mer information om hur du skapar mallar finns [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

För exempelmallar, se:
- [funktionsapp på förbrukning plan]
- [funktionsapp på Azure App Service-plan]

## <a name="required-resources"></a>Resurser som krävs

En funktionsapp kräver följande resurser:

* En [Azure Storage](../storage/index.yml) konto
* En värd plan (förbrukning plan eller programtjänstplanen)
* En funktionsapp 

### <a name="storage-account"></a>Lagringskonto

Ett Azure storage-konto krävs för en funktionsapp. Du behöver en generella-konto som har stöd för blobbar, tabeller, köer och filer. Mer information finns i [Azure Functions lagringskraven för kontot](functions-create-function-app-portal.md#storage-account-requirements).

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

Dessutom egenskaperna `AzureWebJobsStorage` och `AzureWebJobsDashboard` måste anges som app-inställningar i konfigurationen. Azure Functions-runtime använder den `AzureWebJobsStorage` anslutningssträngen för att skapa interna köer. Anslutningssträngen `AzureWebJobsDashboard` används för att logga på Azure Table storage och kraften i **övervakaren** i portalen.

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

### <a name="hosting-plan"></a>Värd för planen

Definitionen av värd planen varierar beroende på om du använder en förbrukning eller App Service-plan. Se [distribuera en funktionsapp förbrukning planen](#consumption) och [distribuera en funktionsapp på programtjänstplanen](#app-service-plan).

### <a name="function-app"></a>Funktionsapp

Funktionen appresursen definieras med hjälp av en resurs av typen **Microsoft.Web/Site** och typ **functionapp**:

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

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Distribuera en funktionsapp förbrukning planen

Du kan köra en funktionsapp i två olika lägen: förbrukning planerings- och App Service-plan. Förbrukning planen tilldelar automatiskt datorkraft när koden körs skalas ut som behövs för att hantera belastningen och skalas när koden inte körs. Så du behöver inte betala för inaktiv virtuella datorer och du behöver inte reserverad kapacitet i förväg. Mer information om värd planer finns [Azure Functions användnings- och App Service-planer](functions-scale.md).

En exempelmall av Azure Resource Manager finns [funktionsapp på förbrukning plan].

### <a name="create-a-consumption-plan"></a>Skapa en plan för förbrukning

En plan för förbrukning är en särskild typ av resurs ”serverfarm”. Du anger det med hjälp av den `Dynamic` värde för den `computeMode` och `sku` egenskaper:

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

Dessutom kan en plan för förbrukning kräver två ytterligare inställningar i konfigurationen: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` och `WEBSITE_CONTENTSHARE`. Dessa egenskaper konfigurera storage-konto och sökvägen där appen Funktionskoden och konfiguration lagras.

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

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Distribuera en funktionsapp på App Service-plan

Funktionen appen körs på dedikerade virtuella datorer på Basic, Standard och Premium-SKU: er, liknar webbappar i App Service-plan. Mer information om hur programtjänstplanen fungerar finns i [Azure App Service-planer djupgående översikt över](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

En exempelmall av Azure Resource Manager finns [funktionsapp på Azure App Service-plan].

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

När du har valt ett skalningsalternativ, skapa en funktionsapp. Appen är behållare som innehåller alla funktioner.

En funktionsapp har många underordnade resurser som du kan använda i din distribution, inklusive inställningar för appen och ange alternativ för källa. Kanske du vill ta bort den **sourcecontrols** underordnade resursen och använda en annan [distributionsalternativet](functions-continuous-deployment.md) i stället.

> [!IMPORTANT]
> För att distribuera programmet med hjälp av Azure Resource Manager, är det viktigt att förstå hur resurser har distribuerats i Azure. I följande exempel tillämpas på den översta nivån konfigurationer med hjälp av **siteConfig**. Det är viktigt att ange dessa konfigurationer på översta nivån, eftersom de överför information till funktioner-motorn för körning och distribution. Översta information krävs innan underordnat **sourcecontrols/web** resurs används. Även om det är möjligt att konfigurera inställningarna i den underordnade nivån **config/appSettings** resurs, i vissa fall måste du distribuera appen funktionen *innan* **config/appSettings** tillämpas. Till exempel när du använder funktioner med [Logikappar](../logic-apps/index.md), dina funktioner är beroende av en annan resurs.

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
> Den här mallen använder den [projekt](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) app inställningar-värde som anger baskatalog som distributionsmotorn funktioner (Kudu) söker efter distribuerbara kod. I vår databas våra funktioner finns i en undermapp till den **src** mapp. Så i föregående exempel vi värdet app-inställningar till `src`. Om dina funktioner finns i roten för din databas, eller om du inte distribuerar från källkontroll, kan du ta bort det här värdet för app-inställningar.

## <a name="deploy-your-template"></a>Distribuera mallen

Du kan använda något av följande sätt att distribuera mallen:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Distribuera till Azure-knappen

Ersätt ```<url-encoded-path-to-azuredeploy-json>``` med en [URL-kodade](https://www.bing.com/search?q=url+encode) version av rådata sökvägen till din `azuredeploy.json` -filen i GitHub.

Här är ett exempel som använder markdown:

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Här är ett exempel som använder HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du utvecklar och konfigurera Azure Functions.

* [Azure Functions, info för utvecklare](functions-reference.md)
* [Så här konfigurerar du Azure funktionen app-inställningar](functions-how-to-use-azure-function-app-settings.md)
* [Skapa din första Azure-funktion](functions-create-first-azure-function.md)

<!-- LINKS -->

[funktionsapp på förbrukning plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[funktionsapp på Azure App Service-plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
