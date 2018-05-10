---
title: Skapa en logg med en Resource Manager-mall | Microsoft Docs
description: Lär dig mer om att skapa loggen med hjälp av en Azure Resource Manager-mall och API.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 4148c6d3f9d4407681159870b858c629c0b5d878
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Skapa en logg med en Resource Manager-mall
Den här artikeln visar hur du kan hantera [Logga varningar](monitor-alerts-unified-log.md) programmässigt i skala, i Azure med hjälp av [Azure Resource Manager-mall](..//azure-resource-manager/resource-group-authoring-templates.md) via [Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) och [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md). För närvarande Azure aviseringar stöder Logga varningar på frågor från [Azure logganalys](../log-analytics/log-analytics-tutorial-viewdata.md) och [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Hantera loggen avisering på logganalys
Loggen varning för [Azure logganalys](../log-analytics/log-analytics-tutorial-viewdata.md) är integrerat i den [nya Azure aviseringar uppstår](monitoring-overview-unified-alerts.md); medan det fortfarande körs av Log Analytics-API: er och förblir kompatibilitet med tidigare används för att hantera schemat[aviseringar i OMS-portalen](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> Från den 14 maj 2018 startar alla aviseringar i en arbetsyta automatiskt utökas till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Med hjälp av Azure Resource Manager-mall
Loggen aviseringar för Log Analytics skapas Varningsregler som kör en sparad sökning med regelbundna intervall. Om resultatet av frågan matchar de angivna villkoren, skapas en avisering post och en eller flera åtgärder körs. 

Resursmall för [Log analytics sparade sökningen](../log-analytics/log-analytics-log-searches.md) och [logga analytics varningar](../log-analytics/log-analytics-alerts.md) är tillgängliga i logganalys-avsnittet i dokumentationen. Lär dig mer om, [lägger till logganalys sparade sökningar och aviseringar](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md); som innehåller vägledande exempel samt information om schemat.

### <a name="using-resource-template-via-apipowershell"></a>Med hjälp av mallen för resursen via API/Powershell
Log Analytics avisering REST API är RESTful och kan nås via Azure Resource Manager REST API. API: et därför kan nås från en PowerShell-kommandorad och kommer att bli sökresultat för dig i JSON-format, så att du kan använda resultaten på många olika sätt programmässigt.

Lär dig mer om [skapa och hantera Varningsregler i logganalys med REST API](../log-analytics/log-analytics-api-alerts.md); inklusive exempel för att komma åt API: et från Powershell.

## <a name="managing-log-alert-on-application-insights"></a>Hantera logga en avisering för Application Insights
Loggen aviseringar för Azure Application Insights har lagts till som en del av de nya Azure aviseringarna under Azure-Monitor. Därför det körs under Azure-Monitor-API som [schemalagda Frågeregler](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) grupp för REST-åtgärden.

### <a name="using-azure-resource-manager-template"></a>Med hjälp av Azure Resource Manager-mall
Avisering om loggfilen för Application Insights-resurser har en typ av `Microsoft.Insights/scheduledQueryRules/`. Mer information om den här resurstypen finns [Azure Monitor - schemalagda frågan regler API-referens för](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

Följande är struktur för [schemalagda Frågeregler skapa](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate) baserade resurs mallen med exempel datauppsättning som variabler.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",    
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
       "Query":"requests",
       "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
       "Type":"ResultCount"
        },
    "alertSchedule":{
        "Frequency": 15,
        "Time": 60
        },
    "alertActions":{
        "SeverityLevel": "4",
        },
     "alertTrigger":{
       "Operator":"GreaterThan",
       "Threshold":"1"
        },
      "actionGrp":{
       "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
      "Subject": "Customized Email Header",
      "Webhook": "{}"           
        }
  },
  "resources":[ {
     "name":"[variables('alertName')]",
     "type":"Microsoft.Insights/scheduledQueryRules",
     "apiVersion": "2018-04-16",
     "location": "[variables('alertLocation')]",
     "tags":{"hidden-link:[variables('alertTargetResource')]": "Resource"},
     "properties":{
        "description": "[variables('alertDescription')]",
        "enabled": "[variables('alertStatus')]",
        "source": {
            "query": "[variables('alertSource').Query]",
            "dataSourceId": "[variables('alertSource').SourceId]",
            "queryType":"[variables('alertSource').Type]"
        },
       "schedule":{
            "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
            "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
        },
       "action":{
            "severity":"[variables('alertActions').SeverityLevel]",
            "aznsAction":{
                "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                "emailSubject":"[variables('actionGrp').Subject]",
                "customWebhookPayload":"[variables('actionGrp').Webhook]"
            },
        "trigger":{
                "thresholdOperator":"[variables('alertTrigger').Operator]",
                "threshold":"[variables('alertTrigger').Threshold]"
            }
        }
      }
    }
  ]
}
```
> [!IMPORTANT]
> Taggen fält med dolt länk till målresursen är obligatoriskt i användning av [schemalagda Frågeregler ](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) API-anrop eller resurs mallen. 

Ovanstående exempel json kan sparas som sampleScheduledQueryRule.json (Säg) för den här genomgången och kan distribueras med hjälp av [Azure Resource Manager i Azure-portalen](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Med hjälp av Resource mall via CLI/Powershell
Övervakare för Azure - schemalagda frågan regler API är en REST-API och är helt kompatibel med Azure Resource Manager REST API. Därför kan den användas via Powershell med Resource Manager-cmdlet som Azure CLI.

Visas nedan användning via Azure Resource Manager PowerShell-cmdlet för exemplet Resource mallen visas tidigare (sampleScheduledQueryRule.json):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Visas nedan användning via Azure Resource Manager-kommando i Azure CLI för exemplet Resource mallen visas tidigare (sampleScheduledQueryRule.json):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
201 återgår till tillstånd att skapa nya varningsregeln på lyckad åtgärd eller 200 returneras om en befintlig aviseringsregel ändrades.


## <a name="next-steps"></a>Nästa steg
* Förstå [Webhook-åtgärder för logg-aviseringar](monitor-alerts-unified-log-webhook.md)
* Lär dig mer om den nya [Azure aviseringar](monitoring-overview-unified-alerts.md)
* Lär dig mer om [Application Insights](../application-insights/app-insights-analytics.md)
* Lär dig mer om [logganalys](../log-analytics/log-analytics-overview.md).   