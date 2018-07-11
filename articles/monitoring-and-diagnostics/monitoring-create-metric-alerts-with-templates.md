---
title: Skapa en metrisk varning med en Resource Manager-mall
description: Lär dig hur du använder Resource Manager-mall för att skapa en metrisk varning.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/26/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 4dba3d182c7c2927aa4feb88e70fe5711fcc6818
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932219"
---
# <a name="create-a-metric-alert-with-a-resource-manager-template"></a>Skapa en metrisk varning med en Resource Manager-mall
Den här artikeln visar hur du kan använda en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) konfigurera [nyare måttaviseringar](monitoring-near-real-time-metric-alerts.md) i Azure Monitor. Resource Manager-mallar kan du programmässigt ställa in aviseringar på ett konsekvent och reproducerbara sätt i alla dina miljöer. Nyare måttaviseringar finns för närvarande på [den här uppsättningen resurstyper](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).

> [!IMPORTANT]
> Resource Manager-mall som angetts för måttavisering inte fungerar för resurstypen: Microsoft.OperationalInsights/workspaces; stöd för mått från Log Analytics finns i förhandsversion. Användare som vill använda förhandsversionsfunktionerna med resursmall kan kontakta [Feedback för Azure-aviseringar](mailto:azurealertsfeedback@microsoft.com)


De grundläggande stegen är följande:

1. Använd någon av mallarna nedan som en JSON-fil som beskriver hur du skapar aviseringen.
2. Redigera och använda motsvarande parameterfilen som en JSON för att anpassa aviseringen
3. Distribuera mallen med hjälp av [alla distributionsmetod](../azure-resource-manager/resource-group-template-deploy.md).


## <a name="resource-manager-template-for-a-simple-metric-alert"></a>Resource Manager-mall för en enkel måttavisering
Om du vill skapa en avisering med en Resource Manager-mall, skapar du en resurs av typen `Microsoft.Insights/metricAlerts` och Fyll i alla relaterade egenskaper. Nedan visas ett exempel på mall som skapar en måttaviseringsregel.

Spara json nedanför som simplemetricalert.json i den här genomgången.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],   
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}
```

En förklaring av schemat och egenskaper för en varningsregel [finns här](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate).

Du kan ange värden för parametrarna på kommandoraden eller via en parameterfil. En parameter-exempelfil finns nedan. 

Spara json nedanför som simplemetricalert.parameters.json och ändra det efter behov.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Metric Alert"
        },
        "alertDescription": {
            "value": "New metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


Du kan skapa metrisk varning med hjälp av mall och parametrar filen med hjälp av PowerShell eller Azure CLI.

Använda Azure PowerShell

```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simplemetricalert.json -TemplateParametersFile simplemetricalert.parameters.json
```


Använda Azure CLI
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simplemetricalert.json \
    --parameters @simplemetricalert.parameters.json
```

> [!NOTE]
>
> Även metrisk varning skapas i en annan resursgrupp till målresursen, rekommenderar vi använder samma resursgrupp som din målresursen.

## <a name="resource-manager-template-for-a-more-advanced-metric-alert"></a>Resource Manager-mall för en mer avancerade metrisk varning
Nyare måttaviseringar stöd avisering för flerdimensionella mått samt stöd för flera villkor. Du kan använda följande mall för att skapa en mer avancerad metrisk varning på endimensionella mått och ange flera villkor.

Spara json nedanför som advancedmetricalert.json i den här genomgången.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion1":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "criterion2": {
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criterion1": "[array(parameters('criterion1'))]",
        "criterion2": "[array(parameters('criterion2'))]",
        "criteria": "[concat(variables('criterion1'),variables('criterion2'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}
```

Du kan använda mallen ovan tillsammans med parameterfilen som anges nedan. 

Spara och ändra json nedanför som advancedmetricalert.parameters.json i den här genomgången.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Multi-dimensional Metric Alert (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion1": {
            "value": {
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Includes",
                            "values": ["Success"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Includes",
                            "values": ["GetBlob"]
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "criterion2": {
            "value":{
                "name": "2nd criterion",
                "metricName": "SuccessE2ELatency",
                "dimensions": [
                    {
                        "name":"ApiName",
                        "operator": "Includes",
                        "values": ["GetBlob"]
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "250",
                "timeAggregation": "Average"
            }      
        },          
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/Default-ActivityLogAlerts/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }    
}
```


Du kan skapa metrisk varning med hjälp av mall och parametrar filen med hjälp av PowerShell eller Azure CLI från din aktuella arbetskatalog

Använda Azure PowerShell
```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advancedmetricalert.json -TemplateParametersFile advancedmetricalert.parameters.json
```



Använda Azure CLI
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advancedmetricalert.json \
    --parameters @advancedmetricalert.parameters.json
```

>[!NOTE]
>
> Även metrisk varning skapas i en annan resursgrupp till målresursen, rekommenderar vi använder samma resursgrupp som din målresursen.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [aviseringar i Azure](monitoring-overview-unified-alerts.md)
* Lär dig hur du [skapa en åtgärdsgrupp med Resource Manager-mallar](monitoring-create-action-group-with-resource-manager-template.md)
