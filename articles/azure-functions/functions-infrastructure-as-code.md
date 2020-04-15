---
title: Automatisera funktionsresursdistribution till Azure
description: Lär dig hur du skapar en Azure Resource Manager-mall som distribuerar din funktionsapp.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 7155a3fa9481ef5f2da62d85d4a932ad5e8e8ab1
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382528"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatisera resursdistribution för din funktionsapp i Azure Functions

Du kan använda en Azure Resource Manager-mall för att distribuera en funktionsapp. I den här artikeln beskrivs de resurser och parametrar som krävs för detta. Du kan behöva distribuera ytterligare resurser, beroende på [utlösare och bindningar](functions-triggers-bindings.md) i funktionsappen.

Mer information om att skapa mallar finns i [Redigera Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md).

Exempelmallar finns i:
- [Funktionsapp på förbrukningsplan]
- [Funktionsapp på Azure App Service-abonnemang]

## <a name="required-resources"></a>Nödvändiga resurser

En Azure Functions-distribution består vanligtvis av följande resurser:

| Resurs                                                                           | Krav | Syntax- och egenskapsreferens                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| En funktionsapp                                                                     | Krävs    | [Microsoft.Webbplatser](/azure/templates/microsoft.web/sites)                             |   |
| Ett [Azure Storage-konto](../storage/index.yml)                                   | Krävs    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| En [application insights-komponent](../azure-monitor/app/app-insights-overview.md) | Valfri    | [Microsoft.Insights/-komponenter](/azure/templates/microsoft.insights/components)         |   |
| En [värdplan](./functions-scale.md)                                             | Valfritt<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1.</sup> En värdplan krävs bara när du väljer att köra din funktionsapp på en [Premium-plan](./functions-premium-plan.md) eller på en [App Service-plan.](../app-service/overview-hosting-plans.md)

> [!TIP]
> Även om det inte krävs, rekommenderar vi starkt att du konfigurerar Application Insights för din app.

<a name="storage"></a>
### <a name="storage-account"></a>Lagringskonto

