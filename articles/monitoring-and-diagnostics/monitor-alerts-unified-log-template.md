---
title: Skapa en loggavisering med en Resource Manager-mall
description: 'Lär dig mer om att skapa log avisering med hjälp av en Azure Resource Manager-mall och API: et.'
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 6ec07d02e61d50aa588d75ba7337eb9237e11207
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402905"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Skapa en loggavisering med en Resource Manager-mall
Den här artikeln visar hur du kan hantera [loggaviseringar](monitor-alerts-unified-log.md) programmässigt i skala, i Azure med hjälp av [Azure Resource Manager-mall](..//azure-resource-manager/resource-group-authoring-templates.md) via [Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) och [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md). För närvarande Azure Alerts stöder loggaviseringar på frågor från [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) och [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Hantera log avisering på Log Analytics
Log avisering för [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ingår i den [nya Azure-aviseringar uppleva](monitoring-overview-unified-alerts.md); medan den fortfarande körs av Log Analytics-API: er och är kompatibelt med schema använde tidigare.

> [!NOTE]
> Från och den 14 maj 2018 började alla aviseringar i en arbetsyta automatiskt ska utöka till Azure. Mer information finns i [utöka aviseringarna till Azure](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Med hjälp av Azure Resource Manager-mall
Loggaviseringar för Log Analytics skapas från Varningsregler som kör en sparad sökning med regelbundna intervall. Om resultatet av frågan matchar de angivna villkoren, skapas en aviseringspost och en eller flera åtgärder körs. 

Resursmall för sparade analytics-loggsökning och Log analytics-alertsare tillgängliga i Log Analytics i dokumentationen för. Läs mer i [att lägga till Log Analytics sparade sökningar och aviseringar](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md); som innehåller färgkodats exempel samt information om schemat.

### <a name="using-resource-template-via-apipowershell"></a>Med Resource mall via API/Powershell
Log Analytics avisering REST API är RESTful och kan nås via Azure Resource Manager REST API. API: et därför kan nås från en PowerShell-kommandorad och kommer att bli sökresultat till dig i JSON-format, så att du kan använda resultaten i många olika sätt programmässigt.

Läs mer om [skapa och hantera Varningsregler i Log Analytics med REST API](../log-analytics/log-analytics-api-alerts.md), inklusive exempel för att komma åt API: et från Powershell.

## <a name="managing-log-alert-on-application-insights"></a>Hantera log avisering i Application Insights
Aviseringar för Azure Application Insights finns som en del av de nya Azure-aviseringarna under Azure Monitor. Därför det körs under Azure Monitor-API som [schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) grupp för REST-åtgärden.

### <a name="using-azure-resource-manager-template"></a>Med hjälp av Azure Resource Manager-mall
Log avisering för Application Insights-resurser har en typ av `Microsoft.Insights/scheduledQueryRules/`. Mer information om den här resurstypen finns [Azure Monitor - schemalagda fråga regler API-referens](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

Följande är strukturen för [schemalagda Frågeregler skapa](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) baserat resursmall med exempeldatauppsättning som variabler.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
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
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
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
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
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
> Taggfältet med dolda-länk till målresursen är obligatoriskt i användning av [schemalagda Frågeregler ](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) API-anrop eller resurs-mallen. 

Exempel-json ovan kan sparas som (exempelvis) sampleScheduledQueryRule.json i den här genomgången och kan distribueras med hjälp av [Azure Resource Manager i Azure-portalen](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Med Resource mall via CLI/Powershell
Azure Monitor - schemalagda fråge-API-regler är en REST-API och helt kompatibla med Azure Resource Manager REST API. Därför kan den användas via Powershell med Resource Manager-cmdlet som Azure CLI.

Bilden nedan användning via Azure Resource Managers PowerShell-cmdlet för exemplet resursmall som visades tidigare (sampleScheduledQueryRule.json):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Bilden nedan användning via Azure Resource Manager-kommando i Azure CLI för exemplet resursmall som visades tidigare (sampleScheduledQueryRule.json):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Lyckade åtgärder 201 återgår till tillståndet skapandet av ny regel för varning eller 200 returneras om en befintlig varningsregel ändrades.


## <a name="next-steps"></a>Nästa steg
* Förstå [Webhook-åtgärder för loggaviseringar](monitor-alerts-unified-log-webhook.md)
* Lär dig mer om den nya [Azure-aviseringar](monitoring-overview-unified-alerts.md)
* Läs mer om [Application Insights](../application-insights/app-insights-analytics.md)
* Läs mer om [Log Analytics](../log-analytics/log-analytics-overview.md).   
