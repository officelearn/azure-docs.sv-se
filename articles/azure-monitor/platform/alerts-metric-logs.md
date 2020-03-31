---
title: Skapa måttaviseringar för loggar i Azure Monitor
description: Självstudiekurs om hur du skapar måttaviseringar i nära realtid på populära logganalysdata.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/17/2018
ms.subservice: alerts
ms.openlocfilehash: 6b21f228858954292e7a3bc5561d5e86fcfaaf41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055186"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Skapa måttaviseringar för loggar i Azure Monitor

## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor stöder [måttaviseringstyp](../../azure-monitor/platform/alerts-metric-near-real-time.md) som har fördelar jämfört med de [klassiska aviseringarna](../../azure-monitor/platform/alerts-classic-portal.md). Mått är tillgängliga för [stor lista över Azure-tjänster](../../azure-monitor/platform/metrics-supported.md). I den här artikeln beskrivs användningen av `Microsoft.OperationalInsights/workspaces`en delmängd (det vill än) för resurs - .

Du kan använda måttaviseringar på populära Logganalysloggar som extraheras som mått som en del av mått från loggar, inklusive resurser i Azure eller lokalt. De Log Analytics-lösningar som stöds visas nedan:

- [Prestandaräknare](../../azure-monitor/platform/data-sources-performance-counters.md) för Windows & Linux-maskiner
- [Pulsslagsposter för Agent Health](../../azure-monitor/insights/solution-agenthealth.md)
- [Uppdatera hanteringsposter](../../automation/automation-update-management.md)
- [Loggböcker](../../azure-monitor/platform/data-sources-windows-events.md) för händelsedata

Det finns många fördelar med att använda **måttaviseringar för loggar** över frågebaserade [loggaviseringar](../../azure-monitor/platform/alerts-log.md) i Azure. några av dem är listade nedan:

- Måttaviseringar erbjuder övervakningskapacitet i nära realtid och måttaviseringar för loggar gafflar data från loggkälla för att säkerställa samma.
- Måttaviseringar är tillståndskänsliga – meddelar bara en gång när aviseringen utlöses och en gång när aviseringen är löst. i motsats till loggvarningar, som är tillståndslösa och fortsätter att skjuta vid varje intervall om varningsvillkoret är uppfyllt.
- Måttaviseringar för logg ger flera dimensioner, vilket gör att filtrering till specifika värden som datorer, OS-typ, etc. enklare; utan behov av penningfråga i analytics.

> [!NOTE]
> Specifikt mått och/eller dimension visas bara om det finns data för det i vald period. Dessa mått är tillgängliga för kunder med Azure Log Analytics-arbetsytor.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Mått och dimensioner som stöds för loggar

 Måttaviseringar stöder aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera måttet till rätt nivå. Den fullständiga listan över mått som stöds för loggar från [Log Analytics-arbetsytor](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) visas. lösningar som stöds.

> [!NOTE]
> Om du vill visa ett mått som stöds som extraherats från en Log Analytics-arbetsyta via [Azure Monitor - Metrics](../../azure-monitor/platform/metrics-charts.md)måste en måttavisering för logg skapas för det specifika måttet. De dimensioner som valts i måttaviseringen för loggar - visas endast för utforskning via Azure Monitor - Mått.

## <a name="creating-metric-alert-for-log-analytics"></a>Skapa måttavisering för Logganalys

Måttdata från populära loggar leds innan de bearbetas i Log Analytics, till Azure Monitor - Metrics. Detta gör det möjligt för användare att utnyttja funktionerna i den metriska plattformen samt måttavisering - inklusive att ha varningar med frekvens så låg som 1 minut.
Nedan listas hur du skapar en måttavisering för loggar.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Förutsättningar för måttavisering för loggar

Innan Mått för loggar som samlas in på Logganalysdata fungerar måste följande vara konfigurerat och tillgängligt:

