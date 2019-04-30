---
title: Automatisera resursdistribution för en funktionsapp i Azure Functions | Microsoft Docs
description: Lär dig hur du skapar en Azure Resource Manager-mall som distribuerar din funktionsapp.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktioner, serverlös arkitektur, infrastruktur som kod, azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 5d028768c062ef7df74d48f83ccc4e27a506f1ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737065"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatisera resursdistribution för din funktionsapp i Azure Functions

Du kan använda en Azure Resource Manager-mall för att distribuera en funktionsapp. Den här artikeln beskriver de resurser som krävs och parametrar för att göra det. Du kan behöva distribuera ytterligare resurser, beroende på den [utlösare och bindningar](functions-triggers-bindings.md) i din funktionsapp.

Mer information om hur du skapar mallar finns i [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

Exempelmallar finns här:
- [Funktionsappen i förbrukningsplan]
- [Funktionsappen i Azure App Service-plan]

> [!NOTE]
> Premium-plan för som är värd för Azure Functions är för närvarande i förhandsversion. Mer information finns i [premiumplan för Azure Functions](functions-premium-plan.md).

## <a name="required-resources"></a>Resurser som krävs

En Azure Functions-distribution består vanligtvis av dessa resurser:

| Resurs                                                                           | Krav | Referens för syntax och egenskaper                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| En funktionsapp                                                                     | Krävs    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| En [Azure Storage](../storage/index.yml) konto                                   | Krävs    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| En [Application Insights](../azure-monitor/app/app-insights-overview.md) komponent | Valfri    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| En [värdplan](./functions-scale.md)                                             | Valfritt<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>en värdplan är endast krävs när du väljer att köra funktionsappen en [premiumprenumerationen](./functions-premium-plan.md) (i förhandsversion) eller på en [App Service-plan](../app-service/overview-hosting-plans.md).

> [!TIP]
> Du måste inte, rekommenderar vi starkt att du konfigurerar Application Insights för din app.

<a name="storage"></a>
### <a name="storage-account"></a>Lagringskonto

Ett Azure storage-konto krävs för en funktionsapp. Du behöver ett konto för generell användning som har stöd för blobbar, tabeller, köer och filer. Mer information finns i [krav för Azure Functions lagringskonto](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
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
]
```

### <a name="application-insights"></a>Application Insights

Application Insights rekommenderas för att övervaka dina funktionsappar. Application Insights-resursen har definierats med typen **Microsoft.Insights/components** och vilken typ **web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

Dessutom instrumenteringsnyckeln måste anges i funktionen app med hjälp av den `APPINSIGHTS_INSTRUMENTATIONKEY` programinställningen. Den här egenskapen har angetts i den `appSettings` samling i den `siteConfig` objekt:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Värdplan

Definitionen av värdplanen varierar och kan vara något av följande:
* [Förbrukningsplan](#consumption) (standard)
* [Premiumprenumerationen](#premium) (i förhandsversion)
* [App Service-plan](#app-service-plan)

### <a name="function-app"></a>Funktionsapp

Funktionen app-resursen definieras med hjälp av en resurs av typen **Microsoft.Web/sites** och typ **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
```

> [!IMPORTANT]
> Om du explicit definierar en värdplan, behövs ytterligare ett objekt i matrisen dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

En funktionsapp måste innehålla dessa programinställningar:

| Inställningsnamn                 | Beskrivning                                                                               | Exempelvärden                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | En anslutningssträng för ett storage-konto som Functions-körningstiden för interna jobbköer | Se [Storage-konto](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Versionen av Azure Functions-körningen                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Språk-stack som ska användas för funktioner i den här appen                                   | `dotnet`, `node`, `java`, eller `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Behövs bara om du använder den `node` språk stack, anger versionen som ska användas              | `10.14.1`                             |

Dessa egenskaper anges i den `appSettings` samling i den `siteConfig` egenskapen:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Distribuera i förbrukningsplan

Med förbrukningsplanen beräkningskraften automatiskt när koden körs, skalas ut efter behov för att hantera belastningen och sedan skalas när koden inte körs. Du behöver betala för virtuella datorer och du behöver inte reserverad kapacitet i förväg. Mer information finns i [Azure Functions skalning och värdtjänster](functions-scale.md#consumption-plan).

En exempelmall av Azure Resource Manager finns i [funktionsappen i förbrukningsplan].

### <a name="create-a-consumption-plan"></a>Skapa en förbrukningsplan

En förbrukningsplan behöver inte definieras. En kommer automatiskt skapade eller markerat på basis av per region när du skapar själva resursen för app funktion.

Med förbrukningsplanen är en särskild typ av resurs ”serverklustret”. För Windows, kan du ange det med hjälp av den `Dynamic` värde för den `computeMode` och `sku` egenskaper:

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

> [!NOTE]
> Med förbrukningsplanen kan inte definieras uttryckligen för Linux. Det kommer att skapas automatiskt.

Om du explicit definierar din förbrukningsplan, behöver du ange den `serverFarmId` egenskapen på appen så att den pekar på resurs-ID för planen. Du bör kontrollera att funktionsappen har en `dependsOn` för planen samt.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

#### <a name="windows"></a>Windows

På Windows, en förbrukningsplan kräver två ytterligare inställningar i plats-konfiguration: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` och `WEBSITE_CONTENTSHARE`. De här egenskaperna konfigurera storage-konto och sökvägen där funktionskod och konfiguration lagras.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
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
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

På Linux, funktionsappen måste ha sin `kind` inställd `functionapp,linux`, och måste ha den `reserved` egenskapen `true`:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Distribuera på Premium-abonnemang

Premiumprenumerationen erbjuder samma skalning som förbrukningsplanen men innehåller dedikerade resurser och fler funktioner. Mer information finns i [Azure Functions Premium Plan (förhandsversion)](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Skapa en premiumplan

En premiumplan är en särskild typ av resurs ”serverklustret”. Du kan ange det genom att använda antingen `EP1`, `EP2`, eller `EP3` för den `sku` egenskapsvärdet.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Skapa en funktionsapp

En funktionsapp på en premiumplan måste ha den `serverFarmId` -egenskapen angetts till resurs-ID för den plan som skapades tidigare. Dessutom kan en premiumplan kräver två ytterligare inställningar i plats-konfiguration: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` och `WEBSITE_CONTENTSHARE`. De här egenskaperna konfigurera storage-konto och sökvägen där funktionskod och konfiguration lagras.

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a> 

## <a name="deploy-on-app-service-plan"></a>Distribuera på App Service-plan

Din funktionsapp körs på dedikerade virtuella datorer på Basic, Standard och Premium-SKU: er, liknande till web apps i App Service-plan. Mer information om hur App Service-planen fungerar finns i den [Azure App Service-planer djupgående översikt över](../app-service/overview-hosting-plans.md).

En exempelmall av Azure Resource Manager finns i [funktionsappen i Azure App Service-plan].

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

En App Service plan definieras av en ”serverklustret”-resurs.

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

Om du vill köra din app på Linux, måste du också ange den `kind` till `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
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

En funktionsapp i en App Service-plan måste ha den `serverFarmId` -egenskapen angetts till resurs-ID för den plan som skapades tidigare.

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Linux-appar bör också innehålla en `linuxFxVersion` egenskapen under `siteConfig`. Om du bara distribuerar kod, avgörs värde för den här av din önskade körningsstack:

| Stack            | Exempelvärde                                         |
|------------------|-------------------------------------------------------|
| Python (förhandsversion) | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Om du är [distribuera en anpassad behållaravbildning](./functions-create-function-linux-custom-image.md), måste du ange den med `linuxFxVersion` och inkluderar konfiguration som gör att din avbildning som ska hämtas i [Web App for Containers](/azure/app-service/containers). Ange dessutom `WEBSITES_ENABLE_APP_SERVICE_STORAGE` till `false`, eftersom ditt appinnehåll tillhandahålls i själva behållaren:

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Anpassa en distribution

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
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
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
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
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
