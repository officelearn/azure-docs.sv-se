---
title: Skapa, Visa och hantera logg aviseringar med Azure Monitor | Microsoft Docs
description: Använd Azure Monitor för att redigera, Visa och hantera logg aviserings regler i Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 85aaefa12f0cef21e3a367700d1a4899a75e8a90
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298471"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Skapa, Visa och hantera logg aviseringar med Azure Monitor

## <a name="overview"></a>Översikt
Den här artikeln visar hur du skapar och hanterar logg aviseringar med hjälp av aviserings gränssnittet i Azure Portal. Varnings regler definieras av tre komponenter:
- Mål: en specifika Azure-resurs som ska övervakas
- Villkor: ett villkor eller en logik som ska utvärderas för sanningen. Om värdet är true utlöses aviseringen.  
- Åtgärd: ett enskilt samtal som skickas till en mottagare av ett meddelande – e-post, SMS, webhook osv.

Termen **logg avisering** beskriver aviseringar där en logg fråga i [Log Analytics arbets yta](../learn/tutorial-viewdata.md) eller [Application Insights](../app/analytics.md) utvärderas och en avisering utlöses om resultatet är sant. Lär dig mer om funktioner, terminologi och typer från [logg aviseringar – översikt](alerts-unified-log.md).

> [!NOTE]
> Loggdata från [en Log Analytics arbets yta](../../azure-monitor/learn/tutorial-viewdata.md) kan också dirigeras till Azure Monitor Metrics-databasen. Mått aviseringar har [olika beteende](alerts-metric-overview.md), vilket kan vara mer önskvärt beroende på vilka data du arbetar med.   Information om vad och hur du kan skicka loggar till mått finns i [mått avisering för loggar](alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Skapa en logg varnings regel med Azure Portal

1. I [portalen](https://portal.azure.com/)väljer du **övervaka**. I avsnittet väljer du **aviseringar**.

    ![Övervakning](media/alerts-log/AlertsPreviewMenu.png)

1. Klicka på **ny aviserings regel**. 

    ![Lägg till avisering](media/alerts-log/AlertsPreviewOption.png)

1. Fönstret **skapa avisering** visas. Den innehåller fyra delar: 
    - Resursen som aviseringen gäller
    - Villkoret att kontrol lera
    - Den åtgärd som ska vidtas om villkoret är sant
    - Information till namn och beskrivning av aviseringen. 

    ![Skapa regel](media/alerts-log/AlertsPreviewAdd.png)

1. Definiera aviserings villkoret med hjälp av länken **Välj resurs** och ange målet genom att välja en resurs. Filtrera genom att välja *prenumeration*, *resurs typ*och nödvändig *resurs*. 

   ![Välj resurs](media/alerts-log/Alert-SelectResourceLog.png)

1. Se till att **resurs typen** är en analys källa som *Log Analytics* eller *Application Insights* och signal typ som *logg*. Klicka på **Klar**. Använd sedan knappen **Lägg till kriterier** för att visa en lista över signal alternativ som är tillgängliga för resursen. Sök efter och välj **anpassat logg Sök** alternativ för antingen *Log Analytics* eller *Application Insights*beroende på var data för logg aviseringarna finns.

   ![Välj en resurs – anpassad loggs ökning](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Aviserings listor kan importera Analytics-fråga som signal typ – **logg (sparad fråga)** som visas i bilden ovan. Så att användarna kan använda en perfekt fråga i analyser och sedan spara dem för framtida användning i aviseringar. Mer information om hur du använder sparade frågor finns i [använda logg fråga i Azure Monitor](../log-query/log-query-overview.md) och [delad fråga i Application Insights Analytics](../app/app-insights-overview.md).

1. När du har valt skapar du aviserings frågan i fältet **Sök fråga** . Om frågesyntaxen är felaktig visas fältet och fel i rött. 

1. Om frågesyntaxen är korrekt visas historiska data för frågan som en graf med alternativet att ändra tidsfönstret från de senaste sex timmarna till förra veckan.

    ![Konfigurera aviserings regel](media/alerts-log/AlertsPreviewAlertLog.png)

   Den historiska data visualiseringen visas bara om frågeresultaten har tids information. Om frågan resulterar i sammanfattade data eller angivna kolumn värden, visar visningen en enda kurva.
  
   För mått mätningar med Application Insights eller [Log Analytics-API: et](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)kan du ange vilken variabel som data ska grupperas med hjälp av alternativet **samla in** . som du ser här: 
  
   ![sammanställt alternativ](media/alerts-log/aggregate-on.png)



1. Välj sedan logik villkor för **avisering** , sammansättning och tröskelvärde. 

1. Välj den tids period under vilken du vill utvärdera det angivna villkoret med alternativet **period** . 

1. Välj hur ofta aviseringen ska köras i **frekvens**. 

    **Logg aviseringar** kan baseras på:
    - [Antal poster](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): en avisering skapas om antalet poster som returneras av frågan är antingen större eller mindre än det angivna värdet.
    - [Mått mått](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): en avisering skapas om varje *aggregerat värde* i resultatet överskrider det angivna tröskelvärdet och *grupperas efter* valt värde. Antalet överträdelser av en avisering är antalet gånger som tröskelvärdet har överskridits under den valda tids perioden. Du kan ange de totala överträdelserna för en kombination av överträdelser i resultat uppsättningen eller över flera överträdelser för att kräva att överträdelsen måste inträffa i efterföljande exempel.


1. Klicka på **Klar**. 

1. Definiera ett namn för din avisering i fältet **namn på varnings regel** tillsammans med en **Beskrivning** av information om aviseringen och **allvarlighets graden** från de angivna alternativen. Dessa uppgifter återanvänds i alla e-postmeddelanden, aviseringar eller push-meddelanden som görs av Azure Monitor. Dessutom kan du välja att omedelbart aktivera aviserings regeln när den skapas genom att klicka på **Aktivera regel när**du har skapat den.

1. Välj om du vill **Ignorera aviseringar** under en viss tids period.  När du aktiverar under tryckning för varnings regeln inaktive ras åtgärder för regeln under en angiven tids period efter att en ny avisering har skapats. Regeln körs fortfarande och skapar aviserings poster förutsatt att villkoren är uppfyllda. Med den här inställningen kan du åtgärda problemet utan att köra dubbla åtgärder.

   ![Ignorera aviseringar för logg aviseringar](media/alerts-log/AlertsPreviewSuppress.png)

    > [!TIP]
    > Ange ett dolt varnings värde som är större än aviserings frekvensen för att säkerställa att meddelanden stoppas utan överlappande

1. Som det tredje och sista steget anger du om varnings regeln ska utlösa en eller flera **Åtgärds grupper** när aviserings villkoret är uppfyllt. Du kan välja en befintlig åtgärds grupp eller skapa en ny. Med åtgärds grupper kan du skicka utföra ett antal åtgärder, till exempel skicka e-post (e-post), skicka SMS (s), anropa Webhooks (s), åtgärda det med hjälp av Azure Runbooks, push to ITSM-verktyget och mycket annat. Läs mer om [Åtgärds grupper](action-groups.md).

    > [!NOTE]
    > Se begränsningar för [Azure-prenumerationen](../../azure-resource-manager/management/azure-subscription-service-limits.md) för begränsningar för de åtgärder som kan utföras.  

    Vissa ytterligare funktioner är tillgängliga för att åsidosätta standard åtgärderna:

    - E-postmeddelande: åsidosätter *e-* **postmeddelandets**ämne i e-postmeddelandet som skickas via åtgärds gruppen. Du kan inte ändra bröd texten i e-postmeddelandet och det här fältet är **inte** för e-postadress.
    - **Inkludera anpassad JSON-nyttolast**: åsidosätter webhook-JSON som används av åtgärds grupper som antar att åtgärds gruppen innehåller en webhook-typ. Mer information om webhook-format finns i [webhook-åtgärd för logg aviseringar](../../azure-monitor/platform/alerts-log-webhook.md). Alternativet Visa webhook har angetts för att kontrol lera formatet med hjälp av JSON-data från exempel.

        ![Åsidosättning av åtgärd för logg aviseringar](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Om alla fält är giltiga och med grön Ticket kan du klicka på knappen **skapa aviserings regel** och en avisering skapas i Azure Monitor-aviseringar. Alla aviseringar kan visas från instrument panelen för aviseringar.

     ![Skapa regel](media/alerts-log/AlertsPreviewCreate.png)

     Inom några minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

Användarna kan också slutföra sin Analytics-fråga i [Log Analytics](../log-query/portals.md) och sedan push-överföra den för att skapa en avisering via knappen "Ange avisering". därefter följer du anvisningarna i steg 6 och senare i självstudierna ovan.

 ![Log Analytics-Ställ in avisering](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Visa & hantera logg aviseringar i Azure Portal

1. I [portalen](https://portal.azure.com/)väljer du **övervaka** och under Monitor-avsnittet – väljer du **aviseringar**.

1. **Instrument panelen för aviseringar** visas – där alla Azure-aviseringar (inklusive logg aviseringar) visas på en fristående tavla. inklusive varje instans av när logg aviserings regeln har utlösts. Läs mer i [aviseringshantering](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Logg aviserings regler består av anpassad frågebaserade logik från användare och därmed utan löst tillstånd. På grund av vilken varje gång de villkor som anges i logg aviserings regeln uppfylls, utlöses.

1. Välj knappen **Hantera regler** i det översta fältet för att navigera till avsnittet regel hantering – där alla varnings regler har skapats visas. inklusive aviseringar som har inaktiverats.
    ![hantera aviserings regler](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Hantera logg aviseringar med Azure-resurs mal len

Logg aviseringar i Azure Monitor är kopplade till resurs typen `Microsoft.Insights/scheduledQueryRules/` . Mer information om den här resurs typen finns i [Azure Monitor API-referens för schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Logg aviseringar för Application Insights eller Log Analytics kan skapas med hjälp av [schemalagda FRÅGEREGLER API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Logg aviseringar för Log Analytics kan också hanteras med äldre [Log Analytics aviserings-API](api-alerts.md) och tidigare mallar för [Log Analytics sparade sökningar och aviseringar](../insights/solutions-resources-searches-alerts.md) . Mer information om hur du använder det nya ScheduledQueryRules-API: t som är detaljerat här som standard finns i [Växla till nytt API för Log Analytics aviseringar](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Exempel på hur du skapar en logg avisering med Azure-resurs mal len

Följande är strukturen för den mall som används för att [skapa schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) med hjälp av standard logg Sök frågan för [antal resultat typ logg avisering](alerts-unified-log.md#number-of-results-alert-rules), med exempel data uppsättning som variabler.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
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
    } ]
}

```

Exempel-JSON ovan kan sparas som (t. ex.) sampleScheduledQueryRule. JSON för denna genom gång och kan distribueras med hjälp av [Azure Resource Manager i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Logg avisering med kors resurs fråga med Azure-resurs mal len

Följande är strukturen för den mall som används för att [skapa schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) med hjälp av [loggs öknings frågor över olika resurser](../../azure-monitor/log-query/cross-workspace-query.md) av [typen logg avisering för mått mått typ](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules), med exempel data uppsättning som variabler.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
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
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> När du använder frågan över resurser i logg aviseringen är användningen av [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) obligatorisk och användaren måste ha åtkomst till listan över resurser som anges

Exempel-JSON ovan kan sparas som (t. ex.) sampleScheduledQueryRule. JSON för denna genom gång och kan distribueras med hjälp av [Azure Resource Manager i Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Hantera logg aviseringar med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

API för Azure Monitor [schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) är en REST API som är helt kompatibel med Azure Resource Manager REST API. Och PowerShell-cmdletarna som anges nedan är tillgängliga för att utnyttja [API: et för schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

- [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell-cmdlet för att skapa en ny logg aviserings regel.
- [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell-cmdleten för att uppdatera en befintlig logg aviserings regel.
- [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : PowerShell-cmdlet för att skapa eller uppdatera objekt som anger käll parametrar för en logg avisering. Används som inmatad av cmdleten [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) och [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): PowerShell-cmdlet för att skapa eller uppdatera objekt som anger schema parametrar för en logg avisering. Används som inmatad av cmdleten [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) och [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : PowerShell-cmdlet för att skapa eller uppdatera objekt som anger åtgärds parametrar för en logg avisering. Används som inmatad av cmdleten [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) och [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : PowerShell-cmdlet för att skapa eller uppdatera objekt som anger parametrar för åtgärds grupper för en logg avisering. Används som inmatad av [New-AzScheduledQueryRuleAlertingAction-](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdleten.
- [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : PowerShell-cmdleten för att skapa eller uppdatera objekt som anger Utlös ande villkors parametrar för logg avisering. Används som inmatad av [New-AzScheduledQueryRuleAlertingAction-](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdleten.
- [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : PowerShell-cmdlet för att skapa eller uppdatera objekt som anger mått för mått utlösare för måttet för mått [mått typ logg avisering](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Används som inmatad av [New-AzScheduledQueryRuleTriggerCondition-](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdleten.
- [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell-cmdleten för att visa en lista över befintliga logg aviserings regler eller en speciell logg aviserings regel
- [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : PowerShell-cmdlet för att aktivera eller inaktivera logg aviserings regel
- [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell-cmdlet för att ta bort en befintlig logg aviserings regel

> [!NOTE]
> ScheduledQueryRules PowerShell-cmdletar kan bara hantera regler som skapats av en cmdlet eller med hjälp av Azure Monitor- [schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Logg varnings regler som skapats med äldre [Log Analytics aviserings-API](api-alerts.md) och äldre mallar av [Log Analytics sparade sökningar och aviseringar](../insights/solutions-resources-searches-alerts.md) kan hanteras med hjälp av ScheduledQueryRules PowerShell-cmdletar när användaren [växlar API-inställningar för Log Analytics aviseringar](alerts-log-api-switch.md).

Illustrera härnäst är stegen för att skapa en exempel logg aviserings regel med hjälp av scheduledQueryRules PowerShell-cmdletar.

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Hantera logg aviseringar med CLI eller API

API för Azure Monitor [schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) är en REST API som är helt kompatibel med Azure Resource Manager REST API. Därför kan den användas via PowerShell med Resource Manager-kommandon för Azure CLI.


> [!NOTE]
> Logg aviseringar för Log Analytics kan också hanteras med äldre [Log Analytics aviserings-API](api-alerts.md) och tidigare mallar för [Log Analytics sparade sökningar och aviseringar](../insights/solutions-resources-searches-alerts.md) . Mer information om hur du använder det nya ScheduledQueryRules-API: t som är detaljerat här som standard finns i [Växla till nytt API för Log Analytics aviseringar](alerts-log-api-switch.md).

Det finns för närvarande inga dedikerade CLI-kommandon för logg aviseringar. men som illustreras nedan kan användas via kommandot Azure Resource Manager CLI för exempel resurs mal len som visas tidigare (sampleScheduledQueryRule. JSON) i avsnittet resurs mal len:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Vid lyckad åtgärd kommer 201 att returneras till tillstånd nya aviserings regel skapande eller 200 kommer att returneras om en befintlig varnings regel har ändrats.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [logg aviseringar i Azure-aviseringar](../../azure-monitor/platform/alerts-unified-log.md)
* Förstå [webhook-åtgärder för logg aviseringar](../../azure-monitor/platform/alerts-log-webhook.md)
* Läs mer om [Application Insights](../../azure-monitor/app/analytics.md)
* Läs mer om [logg frågor](../log-query/log-query-overview.md).