1. **Active Log Analytics Workspace**: En giltig och aktiv Log Analytics-arbetsyta måste finnas. Mer information finns [i Skapa en logganalysarbetsyta i Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md).
2. **Agenten är konfigurerad för Log Analytics Workspace**: Agent måste konfigureras för virtuella Azure-datorer (och/eller) lokala virtuella datorer för att skicka data till logganalysarbetsytan som användes i tidigare steg. Mer information finns i [Log Analytics - Agent Översikt](../../azure-monitor/platform/agents-overview.md).
3. **Log Analytics Solutions som stöds är installerat:** Log Analytics-lösningen ska konfigureras och skicka data till Log Analytics-arbetsytan – lösningar som stöds är [prestandaräknare för Windows & Linux,](../../azure-monitor/platform/data-sources-performance-counters.md) [Heartbeat-poster för Agent Health,](../../azure-monitor/insights/solution-agenthealth.md) [Update management](../../automation/automation-update-management.md)och [Event data](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Log Analytics-lösningar som konfigurerats för att skicka loggar:** Log Analytics-lösning bör ha de loggar/data som krävs som motsvarar [mått som stöds för Log Analytics-arbetsytor](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) aktiverade. För *%Tillgängligt minne* måste den till exempel konfigureras i [lösning för prestandaräknare](../../azure-monitor/platform/data-sources-performance-counters.md) först.

## <a name="configuring-metric-alert-for-logs"></a>Konfigurera måttavisering för loggar

 Måttaviseringar kan skapas och hanteras med hjälp av Azure-portalen, Resource Manager-mallar, REST API, PowerShell och Azure CLI. Eftersom måttaviseringar för loggar, är en variant av måttaviseringar - när förutsättningarna är klara kan måttavisering för loggar skapas för angiven Log Analytics-arbetsyta. Alla egenskaper och funktioner för [måttaviseringar](../../azure-monitor/platform/alerts-metric-near-real-time.md) kommer att tillämpas på måttaviseringar för loggar, liksom; inklusive nyttolastschema, tillämpliga kvotgränser och fakturerat pris.

För steg-för-steg-information och exempel - se [skapa och hantera måttaviseringar](https://aka.ms/createmetricalert). Specifikt för måttaviseringar för loggar – följ instruktionerna för hantering av måttaviseringar och se till att följande är:

- Mål för måttavisering är en giltig *Log Analytics-arbetsyta*
- Signal som valts för måttavisering för vald *Log Analytics-arbetsyta* är av typen **Mått**
- Filtrera efter specifika villkor eller resurser med hjälp av dimensionsfilter. mätvärden för loggar är flerdimensionella
- När du konfigurerar *signallogik*kan en enda avisering skapas för att sträcka sig över flera dimensioner (som dator)
- Om **du inte** använder Azure-portalen för att skapa måttavisering för vald Log *Analytics-arbetsyta*; då måste användaren manuellt först skapa en explicit regel för att konvertera loggdata till ett mått med Hjälp av [Azure Monitor - Schemalagda frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> När du skapar måttavisering för Log Analytics-arbetsyta via Azure-portalen – motsvarande regel för konvertering av loggdata till mått via [Azure Monitor – skapas schemalagda frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) automatiskt i bakgrunden, *utan att användaren behöver göra det.* För måttavisering för loggar skapas med andra medel än Azure portal, se [Resursmall för måttaviseringar för loggar](#resource-template-for-metric-alerts-for-logs) avsnitt om exempel på exempelsätt att skapa en ScheduledQueryRule baserad logg till måttkonverteringsregel innan måttvarning skapas - annars kommer det inte att finnas några data för måttaviseringen på loggar som skapats.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Resursmall för måttaviseringar för loggar

Som tidigare nämnts är processen för att skapa måttaviseringar från loggar tvådelade:

1. Skapa en regel för att extrahera mått från loggar som stöds med hjälp av schemalagtQueryRule API
2. Skapa en måttavisering för mått som extraherats från log (i steg1) och Log Analytics-arbetsyta som målresurs

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Måttaviseringar för loggar med statiskt tröskelvärde

För att uppnå samma sak kan man använda exempelmallen för Azure Resource Manager nedan - där skapandet av en statisk tröskelmåttvarning beror på att regeln för att extrahera mått från loggar framgångsrikt extraheras via scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
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

Säg att ovanstående JSON sparas som metricfromLogsAlertStatic.json - då kan det kombineras med en parameter JSON-fil för Resursmall baserad skapande. En exempelparameter JSON-fil visas nedan:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Förutsatt att parameterfilen ovan sparas som metricfromLogsAlertStatic.parameters.json; då kan man skapa måttavisering för loggar med [resursmall för att skapa i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Alternativt kan man använda Azure Powershell kommandot nedan också:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Eller använd distribuera Resursmall med Hjälp av Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Måttaviseringar för loggar med dynamiska tröskelvärden

För att uppnå samma sak kan man använda exempelmallen för Azure Resource Manager nedan – där skapandet av en måttavisering för dynamiska tröskelvärden beror på att regeln för att extrahera mått från loggar extraheras via schemalagdasekvenser.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
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
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
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

Säg att ovanstående JSON sparas som metricfromLogsAlertDynamic.json - då kan det kombineras med en parameter JSON-fil för Resursmall baserad skapande. En exempelparameter JSON-fil visas nedan:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Förutsatt att parameterfilen ovan sparas som metricfromLogsAlertDynamic.parameters.json; då kan man skapa måttavisering för loggar med [resursmall för att skapa i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Alternativt kan man använda Azure Powershell kommandot nedan också:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Eller använd distribuera Resursmall med Hjälp av Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [måttaviseringarna](alerts-metric.md).
- Läs mer om [loggaviseringar i Azure](../../azure-monitor/platform/alerts-unified-log.md).
- Läs mer om [aviseringar i Azure](alerts-overview.md).
