---
title: Skapa, visa och hantera log aviseringar med Azure Monitor | Microsoft Docs
description: Använda Azure Monitor för att skapa, visa och hantera loggvarningsregler i Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: d3285a6b2aa09dd78bbb63c384bd1f65c17034ff
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006945"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Skapa, visa och hantera aviseringar med Azure Monitor

## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in aviseringar med hjälp av gränssnittet aviseringar i Azure-portalen. Definitionen för en varningsregel är i tre delar:
- Mål: Specifika Azure-resurs som ska övervakas
- Villkor: Specifika villkor eller logik som när synts signalen ska utlösa åtgärd
- Åtgärd: Specifika anrop som skickats till en mottagare av ett meddelande - e-post, SMS, webhook osv.

Termen **Loggaviseringar** att beskriva aviseringar där signalen är loggfråga i en [Log Analytics-arbetsyta](../learn/tutorial-viewdata.md) eller [Application Insights](../app/analytics.md). Mer information om funktioner, terminologi och typer från [Loggaviseringar - översikt](alerts-unified-log.md).

> [!NOTE]
> Populära loggdata från [Log Analytics-arbetsytan](../../azure-monitor/learn/tutorial-viewdata.md) finns nu även på plattformen mått i Azure Monitor. För information om vy [mått aviseringar för loggar](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Hantera aviseringar från Azure portal

Detaljerad nästa är stegvis guide till med hjälp av aviseringar i Azure portal-gränssnittet.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Skapa en loggvarningsregel med Azure portal
1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.

    ![Övervakning](media/alerts-log/AlertsPreviewMenu.png)

1. Välj den **ny Aviseringsregel** för att skapa en ny avisering i Azure.

    ![Lägg till avisering](media/alerts-log/AlertsPreviewOption.png)

1. Avsnittet skapa aviseringar visas med tre delar som består av: *Definiera aviseringsvillkoret*, *definiera Aviseringsinformationen*, och *definiera åtgärdsgruppen*.

    ![Skapa regel](media/alerts-log/AlertsPreviewAdd.png)

1. Definiera aviseringsvillkoren genom att använda den **Välj Resource** länk och specificera genom att välja en resurs. Filtret genom att välja den _prenumeration_, _resurstyp_, och nödvändiga _Resource_.

   > [!NOTE]
   > 
   > För att skapa en logg varning - Kontrollera den **log** signal är tillgänglig för den valda resursen innan du fortsätter.
   >  ![Välj resurs](media/alerts-log/Alert-SelectResourceLog.png)

1. *Loggaviseringar*: Se till att **resurstyp** är en analytics-källa som *Log Analytics* eller *Programinsikter* och skicka en signal typ som **Log**, och sedan en gång lämplig **resource** är valt, klickar du på *klar*. Sedan använda den **lägga till villkor** knappen för att visa en lista över signalen alternativ som är tillgängliga för resursen och från listan över signal **anpassade loggsökning** alternativ för den valda logga övervakningstjänst som *Log Analytics* eller *Programinsikter*.

   ![Välj en resurs - anpassade loggsökning](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Aviseringar listor importera analytics-fråga som signaltyp - **Log (sparad fråga)** enligt ovan bild. Så att användarna kan förbättrar din fråga i Analytics och spara dem för framtida användning i aviseringar – mer information om hur du använder för att spara frågan som är tillgängliga på [med loggfråga i Azure Monitor](../log-query/log-query-overview.md) eller [delade fråga i application insights analytics ](../log-query/log-query-overview.md).

1. *Loggaviseringar*: När du valt, fråga aviseringar kan anges i **sökfråga** fältet; om frågesyntaxen är felaktigt fältet visar fel i rött. Om frågesyntaxen är korrekt - referens visas historiska data för den angivna frågan som ett diagram med alternativet för att justera tidsfönster från senaste sex timmar att förra veckan.

    ![Konfigurera varningsregeln](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Historiska datavisualisering kan endast visas om resultatet av frågan har information om tid. Om din fråga resulterar i sammanfattade data eller specifika kolumnvärdena - visas samma som ett enda diagram.
   > För måttet mätning typ av loggvarningar med Application Insights eller [växlas till nya API: et](alerts-log-api-switch.md), du kan ange vilka specifika variabeln för att gruppera data med hjälp av den **sammanställda på** alternativet; enligt beskrivningen i nedan:
   > 
   > ![Aggregera på alternativ](media/alerts-log/aggregate-on.png)

1. *Loggaviseringar*: Med visualiseringen på plats, **Alert Logic** kan väljas från visas alternativen för villkor, sammanställning och slutligen tröskelvärdet. Ange sedan i logik, tiden för att utvärdera för det angivna villkoret med **Period** alternativet. Tillsammans med hur ofta avisering ska köras genom att välja **frekvens**. **Loggaviseringar** kan baseras på:
    - [Antalet poster](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): En avisering skapas om antalet poster som returneras av frågan är större än eller mindre än det angivna värdet.
    - [Metrisk måttenhet](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): En avisering skapas om varje *aggregera värdet* överskrider tröskelvärdet som angetts i resultatet och det är *grupperade efter* valt värde. Antalet överträdelser för en avisering är antalet gånger som tröskelvärdet överstigs under den valda tidsperioden. Du kan ange Totalt antal överträdelser för valfri kombination av intrång i resultatuppsättningen eller efterföljande överträdelser att kräva att överträdelserna måste ske i efterföljande prover.


1. Som det andra steget definierar du ett namn för aviseringen i den **varningsregelns namn** fältet tillsammans med en **beskrivning** med informationen för för aviseringen och **allvarlighetsgrad** värdet från den alternativen. Dessa uppgifter återanvänds i alla aviseringsmeddelanden, meddelanden eller push som görs av Azure Monitor. Dessutom kan användaren välja att aktivera varningsregeln skapats omedelbart genom att klicka på rätt sätt **aktivera regeln vid skapande** alternativet.

    För **Loggaviseringar** endast vissa ytterligare funktioner finns i varningsinformationen:

    - **Visa inga aviseringar**: När du aktiverar Undertryckning för regeln, inaktiveras åtgärderna för regeln för en definierad tidsperiod när du har skapat en ny avisering. Regeln körs fortfarande och skapar aviseringsposter förutsatt att följande villkor är uppfyllda. Så att du har tid att åtgärda problemet utan att köra duplicerade åtgärder.

        ![Visa inga aviseringar för Loggaviseringar](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Ange en Ignorera varning värde större än frekvensen för aviseringen för att se till att meddelanden stoppas utan överlappningar

1. Som det tredje och sista steget, anger eventuella **åtgärdsgrupp** måste aktiveras för regeln när varningsvillkor är uppfyllt. Du kan välja en befintlig åtgärdsgrupp med avisering eller skapa en ny åtgärdsgrupp. Enligt valde åtgärdsgrupp när aviseringen är utlösaren Azure kommer: skicka email(s), skicka SMS(s), anropa webhooks, åtgärda problemet med hjälp av Azure-Runbooks, push till ditt ITSM-verktyg, osv. Läs mer om [åtgärdsgrupper](action-groups.md).

    > [!NOTE]
    > Referera till den [Azure-prenumerationstjänsten](../../azure-subscription-service-limits.md) för gränser för Runbook-nyttolaster för aviseringar via Azure åtgärdsgrupper

    För **Loggaviseringar** vissa ytterligare funktioner är tillgänglig för att åsidosätta standardåtgärder:

    - **E-postavisering**: Åsidosätter *e-postämne* i e-post, skickas via åtgärdsgrupp; om en eller flera e poståtgärder finns i dessa åtgärdsgruppen. Du kan inte ändra innehållet i e-postmeddelandet och det här fältet är **inte** för e-postadress.
    - **Inkludera anpassad Json-nyttolast**: Åsidosätter webhooken JSON som används av åtgärdsgrupper; Om en eller flera webhook-åtgärder finns i dessa åtgärdsgruppen. Användaren kan ange formatet för JSON som ska användas för alla webhookar som konfigurerats i åtgärdsgruppen associerade; Mer information om webhook-format finns i [webhook-åtgärd för Loggaviseringar](../../azure-monitor/platform/alerts-log-webhook.md). Visa Webhook-alternativet har angetts för att kontrollera format med hjälp av JSON-exempeldata.

        ![Åtgärden åsidosättningar för Loggaviseringar](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Om alla fält är giltiga och med grön skalstreck den **skapa aviseringsregel** Klicka på knappen och en avisering skapas i Azure Monitor - aviseringar. Alla aviseringar kan visas från aviseringarna instrumentpanelen.

     ![Skapa en regel](media/alerts-log/AlertsPreviewCreate.png)

     Inom några minuter, aviseringen är aktiv och utlöser som det beskrivits.

Användarna kan också underkänts analytics-fråga i [logganalys](../log-query/portals.md) push-överföra den att skapa en avisering genom att ställa in avisering knappen - och följa anvisningarna steg 6 och senare i självstudien ovan.

 ![Log Analytics – Ställ in en avisering](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Visa och hantera aviseringar i Azure-portalen

1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.

1. Den **aviseringar instrumentpanelen** visas - där alla Azure-aviseringar (inklusive loggaviseringar) visas i en enda panel, inklusive varje instans av när loggen varningsregel har utlösts. Mer information finns i [aviseringshantering](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Loggvarningsregler som består av anpassad frågebaserade logik som tillhandahålls av användare och därför utan en löst. På grund av som varje gång de villkor som anges i loggvarningsregel uppfylls, den utlöses.

1. Välj den **hantera regler** knappen i det översta fältet att navigera till regeln hantering – där alla aviseringsregler skapade räknas, inklusive aviseringar som har inaktiverats.
    ![ Hantera Varningsregler](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Hantera aviseringar med Azure Resource-mall

Loggaviseringar i Azure Monitor är associerade med resurstyp `Microsoft.Insights/scheduledQueryRules/`. Mer information om den här resurstypen finns [Azure Monitor - schemalagda fråga regler API-referens](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Loggaviseringar för Application Insights eller Log Analytics, kan skapas med [schemalagda regler fråge-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Loggaviseringar för Log Analytics kan också hanteras med hjälp av äldre [Log Analytics-avisering API](api-alerts.md) och äldre mallar för [sparade sökningar och aviseringar i Log Analytics](../insights/solutions-resources-searches-alerts.md) samt. Mer information om hur du använder den nya ScheduledQueryRules API som beskrivs här som standard finns i [växla till nya API: et för Log Analytics-aviseringar](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Exempel på Log skapande av varning med Azure Resource-mall

Följer strukturen för [skapa en schemalagd Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) baserat resursmall med standardloggen sökfråga [antal resultat typ log avisering](alerts-unified-log.md#number-of-results-alert-rules), med exempeldatauppsättning som variabler.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
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
        "tags":{"[variables('alertTag')]": "Resource"},
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

> [!IMPORTANT]
> Taggfältet med dolda-länk till målresursen är obligatoriskt i användning av [schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) API-anrop eller resurs-mallen.

Exempel-json ovan kan sparas som (exempelvis) sampleScheduledQueryRule.json i den här genomgången och kan distribueras med hjälp av [Azure Resource Manager i Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Log avisering med mellan resurser fråga med Azure Resource-mall

Följer strukturen för [skapa en schemalagd Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) baserat resource mallen med hjälp av [mellan resurser logga sökfråga](../../azure-monitor/log-query/cross-workspace-query.md) av [metriska måttenheter typ log avisering](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules), med exempeldatauppsättning som variabler.

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
        "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
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
        "tags":{"[variables('alertTag')]": "Resource"},
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
> Taggfältet med dolda-länk till målresursen är obligatoriskt i användning av [schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) API-anrop eller resurs-mallen. När du använder frågan mellan resurser i loggen för avisering, användningen av [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) är obligatoriskt och användaren måste ha åtkomst till listan över resurser som anges

Exempel-json ovan kan sparas som (exempelvis) sampleScheduledQueryRule.json i den här genomgången och kan distribueras med hjälp av [Azure Resource Manager i Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell-cli-or-api"></a>Hantera aviseringar med PowerShell, CLI eller API

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor - schemalagda Frågeregler API] (https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) är en REST-API och helt kompatibla med Azure Resource Manager REST API. Därför kan den användas via Powershell med Resource Manager-cmdlet som Azure CLI.


> [!NOTE]
> Loggaviseringar för Log Analytics kan också hanteras med hjälp av äldre [Log Analytics-avisering API](api-alerts.md) och äldre mallar för [sparade sökningar och aviseringar i Log Analytics](../insights/solutions-resources-searches-alerts.md) samt. Mer information om hur du använder den nya ScheduledQueryRules API som beskrivs här som standard finns i [växla till nya API: et för Log Analytics-aviseringar](alerts-log-api-switch.md).

Aviseringar har för närvarande inte dedikerade PowerShell eller CLI-kommandon för närvarande; men som på bilden nedan kan användas via Azure Resource Managers PowerShell-cmdlet för exemplet resursmall som visades tidigare (sampleScheduledQueryRule.json) i avsnittet resursmall:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "contosoRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```

Bilden nedan användning via Azure Resource Manager-kommando i Azure CLI för exemplet resursmall som visades tidigare (sampleScheduledQueryRule.json) i avsnittet resursmall:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
On successful operation, 201 will be returned to state new alert rule creation or 200 will be returned if an existing alert rule was modified.

## Next steps

* Learn about [Log Alerts in Azure Alerts](../../azure-monitor/platform/alerts-unified-log.md)
* Understand [Webhook actions for log alerts](../../azure-monitor/platform/alerts-log-webhook.md)
* Learn more about [Application Insights](../../azure-monitor/app/analytics.md)
* Learn more about [log queries](../log-query/log-query-overview.md).
