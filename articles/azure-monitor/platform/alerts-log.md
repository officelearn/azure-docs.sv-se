---
title: Skapa, visa och hantera loggaviseringar med Azure Monitor | Microsoft-dokument
description: Använd Azure Monitor för att skapa, visa och hantera loggaviseringsregler i Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 96b1bd86576f8cf34428eb60e2d3f476312311c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249430"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Skapa, visa och hantera loggaviseringar med Azure Monitor

## <a name="overview"></a>Översikt
Den här artikeln visar hur du konfigurerar loggaviseringar med hjälp av aviseringsgränssnittet i Azure-portalen. Definitionen av en varningsregel finns i tre delar:
- Mål: Specifik Azure-resurs som ska övervakas
- Villkor: Specifikt villkor eller logik som när de ses i Signal, bör utlösa åtgärder
- Åtgärd: Specifikt samtal skickas till en mottagare av ett meddelande - e-post, SMS, webhook etc.

Termen **Loggaviseringar** för att beskriva aviseringar där signalen är loggfråga i en [Log Analytics-arbetsyta](../learn/tutorial-viewdata.md) eller [Application Insights](../app/analytics.md). Läs mer om funktioner, terminologi och typer från [Loggaviseringar - Översikt](alerts-unified-log.md).

> [!NOTE]
> Populära loggdata från [en Log Analytics-arbetsyta](../../azure-monitor/learn/tutorial-viewdata.md) är nu också tillgänglig på måttplattformen i Azure Monitor. För mer informationsvy, [Måttavisering för loggar](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Hantera loggaviseringar från Azure-portalen

Detaljerad nästa är steg-för-steg-guide till att använda loggaviseringar med hjälp av Azure Portal-gränssnittet.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Skapa en loggaviseringsregel med Azure-portalen

1. I [portalen](https://portal.azure.com/)väljer du **Övervaka** och under avsnittet MONITOR - välj **Aviseringar**.

    ![Övervakning](media/alerts-log/AlertsPreviewMenu.png)

1. Välj knappen **Ny varningsregel** om du vill skapa en ny avisering i Azure.

    ![Lägg till avisering](media/alerts-log/AlertsPreviewOption.png)

1. Avsnittet Skapa avisering visas med de tre delar som består av: *Definiera varningsvillkor*, *Definiera aviseringsinformation*och *Definiera åtgärdsgrupp*.

    ![Skapa regel](media/alerts-log/AlertsPreviewAdd.png)

1. Definiera aviseringsvillkoret med hjälp av länken **Välj resurs** och ange målet genom att välja en resurs. Filtrera genom att välja _prenumeration,_ _resurstyp_och nödvändig _resurs_.

   > [!NOTE]
   > För att skapa en loggavisering - kontrollera **att loggsignalen** är tillgänglig för den valda resursen innan du fortsätter.
   >  ![Välj resurs](media/alerts-log/Alert-SelectResourceLog.png)

1. *Loggvarningar:* Se till att **resurstyp** är en analyskälla som *Log Analytics* eller *Application Insights* och signaltyp som **Log**och klicka sedan på *Klar*när rätt **resurs** har valts . Använd sedan knappen **Lägg till villkor** för att visa en lista över signalalternativ som är tillgängliga för resursen och från alternativet för anpassad **loggsökning** i signallistan för vald loggövervakartjänst som *Log Analytics* eller *Application Insights*.

   ![Välj en resurs - anpassad loggsökning](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Varningslistor kan importera analytics-fråga som signaltyp - **Log (Sparad fråga),** som visas i bilden ovan. Så att användarna kan göra din fråga perfekt i Analytics och sedan spara dem för framtida användning i aviseringar - mer information om hur du använder spara fråga tillgänglig på [med hjälp av loggfråga i Azure Monitor](../log-query/log-query-overview.md) eller delad fråga i [programinsiktsanalys](../app/app-insights-overview.md).

1. *Loggaviseringar*: När du har valt kan fråga för aviseringar anges i fältet **Sökfråga.** Om frågesyntaxen är felaktig visas fel i RÖTT i fältet. Om frågesyntaxen är korrekt - För referens historiska data för den angivna frågan visas som ett diagram med möjlighet att justera tidsfönstret från senaste sex timmar till förra veckan.

    ![Konfigurera varningsregel](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Historisk datavisualisering kan bara visas om frågeresultaten har tidsinformation. Om frågan resulterar i summerade data eller specifika kolumnvärden visas samma som en enskild ritplan.
   > För måttmåttstypen loggaviseringar med hjälp av Application Insights eller [växlade till nytt API](alerts-log-api-switch.md)kan du ange vilken specifik variabel som data ska grupperas med alternativet **Aggregera på.** som visas nedan:
   > 
   > ![aggregerat på option](media/alerts-log/aggregate-on.png)

1. *Loggvarningar*: Med visualiseringen på plats kan **varningslogik** väljas från visade alternativ för villkor, aggregering och slutligen tröskel. Slutligen ange i logiken, den tid att bedöma för det angivna villkoret, med hjälp av **period** alternativ. Tillsammans med hur ofta Alert ska köras genom att välja **Frekvens**. **Loggvarningar** kan baseras på:
    - [Antal poster:](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules)En avisering skapas om antalet poster som returneras av frågan antingen är större än eller mindre än det angivna värdet.
    - [Måttmått:](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)En avisering skapas om varje *aggregerat värde* i resultaten överskrider tröskelvärdet och det *grupperas efter* valt värde. Antalet överträdelser för en avisering är antalet gånger tröskelvärdet överskrids under den valda tidsperioden. Du kan ange totala överträdelser för en kombination av överträdelser över resultatuppsättningen eller på varandra följande överträdelser för att kräva att överträdelserna måste inträffa i på varandra följande exempel.


1. Som det andra steget definierar du ett namn för aviseringen i **fältet Aviseringsregelnamn** tillsammans med en **beskrivning** som beskriver detaljer för aviserings- och **allvarlighetsgraden** från de alternativ som anges. Dessa uppgifter återanvänds i alla aviseringar, meddelanden eller push som görs av Azure Monitor. Dessutom kan användaren välja att omedelbart aktivera varningsregeln när den skapas genom att växla aktivera regeln på rätt sätt när du **skapar.**

    Endast **för loggaviseringar** finns vissa ytterligare funktioner i Varningsinformation:

    - **Ignorera aviseringar:** När du aktiverar undertryck för varningsregeln inaktiveras åtgärder för regeln under en bestämd tidsperiod efter att du har skapat en ny avisering. Regeln körs fortfarande och skapar varningsposter förutsatt att villkoren är uppfyllda. Så att du tid att rätta till problemet utan att köra dubblettåtgärder.

        ![Ignorera aviseringar för loggaviseringar](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Ange ett utelämnat varningsvärde som är större än varningsfrekvensen för att säkerställa att meddelanden stoppas utan överlappning

1. Som det tredje och sista steget anger du om någon **åtgärdsgrupp** behöver utlösas för varningsregeln när varningsvillkoret uppfylls. Du kan välja en befintlig åtgärdsgrupp med avisering eller skapa en ny åtgärdsgrupp. Enligt valda Åtgärdsgruppen, när aviseringen är utlösande Azure kommer: skicka e-post( s), skicka SMS (s), ring Webhook (s), åtgärda med Hjälp av Azure Runbooks, tryck till ditt ITSM-verktyg, etc. Läs mer om [åtgärdsgrupper](action-groups.md).

    > [!NOTE]
    > Se [Azure-prenumerationstjänstgränserna](../../azure-resource-manager/management/azure-subscription-service-limits.md) för begränsningar för runbook-nyttolaster som utlöses för loggaviseringar via Azure-åtgärdsgrupper

    För **loggvarningar** finns vissa ytterligare funktioner tillgängliga för att åsidosätta standardåtgärderna:

    - **E-postmeddelande:** Åsidosätter *e-postämne* i e-postmeddelandet, skickas via Åtgärdsgrupp; om det finns en eller flera e-poståtgärder i den nämnda åtgärdsgruppen. Du kan inte ändra e-postmeddelandets brödtext och det här fältet är **inte** för e-postadress.
    - **Inkludera anpassad Json nyttolast:** Åsidosätter webhook JSON används av åtgärdsgrupper; om det finns en eller flera webhook-åtgärder i nämnda åtgärdsgrupp. Användaren kan ange formatet för JSON som ska användas för alla webhooks som konfigurerats i associerad åtgärdsgrupp. Mer information om webhook-format finns i [webhook-åtgärden för loggvarningar](../../azure-monitor/platform/alerts-log-webhook.md). Visa Webhook-alternativet finns för att kontrollera formatet med hjälp av exempel på JSON-data.

        ![Åtgärd åsidosättningar för loggaviseringar](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Om alla fält är giltiga och med grön bock kan knappen **skapa aviseringsregel** klickas och en avisering skapas i Azure Monitor - Aviseringar. Alla aviseringar kan visas från instrumentpanelen för aviseringar.

     ![Skapande av regler](media/alerts-log/AlertsPreviewCreate.png)

     Inom några minuter är aviseringen aktiv och utlöser som tidigare beskrivits.

Användare kan också slutföra sin analytics-fråga i [logganalys](../log-query/portals.md) och sedan trycka på den för att skapa en avisering via "Set Alert"-knappen - sedan följa instruktionerna från steg 6 och framåt i ovanstående handledning.

 ![Logganalys - Ange avisering](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Visa & hantera loggaviseringar i Azure Portal

1. I [portalen](https://portal.azure.com/)väljer du **Övervaka** och under avsnittet MONITOR - välj **Aviseringar**.

1. **Instrumentpanelen aviseringar** visas - där alla Azure-aviseringar (inklusive loggaviseringar) visas på en enda anslagstavla. inklusive varje instans av när loggvarningsregeln har avfyrats. Mer information finns i [Varningshantering](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Loggvarningsregler består av anpassad frågebaserad logik som tillhandahålls av användare och därmed utan ett löst tillstånd. På grund av vilka villkoren som anges i loggvarningsregeln är uppfyllda utlöses de varje gång de villkor som anges i loggvarningsregeln är uppfyllda.

1. Välj knappen **Hantera regler** i det övre fältet om du vill navigera till regelhanteringsavsnittet – där alla varningsregler som skapas visas. inklusive aviseringar som har inaktiverats.
    ![hantera varningsregler](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Hantera loggaviseringar med Azure Resource-mall

Loggaviseringar i Azure Monitor `Microsoft.Insights/scheduledQueryRules/`är associerade med resurstyp . Mer information om den här resurstypen finns i [Azure Monitor - API-referens för schemalagda frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Loggaviseringar för Programstatistik eller Logganalys kan skapas med [API för schemalagda frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Loggaviseringar för Log Analytics kan också hanteras med hjälp av äldre [Log Analytics Alert API](api-alerts.md) och äldre mallar för Log Analytics [sparade sökningar och varningar](../insights/solutions-resources-searches-alerts.md) också. Mer information om hur du använder det nya Api:et För Schemalagda Regler som beskrivs här som standard finns i [Växla till nytt API för logganalysvarningar](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Skapa exempelloggavisering med Azure Resource-mall

Följande är strukturen för [skapande av schemalagda frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) baserad resursmall med hjälp av standardloggsökfråga för [antal resultattyploggavisering](alerts-unified-log.md#number-of-results-alert-rules), med exempeldatauppsättning som variabler.

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

Exemplet json ovan kan sparas som (säg) sampleScheduledQueryRule.json i den här genomskräckningen och kan distribueras med [Azure Resource Manager i Azure-portalen](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Loggavisering med resursövergripande fråga med Azure Resource Template

Följande är strukturen för [skapande av schemalagda frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) baserad resursmall med hjälp av [sökfråga för flera resurser logg för](../../azure-monitor/log-query/cross-workspace-query.md) [måttmättyp,](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)med exempeldatauppsättning som variabler.

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
> När du använder resursövergripande fråga i loggavisering är användningen av [auktoriserade Resurser](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) obligatorisk och användaren måste ha tillgång till listan över angivna resurser

Exemplet json ovan kan sparas som (säg) sampleScheduledQueryRule.json i den här genomskräckningen och kan distribueras med [Azure Resource Manager i Azure-portalen](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Hantera loggvarningar med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor - [API för schemalagda frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) är ett REST API och är helt kompatibelt med AZURE Resource Manager REST API. Och PowerShell-cmdletar som anges nedan är tillgängliga för att utnyttja [API:et schemalagda frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : Powershell cmdlet för att skapa en ny loggaviseringsregel.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : Powershell cmdlet för att uppdatera en befintlig loggaviseringsregel.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : Powershell cmdlet för att skapa eller uppdatera objekt som anger källparametrar för en loggavisering. Används som indata från [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) och [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): Powershell cmdlet för att skapa eller uppdatera objekt som anger schemaparametrar för en loggavisering. Används som indata från [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) och [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : Powershell cmdlet för att skapa eller uppdatera objekt som anger åtgärdsparametrar för en loggavisering. Används som indata från [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) och [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : Powershell cmdlet för att skapa eller uppdatera objekt som anger åtgärdsgrupperparametrar för en loggavisering. Används som indata från [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdlet.
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : Powershell cmdlet för att skapa eller uppdatera objekt som anger utlösarvillkorsparametrar för loggavisering. Används som indata från [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdlet.
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : Powershell cmdlet för att skapa eller uppdatera objekt som anger måttutlösarevillkorsparametrar för [måttmätningstyploggavisering](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Används som indata från [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdlet.
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : Powershell cmdlet för att lista befintliga loggvarningsregler eller en specifik loggvarningsregel
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : Powershell cmdlet för att aktivera eller inaktivera loggvarningsregel
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): Powershell cmdlet för att ta bort en befintlig loggvarningsregel

> [!NOTE]
> SchemalagdaqueryRules PowerShell-cmdletar kan bara hantera regler som skapats cmdlet själv eller med Hjälp av Azure Monitor - [Schemalagt frågeregler API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Loggvarningsregler som skapats med äldre [Log Analytics Alert API](api-alerts.md) och äldre mallar för sparade sökningar och aviseringar i Log Analytics kan [hanteras](../insights/solutions-resources-searches-alerts.md) med ScheduledQueryRules PowerShell-cmdletar först efter att användaren [har bytt API-inställningar för Logganalysvarningar](alerts-log-api-switch.md).

Illustrerad nästa är stegen för att skapa en exempelloggvarningsregel med hjälp av de schemalagdaQueryRules PowerShell-cmdlets.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Hantera loggaviseringar med CLI eller API

Azure Monitor - [API för schemalagda frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) är ett REST API och är helt kompatibelt med AZURE Resource Manager REST API. Därför kan den användas via Powershell med hjälp av Resource Manager-kommandon för Azure CLI.


> [!NOTE]
> Loggaviseringar för Log Analytics kan också hanteras med hjälp av äldre [Log Analytics Alert API](api-alerts.md) och äldre mallar för Log Analytics [sparade sökningar och varningar](../insights/solutions-resources-searches-alerts.md) också. Mer information om hur du använder det nya Api:et För Schemalagda Regler som beskrivs här som standard finns i [Växla till nytt API för logganalysvarningar](alerts-log-api-switch.md).

Loggvarningar har för närvarande inte dedikerade CLI-kommandon för närvarande. men som visas nedan kan användas via Azure Resource Manager CLI-kommandot för exempel resursmall som visas tidigare (sampleScheduledQueryRule.json) i avsnittet Resursmall:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Vid lyckad åtgärd returneras 201 till att skapa en ny varningsregel eller 200 returneras om en befintlig aviseringsregel har ändrats.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [loggaviseringar i Azure-aviseringar](../../azure-monitor/platform/alerts-unified-log.md)
* Förstå [Webhook-åtgärder för loggaviseringar](../../azure-monitor/platform/alerts-log-webhook.md)
* Läs mer om [Application Insights](../../azure-monitor/app/analytics.md)
* Läs mer om [loggfrågor](../log-query/log-query-overview.md).