Ett Azure-lagringskonto krävs för en funktionsapp. Du behöver ett konto för allmänt ändamål som stöder blobbar, tabeller, köer och filer. Mer information finns i [Azure Functions lagringskontokrav](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Dessutom måste egenskapen `AzureWebJobsStorage` anges som en appinställning i platskonfigurationen. Om funktionsappen inte använder Application Insights för `AzureWebJobsDashboard` övervakning bör den också ange som en appinställning.

Azure Functions-körningen `AzureWebJobsStorage` använder anslutningssträngen för att skapa interna köer.  När Application Insights inte är aktiverat använder `AzureWebJobsDashboard` körningen anslutningssträngen för att logga till Azure Table-lagring och driva fliken **Övervakare** i portalen.

Dessa egenskaper anges i `appSettings` samlingen `siteConfig` i objektet:

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

Application Insights rekommenderas för att övervaka dina funktionsappar. Application Insights-resursen definieras med typen **Microsoft.Insights/components** och **typwebbplatsen:**

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
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Dessutom måste instrumenteringsnyckeln tillhandahållas till funktionsappen med hjälp av programinställningen. `APPINSIGHTS_INSTRUMENTATIONKEY` Den här egenskapen `appSettings` anges i `siteConfig` samlingen i objektet:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Hosting plan

Definitionen av värdplanen varierar och kan vara något av följande:
* [Förbrukningsplan](#consumption) (standard)
* [Premiumplan](#premium)
* [App Service plan](#app-service-plan)

### <a name="function-app"></a>Funktionsapp

Funktionsappresursen definieras med hjälp av en resurs av typen **Microsoft.Web/sites** och kind **functionapp:**

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
}
```

> [!IMPORTANT]
> Om du uttryckligen definierar en värdplan behövs ytterligare ett objekt i arrayen dependsOn:`"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

En funktionsapp måste innehålla följande programinställningar:

| Inställningsnamn                 | Beskrivning                                                                               | Exempelvärden                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | En anslutningssträng till ett lagringskonto som funktionskörningen använder för intern kö | Se [lagringskonto](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Versionen av Azure Functions-körningen                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Språkstacken som ska användas för funktioner i den här appen                                   | `dotnet`, `node` `java`, `python`eller`powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Behövs endast om `node` du använder språkstacken, anger vilken version som ska användas              | `10.14.1`                             |

Dessa egenskaper anges i `appSettings` samlingen `siteConfig` i egenskapen:

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

Förbrukningsplanen allokerar automatiskt beräkningskraft när koden körs, skalas ut efter behov för att hantera inläsning och skalas sedan in när koden inte körs. Du behöver inte betala för inaktiva virtuella datorer, och du behöver inte reservera kapacitet i förväg. Mer information finns i [Azure Functions skala och vara värd .](functions-scale.md#consumption-plan)

Ett exempel på en Azure Resource Manager-mall finns i [Funktionsapp på förbrukningsplan].

### <a name="create-a-consumption-plan"></a>Skapa en förbrukningsplan

En förbrukningsplan behöver inte definieras. En skapas eller väljs automatiskt per region när du skapar själva funktionsappresursen.

Förbrukningsplanen är en särskild typ av resurs för serverfarm. För Windows kan du ange `Dynamic` det med `computeMode` `sku` hjälp av värdet för egenskaperna och:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Förbrukningsplanen kan inte uttryckligen definieras för Linux. Det kommer att skapas automatiskt.

Om du uttryckligen definierar din förbrukningsplan `serverFarmId` måste du ange egenskapen i appen så att den pekar på planens resurs-ID. Du bör se till att `dependsOn` funktionsappen har en inställning för planen också.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

#### <a name="windows"></a>Windows

I Windows kräver en förbrukningsplan ytterligare två `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` `WEBSITE_CONTENTSHARE`inställningar i platskonfigurationen: och . Dessa egenskaper konfigurerar lagringskontot och filsökvägen där funktionsappkoden och konfigurationen lagras.

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

På Linux måste funktionsappen `kind` `functionapp,linux`ha sin inställning `reserved` till , `true`och den måste ha egenskapen inställd på:

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

Premium-planen erbjuder samma skalning som förbrukningsplanen men innehåller dedikerade resurser och ytterligare funktioner. Mer information finns i [Azure Functions Premium Plan](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Skapa en Premium-plan

En Premium-plan är en speciell typ av "serverfarm"-resurs. Du kan ange det `EP1` `EP2`med `EP3` hjälp `Name` av antingen `sku` , eller för egenskapsvärdet i [beskrivningsobjektet](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Skapa en funktionsapp

En funktionsapp på en `serverFarmId` Premium-plan måste ha egenskapen inställd på resurs-ID för den plan som skapats tidigare. Dessutom kräver en Premium-plan ytterligare två inställningar `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` `WEBSITE_CONTENTSHARE`i platskonfigurationen: och . Dessa egenskaper konfigurerar lagringskontot och filsökvägen där funktionsappkoden och konfigurationen lagras.

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

I App Service-planen körs din funktionsapp på dedikerade virtuella datorer på basic-, standard- och premium-SKU:er, liknande webbappar. Mer information om hur App Service-planen fungerar finns [i översikten över Azure App Service-planer på djupet](../app-service/overview-hosting-plans.md).

Ett exempel på en Azure Resource Manager-mall finns i [Funktionsapp på Azure App Service-planen].

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

En apptjänstplan definieras av en "serverfarm"-resurs.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Om du vill köra din app `kind` på `Linux`Linux måste du också ställa in till:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Skapa en funktionsapp

En funktionsapp på en App `serverFarmId` Service-plan måste ha egenskapen inställd på resurs-ID för den plan som skapats tidigare.

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

Linux-appar bör `linuxFxVersion` också `siteConfig`innehålla en egenskap under . Om du bara distribuerar kod bestäms värdet för detta av önskad körningsstack:

| Stack            | Exempelvärde                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
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

Om du [distribuerar en anpassad behållaravbildning](./functions-create-function-linux-custom-image.md)måste du ange den med `linuxFxVersion` och inkludera konfiguration som gör att avbildningen kan dras, som i Web App for [Containers](/azure/app-service/containers). Ställ också `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false`in på eftersom appinnehållet finns i själva behållaren:

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

En funktionsapp har många underordnade resurser som du kan använda i distributionen, inklusive appinställningar och alternativ för källkontroll. Du kan också välja att ta bort **den underordnade resursen sourcecontrols** och använda ett annat [distributionsalternativ](functions-continuous-deployment.md) i stället.

> [!IMPORTANT]
> För att distribuera ditt program med hjälp av Azure Resource Manager är det viktigt att förstå hur resurser distribueras i Azure. I följande exempel tillämpas konfigurationer på den högsta nivån med hjälp av **siteConfig**. Det är viktigt att ställa in dessa konfigurationer på en toppnivå, eftersom de förmedlar information till funktionskörnings- och distributionsmotorn. Information på översta nivån krävs innan den underordnade **källkontrollen/webbresursen** tillämpas. Även om det är möjligt att konfigurera dessa inställningar i **config/appSettings-resursen** på barnnivå, måste funktionsappen i vissa fall distribueras *innan* **konfiguration/appInställningar** tillämpas. När du till exempel använder funktioner med [Logic Apps](../logic-apps/index.yml)är dina funktioner beroende av en annan resurs.

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
> Den här mallen använder värdet [för inställningar](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) för Project-appen, som anger baskatalogen där Funktionsdistributionsmotorn (Kudu) söker efter distributionsbar kod. I vårt arkiv finns våra funktioner i en undermapp till **src-mappen.** Så i föregående exempel ställer vi in värdet `src`för appinställningar till . Om dina funktioner finns i roten i databasen, eller om du inte distribuerar från källkontrollen, kan du ta bort värdet för appinställningar.

## <a name="deploy-your-template"></a>Distribuera mallen

Du kan använda något av följande sätt att distribuera mallen:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST-API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Knappen Distribuera till Azure

Ersätt ```<url-encoded-path-to-azuredeploy-json>``` med en [URL-kodad](https://www.bing.com/search?q=url+encode) version av `azuredeploy.json` filens råsökväg i GitHub.

Här är ett exempel som använder markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Här är ett exempel som använder HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Distribuera med hjälp av PowerShell

Följande PowerShell-kommandon skapar en resursgrupp och distribuerar en mall som skapar en funktionsapp med de resurser som krävs. Om du vill köra lokalt måste du ha [Azure PowerShell](/powershell/azure/install-az-ps) installerat. Kör [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) för att logga in.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Om du vill testa den här distributionen kan du använda en [mall som den här](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) som skapar en funktionsapp i Windows i en förbrukningsplan. Ersätt `<function-app-name>` med ett unikt namn för din funktionsapp.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du utvecklar och konfigurerar Azure Functions.

* [Azure Functions, info för utvecklare](functions-reference.md)
* [Konfigurerar inställningar för Azure-funktionsappar](functions-how-to-use-azure-function-app-settings.md)
* [Skapa din första Azure-funktion](functions-create-first-azure-function.md)

<!-- LINKS -->

[Funktionsapp på förbrukningsplan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Funktionsapp på Azure App Service-abonnemang]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
