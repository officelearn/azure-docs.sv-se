---
title: Skapa, visa och hantera log aviseringar med Azure Monitor
description: Använda Azure Monitor för att skapa, visa och hantera loggvarningsregler i Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: fd183b5776f30b12d577a94be147eabef05d258f
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419375"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Skapa, visa och hantera aviseringar med Azure Monitor  

## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in aviseringar med hjälp av gränssnittet aviseringar i Azure-portalen. Definitionen för en varningsregel är i tre delar:
- Mål: Specifika Azure-resursen, vilket är som ska övervakas
- Villkor: Specifika villkor eller logik som när synts signalen ska utlösa åtgärd
- Åtgärd: Specifika anrop som skickats till en mottagare av ett meddelande - e-post, SMS, webhook osv.

Termen **Loggaviseringar** att beskriva aviseringar där signalen är anpassad fråga baserat på [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Application Insights](../application-insights/app-insights-analytics.md). Mer information om funktioner, terminologi och typer från [Loggaviseringar - översikt](monitor-alerts-unified-log.md).

> [!NOTE]
> Populära loggdata från [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) finns nu även på plattformen mått i Azure Monitor. För information om vy [mått aviseringar för loggar](monitoring-metric-alerts-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Hantera aviseringar från Azure portal

Detaljerad nästa är stegvis guide till med hjälp av aviseringar i Azure portal-gränssnittet.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Skapa en loggvarningsregel med Azure portal
1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.  
    ![Övervakning](media/alert-log/AlertsPreviewMenu.png)

1. Välj den **ny Aviseringsregel** för att skapa en ny avisering i Azure.
    ![Lägg till avisering](media/alert-log/AlertsPreviewOption.png)

1. Skapa avisering avsnittet visas med tre delar som består av: *definiera aviseringsvillkoret*, *definiera Aviseringsinformationen*, och *definiera åtgärdsgruppen*.

    ![Skapa regel](media/alert-log/AlertsPreviewAdd.png)

1.  Definiera aviseringsvillkoren genom att använda den **Välj Resource** länk och specificera genom att välja en resurs. Filtret genom att välja den _prenumeration_, _resurstyp_, och nödvändiga _Resource_. 

    >[!NOTE]

    > För att skapa en logg varning - Kontrollera den **log** signal är tillgänglig för den valda resursen innan du fortsätter.
    ![Välj resurs](media/alert-log/Alert-SelectResourceLog.png)

 
1. *Loggaviseringar*: se till att **resurstyp** är en analytics-källa som *Log Analytics* eller *Programinsikter* och skicka en signal typ som **Log** , en gång sedan lämplig **resource** är valt, klickar du på *klar*. Sedan använda den **lägga till villkor** knappen för att visa en lista över signalen alternativ som är tillgängliga för resursen och från listan över signal **anpassade loggsökning** alternativ för den valda logga övervakningstjänst som *Log Analytics* eller *Programinsikter*.

   ![Välj en resurs - anpassade loggsökning](media/alert-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Aviseringar listor importera analytics-fråga som signaltyp - **Log (sparad fråga)** enligt ovan bild. Så att användarna kan förbättrar din fråga i Analytics och spara dem för framtida användning i aviseringar – mer information om hur du använder för att spara frågan som är tillgängliga på [med hjälp av loggsökning i log analytics](../log-analytics/log-analytics-log-searches.md) eller [delade fråga i application insights Analytics](../log-analytics/log-analytics-overview.md). 

1.  *Loggaviseringar*: när du valt, fråga aviseringar kan anges i **sökfråga** fältet; om frågesyntaxen är felaktigt fältet visar fel i rött. Om frågesyntaxen är korrekt - referens visas historiska data för den angivna frågan som ett diagram med alternativet för att justera tidsfönster från senaste sex timmar att förra veckan.

 ![Konfigurera varningsregeln](media/alert-log/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Historiska datavisualisering kan endast visas om resultatet av frågan har information om tid. Om din fråga resulterar i sammanfattade data eller specifika kolumnvärdena - visas samma som ett enda diagram.

    >  För måttet måttenhet för loggvarningar med Application insights kan du ange vilken specifik variabel för att gruppera data med hjälp av den **sammanställda på** alternativet; enligt nedan:

    ![Aggregera på alternativ](media/alert-log/aggregate-on.png)

1.  *Loggaviseringar*: med visualisering på plats, **Alert Logic** kan väljas från visas alternativen för villkor, sammanställning och slutligen tröskelvärdet. Ange sedan i logik, tiden för att utvärdera för det angivna villkoret med **Period** alternativet. Tillsammans med hur ofta avisering ska köras genom att välja **frekvens**.
För **Loggaviseringar** aviseringar kan baseras på:
   - *Antalet poster*: en avisering skapas om antalet poster som returneras av frågan är större än eller mindre än det angivna värdet.
   - *Metrisk måttenhet*: en avisering skapas om varje *aggregera värdet* överskrider tröskelvärdet som angetts i resultatet och det är *grupperade efter* valt värde. Antalet överträdelser för en avisering är antalet gånger som tröskelvärdet överstigs under den valda tidsperioden. Du kan ange Totalt antal överträdelser för valfri kombination av intrång i resultatuppsättningen eller efterföljande överträdelser att kräva att överträdelserna måste ske i efterföljande prover. Läs mer om [Loggaviseringar och deras typer](monitor-alerts-unified-log.md).


1. Som det andra steget definierar du ett namn för aviseringen i den **varningsregelns namn** fältet tillsammans med en **beskrivning** med informationen för för aviseringen och **allvarlighetsgrad** värdet från den alternativen. Dessa uppgifter återanvänds i alla aviseringsmeddelanden, meddelanden eller push som görs av Azure Monitor. Dessutom kan användaren välja att aktivera varningsregeln skapats omedelbart genom att klicka på rätt sätt **aktivera regeln vid skapande** alternativet.

    För **Loggaviseringar** endast vissa ytterligare funktioner finns i varningsinformationen:

    - **Visa inga aviseringar**: när du aktiverar Undertryckning för regeln, åtgärder för regeln är inaktiverade för en definierad tidsperiod när du har skapat en ny avisering. Regeln körs fortfarande och skapar aviseringsposter förutsatt att följande villkor är uppfyllda. Så att du har tid att åtgärda problemet utan att köra duplicerade åtgärder.

        ![Visa inga aviseringar för Loggaviseringar](media/alert-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Ange en Ignorera varning värde större än frekvensen för aviseringen för att se till att meddelanden stoppas utan överlappningar

1. Som det tredje och sista steget, anger eventuella **åtgärdsgrupp** måste aktiveras för regeln när varningsvillkor är uppfyllt. Du kan välja en befintlig åtgärdsgrupp med avisering eller skapa en ny åtgärdsgrupp. Enligt valde åtgärdsgrupp när aviseringen är utlösaren Azure kommer: skicka email(s), skicka SMS(s), anropa webhooks, åtgärda problemet med hjälp av Azure-Runbooks, push till ditt ITSM-verktyg, osv. Läs mer om [åtgärdsgrupper](monitoring-action-groups.md).

    > [!NOTE]
    > Referera till den [Azure-prenumerationstjänsten](../azure-subscription-service-limits.md) för gränser för Runbook-nyttolaster för aviseringar via Azure åtgärdsgrupper

    För **Loggaviseringar** vissa ytterligare funktioner är tillgänglig för att åsidosätta standardåtgärder:

    - **E-postavisering**: åsidosätter *e-postämne* i e-post, skickas via åtgärdsgrupp; om en eller flera e poståtgärder finns i dessa åtgärdsgruppen. Du kan inte ändra innehållet i e-postmeddelandet och det här fältet är **inte** för e-postadress.
    - **Inkludera anpassad Json-nyttolast**: åsidosätter webhooken JSON som används av åtgärdsgrupper; om en eller flera webhook-åtgärder finns i dessa åtgärdsgruppen. Användaren kan ange formatet för JSON som ska användas för alla webhookar som konfigurerats i åtgärdsgruppen associerade; Mer information om webhook-format finns i [webhook-åtgärd för Loggaviseringar](monitor-alerts-unified-log-webhook.md). Visa Webhook-alternativet har angetts för att kontrollera format med hjälp av JSON-exempeldata.

        ![Åtgärden åsidosättningar för Loggaviseringar](media/alert-log/AlertsPreviewOverrideLog.png)


1. Om alla fält är giltiga och med grön skalstreck den **skapa aviseringsregel** Klicka på knappen och en avisering skapas i Azure Monitor - aviseringar. Alla aviseringar kan visas från aviseringarna instrumentpanelen.

    ![Skapa en regel](media/alert-log/AlertsPreviewCreate.png)

    Inom några minuter, aviseringen är aktiv och utlöser som det beskrivits.

Användarna kan också underkänts analytics-fråga i [loggar analyssidan i Azure-portalen](../log-analytics/log-analytics-log-search-portals.md#log-analytics-page
) push-överföra den att skapa en avisering genom att ställa in avisering knappen - och följa anvisningarna steg 6 och senare i självstudien ovan.

 ![Log Analytics – Ställ in en avisering](media/alert-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Visa och hantera aviseringar i Azure-portalen

1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.  

1. Den **aviseringar instrumentpanelen** visas - där alla Azure-aviseringar (inklusive loggaviseringar) visas i en enda panel, inklusive varje instans av när loggen varningsregel har utlösts. Mer information finns i [aviseringshantering](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Loggvarningsregler som består av anpassad frågebaserade logik som tillhandahålls av användare och därför utan en löst. På grund av som varje gång de villkor som anges i loggvarningsregel uppfylls, den utlöses. 


1. Välj den **hantera regler** knappen i det översta fältet att navigera till regeln hantering – där alla aviseringsregler skapade räknas, inklusive aviseringar som har inaktiverats.
    ![ Hantera Varningsregler](media/alert-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Hantera aviseringar med Azure Resource-mall
För närvarande baserat log aviseringar kan skapas med hjälp av två olika Resursmallar, på vilka analysplattform aviseringen är ska baseras på (det vill säga) Log Analytics eller Application Insights.

Därför i avsnittet nedan innehåller information om hur du använder Resource-mall för Loggaviseringar för varje plattform för dataanalys.

### <a name="azure-resource-template-for-log-analytics"></a>Azure-resursmall för Log Analytics
Loggaviseringar för Log Analytics skapas från Varningsregler som kör en sparad sökning med regelbundna intervall. Om resultatet av frågan matchar de angivna villkoren, skapas en aviseringspost och en eller flera åtgärder körs. 

Resursmall för sökning och Log analytics-aviseringar den sparade Log analytics är tillgängliga i Log Analytics i dokumentationen för. Mer information finns i, [att lägga till Log Analytics sparade sökningar och aviseringar](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md); som innehåller färgkodats exempel samt information om schemat.

### <a name="azure-resource-template-for-application-insights"></a>Azure-resursmall för Application Insights
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


## <a name="managing-log-alerts-using-powershell-cli-or-api"></a>Hantera aviseringar med PowerShell, CLI eller API
Log aviseringar kan skapas baserat med hjälp av två olika Resource Manager kompatibla API: er, för närvarande på vilka analysplattform aviseringen är ska baseras på (det vill säga) Log Analytics eller Application Insights.

I avsnittet nedan innehåller därför information med API: et via Powershell eller CLI för Loggaviseringar för varje plattform för dataanalys.

### <a name="powershell-cli-or-api-for-log-analytics"></a>PowerShell, CLI eller API för Log Analytics
Log Analytics avisering REST API är RESTful och kan nås via Azure Resource Manager REST API. API: et därför kan nås från en PowerShell-kommandorad och kommer att bli sökresultat till dig i JSON-format, så att du kan använda resultaten i många olika sätt programmässigt.

Läs mer om [skapa och hantera Varningsregler i Log Analytics med REST API](../log-analytics/log-analytics-api-alerts.md), inklusive exempel för att komma åt API: et från Powershell.

### <a name="powershell-cli-or-api-for-application-insights"></a>PowerShell, CLI eller API för Application Insights
[Azure Monitor - schemalagda Frågeregler API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) är en REST-API och helt kompatibla med Azure Resource Manager REST API. Därför kan den användas via Powershell med Resource Manager-cmdlet som Azure CLI.

Bilden nedan användning via Azure Resource Managers PowerShell-cmdlet för exemplet resursmall som visades tidigare (sampleScheduledQueryRule.json) i den [Resource mallavsnittet](#azure-resource-template-for-application-insights) :
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Bilden nedan användning via Azure Resource Manager-kommando i Azure CLI för exemplet resursmall som visades tidigare (sampleScheduledQueryRule.json) i den [Resource mallavsnittet](#azure-resource-template-for-application-insights) :

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Lyckade åtgärder 201 återgår till tillståndet skapandet av ny regel för varning eller 200 returneras om en befintlig varningsregel ändrades.


  
## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Loggaviseringar i Azure-aviseringar](monitor-alerts-unified-log.md)
* Förstå [Webhook-åtgärder för loggaviseringar](monitor-alerts-unified-log-webhook.md)
* Läs mer om [Application Insights](../application-insights/app-insights-analytics.md)
* Läs mer om [Log Analytics](../log-analytics/log-analytics-overview.md). 

