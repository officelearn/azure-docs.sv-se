---
title: Skapa diagnostikinställning i Azure med hjälp av Resource Manager-mallen
description: Skapa diagnostikinställningar med hjälp av en Resource Manager-mall för att vidarebefordra Azure-plattformsloggar till Azure Monitor-loggar, Azure-lagring eller Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672437"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Skapa diagnostikinställning i Azure med hjälp av en Resource Manager-mall
[Diagnostikinställningar](diagnostic-settings.md) i Azure Monitor anger var [plattformsloggar](platform-logs-overview.md) som samlas in av Azure-resurser och Azure-plattformen som de är beroende av ska skickas. Den här artikeln innehåller information och exempel för hur du använder en [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) för att skapa och konfigurera diagnostikinställningar för att samla in plattformsloggar till olika destinationer.

> [!NOTE]
> Eftersom du inte kan [skapa en diagnostikinställning](diagnostic-settings.md) för Azure Activity-loggen med PowerShell eller CLI som diagnostikinställningar för andra Azure-resurser skapar du en Resource Manager-mall för aktivitetsloggen med hjälp av informationen i den här artikeln och distribuerar mallen med PowerShell eller CLI.

## <a name="deployment-methods"></a>Distributionsmetoder
Du kan distribuera Resource Manager-mallar med valfri giltig metod, inklusive PowerShell och CLI. Diagnostikinställningar för aktivitetsloggen måste `az deployment create` distribueras `New-AzDeployment` till en prenumeration som använder för CLI eller PowerShell. Diagnostikinställningar för resursloggar måste distribueras `az group deployment create` till `New-AzResourceGroupDeployment` en resursgrupp som använder för CLI eller PowerShell.

Mer information finns [i Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) och distribuera resurser med Resource [Manager-mallar och Azure CLI.](../../azure-resource-manager/templates/deploy-cli.md) 





## <a name="resource-logs"></a>Resursloggar
Lägg till en typresurs `<resource namespace>/providers/diagnosticSettings` i mallen för resursloggar. Egenskapsavsnittet följer formatet som beskrivs i [Diagnostikinställningar - Skapa eller uppdatera](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Ange `category` en `logs` i avsnittet för var och en av de kategorier som gäller för den resurs som du vill samla in. Lägg `metrics` till egenskapen för att samla in resursmått på samma mål om [resursen stöder mått](metrics-supported.md).

Följande är en mall som samlar in en resursloggkategori för en viss resurs till en Log Analytics-arbetsyta, ett lagringskonto och en händelsehubb.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Exempel
Följande är ett exempel som skapar en diagnostikinställning för en automatisk skalningsinställning som möjliggör direktuppspelning av resursloggar till en händelsehubb, ett lagringskonto och en Log Analytics-arbetsyta.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Aktivitetslogg
Lägg till en resurs av typen `Microsoft.Insights/diagnosticSettings`. De tillgängliga kategorierna visas i [Kategorier i aktivitetsloggen](activity-log-view.md#categories-in-the-activity-log). Följande är en mall som samlar in alla aktivitetsloggkategorier till en Log Analytics-arbetsyta, ett lagringskonto och händelsenav.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>Nästa steg
* Läs mer om [plattformsloggar i Azure](platform-logs-overview.md).
* Läs mer om [diagnostikinställningar](diagnostic-settings.md).
