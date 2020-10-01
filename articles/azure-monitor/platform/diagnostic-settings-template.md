---
title: Skapa diagnostisk inställning i Azure med Resource Manager-mall
description: Skapa diagnostiska inställningar med en Resource Manager-mall för att vidarebefordra Azure-plattforms loggar till Azure Monitor loggar, Azure Storage eller Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 73f2b3ea90cc94fa3411552c7b812fe53eb4dbbb
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607418"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Skapa diagnostisk inställning i Azure med hjälp av en Resource Manager-mall
[Diagnostikinställningar](diagnostic-settings.md) i Azure Monitor anger var du vill skicka [plattforms loggar](platform-logs-overview.md) som samlas in av Azure-resurser och den Azure-plattform som de är beroende av. Den här artikeln innehåller information och exempel på hur du använder en [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) för att skapa och konfigurera diagnostikinställningar för att samla in plattforms loggar till olika mål.

> [!NOTE]
> Eftersom du inte kan [skapa en diagnostisk inställning](diagnostic-settings.md) för Azure aktivitets loggen med hjälp av POWERSHELL eller CLI som diagnostikinställningar för andra Azure-resurser, skapar du en Resource Manager-mall för aktivitets loggen med hjälp av informationen i den här artikeln och distribuerar mallen med POWERSHELL eller cli.

## <a name="deployment-methods"></a>Distributions metoder
Du kan distribuera Resource Manager-mallar med valfri giltig metod, inklusive PowerShell och CLI. Diagnostikinställningar för aktivitets loggen måste distribueras till en prenumeration med hjälp av `az deployment create` for CLI eller `New-AzDeployment` PowerShell. Diagnostikinställningar för resurs loggar måste distribueras till en resurs grupp med hjälp av `az group deployment create` for CLI eller `New-AzResourceGroupDeployment` PowerShell.

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) och [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) . 





## <a name="resource-logs"></a>Resursloggar
För resurs loggar lägger du till en resurs av typen `<resource namespace>/providers/diagnosticSettings` till mallen. Avsnittet egenskaper följer formatet som beskrivs i [diagnostikinställningar-Create eller Update](/rest/api/monitor/diagnosticsettings/createorupdate). Ange ett `category` i `logs` avsnittet för var och en av de kategorier som är giltiga för den resurs som du vill samla in. Lägg till `metrics` egenskapen för att samla in resurs mått till samma mål om [resursen stöder mått](metrics-supported.md).

Följande är en mall som samlar in en resurs logg kategori för en viss resurs till en Log Analytics arbets yta, lagrings konto och händelsehubben.

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
Följande är ett exempel som skapar en diagnostisk inställning för en autoskalningsinställning som möjliggör strömning av resurs loggar till en händelsehubben, ett lagrings konto och en Log Analytics arbets yta.

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
Lägg till en resurs av typen för Azure aktivitets loggen `Microsoft.Insights/diagnosticSettings` . De tillgängliga kategorierna visas i [Kategorier i aktivitets loggen](./activity-log.md#view-the-activity-log). Följande är en mall som samlar in alla aktivitets logg kategorier till en Log Analytics arbets yta, lagrings konto och händelsehubben.


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
* Läs mer om [plattforms loggar i Azure](platform-logs-overview.md).
* Lär dig mer om [diagnostikinställningar](diagnostic-settings.md).
