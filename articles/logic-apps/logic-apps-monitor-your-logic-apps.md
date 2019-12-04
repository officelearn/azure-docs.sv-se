---
title: Kontrol lera status, Konfigurera loggning och hämta aviseringar
description: Övervaka status, logg diagnostikdata och konfigurera aviseringar för Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/21/2017
ms.openlocfilehash: ca1e2836ddfb38de640a5a90cf53e416b8923c04
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791909"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Övervaka status, konfigurera diagnostikloggning och aktivera aviseringar för Azure Logic Apps

När du har [skapat och kört en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) kan du kontrollera logikappens körningshistorik, utlösarhistorik, status och prestanda. För händelse övervakning i real tid och bättre fel sökning, ställer du in [diagnostikloggning](#azure-diagnostics) för din Logic app. På så sätt kan du [hitta och visa händelser](#find-events), t.ex. utlösarhändelser, körningshändelser och åtgärdshändelser. Du kan också använda dessa [diagnostikdata med andra tjänster](#extend-diagnostic-data), t.ex. Azure Storage och Azure Event Hubs. 

Konfigurera [aviseringar](#add-azure-alerts)om du vill få meddelanden om fel eller andra möjliga problem. Du kan till exempel skapa en avisering som identifierar "när fler än fem körningar går sönder om en timme." Du kan också konfigurera övervakning, spårning och loggning program mässigt genom att använda [Azure-diagnostik händelse inställningar och egenskaper](#diagnostic-event-properties).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Visa körnings-och utlösnings historik för din Logic app

1. Om du vill hitta din Logi Kap par i [Azure Portal](https://portal.azure.com)väljer du **alla tjänster**på huvud menyn i Azure. Skriv "Logic Apps" i sökrutan och välj **Logic Apps**.

   ![Hitta din Logic app](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   I Azure Portal visas alla Logic Apps som är associerade med din Azure-prenumeration. 

2. Välj din Logic app och välj sedan **Översikt**.

   Azure Portal visar historiken för körningarna och utlösaren för din Logic app. Exempel:

   ![Logic app kör historik och utlösnings historik](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Kör historik** visar alla körningar för din Logic app. 
   * **Utlösnings historik** visar all Utlösar-aktivitet för din Logic app.

   Information om status beskrivningar finns i [Felsöka din Logic app](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Om du inte hittar de data du förväntar dig väljer du **Uppdatera**i verktygsfältet.

3. Om du vill visa stegen från en speciell körning väljer du den i kör **Historik**. 

   I vyn övervaka visas varje steg i som körs. Exempel:

   ![Åtgärder för en speciell körning](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Om du vill ha mer information om körningen väljer du **Kör information**. Den här informationen sammanfattar stegen, status, indata och utdata för körningen. 

   ![Välj Kör information](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Du kan till exempel hämta **korrelations-ID**: t för körning, som du kan behöva när du använder [REST API för Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Om du vill ha mer information om ett speciellt steg väljer du det steget. Du kan nu granska information som indata, utdata och eventuella fel som har inträffat för det steget. Exempel:

   ![Steg information](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > All körnings information och händelser krypteras i Logic Appss tjänsten. De dekrypteras bara när en användare begär att visa dessa data. Du kan också kontrol lera åtkomsten till dessa händelser med [hjälp av RBAC (Azure rollbaserad Access Control)](../role-based-access-control/overview.md).

6. Om du vill ha information om en angiven Utlös ande händelse går du tillbaka till **översikts** fönstret. Under **utlösnings historik**väljer du händelsen Utlös. Du kan nu granska information som indata och utdata, till exempel:

   ![Utlöser information om utdata för händelse](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Aktivera diagnostikloggning för din Logic app

För bättre fel sökning med körnings information och händelser kan du konfigurera diagnostikloggning med [Azure Monitor loggar](../log-analytics/log-analytics-overview.md). Azure Monitor är en tjänst i Azure som övervakar molnet och lokala miljöer för att hjälpa dig att underhålla deras tillgänglighet och prestanda. 

Innan du börjar måste du ha en Log Analytics-arbetsyta. Lär dig [hur du skapar en arbets yta för Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. Leta upp och välj din Logic app i [Azure Portal](https://portal.azure.com). 

2. Välj **diagnostik** > **diagnostikinställningar**under **övervakning**på bladet Logic app-blad.

   ![Gå till övervakning, diagnostik, diagnostikinställningar](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. Under **diagnostikinställningar**väljer du **på**.

   ![Aktivera diagnostikloggar](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Välj nu Log Analytics arbets yta och händelse kategori för loggning på det sätt som visas:

   1. Välj **Skicka till Log Analytics**. 
   2. Under **Log Analytics**väljer du **Konfigurera**. 
   3. Under **OMS-arbetsytor**väljer du den arbets yta som ska användas för loggning.
      > [!NOTE]
      > OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.
   4. Under **logg**väljer du kategorin **WorkflowRuntime** .
   5. Välj mått intervallet.
   6. När du är klar väljer du **Spara**.

   ![Välj Log Analytics arbets yta och data för loggning](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Nu kan du hitta händelser och andra data för utlösnings händelser, köra händelser och åtgärds händelser.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Hitta händelser och data för din Logic app

Följ dessa steg om du vill söka efter och Visa händelser i din Logic app, t. ex. utlösa händelser, köra händelser och åtgärds händelser.

1. I [Azure Portal](https://portal.azure.com)väljer du **alla tjänster**. Sök efter "Log Analytics" och välj sedan **Log Analytics** som visas här:

   ![Välj "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. Under **Log Analytics**, leta upp och välj arbets ytan Log Analytics. 

   ![Välj din Log Analytics arbets yta](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. Under **hantering**väljer du **loggs ökning**.

   ![Välj loggs ökning](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. I rutan Sök anger du ett fält som du vill söka efter och trycker på **RETUR**. När du börjar skriva kan du se möjliga matchningar och åtgärder som du kan använda. 

   Om du till exempel vill hitta de 10 viktigaste händelserna som hände, anger och väljer du den här Sök frågan: **Sök kategori = = "WorkflowRuntime" | Limit 10**

   ![Ange Sök sträng](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Lär dig mer om [hur du hittar data i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md).

5. På sidan resultat i det vänstra fältet väljer du den tidsram som du vill visa.
Om du vill förfina frågan genom att lägga till ett filter väljer du **+ Lägg till**.

   ![Välj tidsram för frågeresultat](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. Under **Lägg till filter**anger du filter namnet så att du kan hitta det filter som du vill använda. Välj filtret och välj **+ Lägg till**.

   I det här exemplet används ordet "status" för att hitta misslyckade händelser under **AzureDiagnostics**.
   Här är filtret för **status_s** redan valt.

   ![Välj filter](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. I det vänstra fältet väljer du det filter värde som du vill använda och väljer **Använd**.

   ![Välj filter värde, Välj Använd](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Gå tillbaka till frågan som du skapar. Din fråga har uppdaterats med det valda filtret och värdet. Dina tidigare resultat har nu filtrerats.

   ![Återgå till din fråga med filtrerade resultat](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Välj **Spara**om du vill spara din fråga för framtida bruk. Lär dig [hur du sparar din fråga](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Utöka hur och var du använder diagnostikdata med andra tjänster

Tillsammans med Azure Monitor loggar kan du utöka hur du använder dina Logic Apps diagnostikdata med andra Azure-tjänster, till exempel: 

* [Arkivera Azure-diagnostik loggar i Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Strömma Azure-diagnostik loggar till Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Du kan sedan få real tids övervakning genom att använda telemetri och analys från andra tjänster, t. ex. [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och [Power BI](../azure-monitor/platform/powerbi.md). Exempel:

* [Strömma data från Event Hubs till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysera strömmande data med Stream Analytics och skapa en instrument panel för real tids analys i Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Kontrol lera att du först [skapar ett Azure Storage-konto](../storage/common/storage-create-storage-account.md) eller [skapa en Azure Event Hub](../event-hubs/event-hubs-create.md)baserat på de alternativ som du vill konfigurera. Välj sedan de alternativ för vilka du vill skicka diagnostikdata:

![Skicka data till Azure Storage-konto eller händelsehubben](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Kvarhållning gäller endast när du väljer att använda ett lagrings konto.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Konfigurera aviseringar för din Logic app

Konfigurera [aviseringar i Azure](../azure-monitor/platform/alerts-overview.md)om du vill övervaka vissa mått eller överskrida tröskelvärden för din Logic app. Lär dig mer om [mått i Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Följ dessa steg om du vill konfigurera aviseringar utan [Azure Monitor loggar](../log-analytics/log-analytics-overview.md). Mer avancerade aviserings kriterier och åtgärder får du genom att konfigurera [Azure Monitor loggar](#azure-diagnostics) .

1. Välj **diagnostik** > **varnings regler** > **Lägg till avisering** som visas här under **övervakning**i bladet Logic app-bladet.

   ![Lägg till en avisering för din Logic app](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. På bladet **Lägg till en varnings regel** skapar du aviseringen som visas:

   1. Under **resurs**väljer du din Logi Kap par, om du inte redan har gjort det. 
   2. Ange ett namn och en beskrivning för aviseringen.
   3. Välj ett **mått** eller en händelse som du vill spåra.
   4. Välj ett **villkor**, ange ett **tröskelvärde** för måttet och välj **period** för övervakning av det här måttet.
   5. Välj om du vill skicka e-post för aviseringen. 
   6. Ange andra e-postadresser för att skicka aviseringen. 
   Du kan också ange en webhook-URL där du vill skicka aviseringen.

   Den här regeln skickar till exempel en avisering när fem eller fler körningar fungerar under en timme:

   ![Skapa mått varnings regel](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Om du vill köra en Logi Kap par från en avisering kan du ta med [utlösaren för begäran](../connectors/connectors-native-reqres.md) i ditt arbets flöde, där du kan utföra uppgifter som exempel:
> 
> * [Publicera till slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Skicka en text](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Lägga till ett meddelande i en kö](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Azure-diagnostik händelse inställningar och information

Varje diagnostisk händelse innehåller information om din Logic app och händelsen, till exempel status, start tid, slut tid och så vidare. Om du vill konfigurera övervakning, spårning och loggning program mässigt kan du använda den här informationen med [REST API för Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) och [REST API för Azure-diagnostik](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows).

`ActionCompleted` händelsen har till exempel `clientTrackingId`-och `trackedProperties` egenskaper som du kan använda för att spåra och övervaka:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: om inget annat anges genererar Azure automatiskt detta ID och korrelerar händelser i en Logic app-körning, inklusive eventuella kapslade arbets flöden som anropas från Logic app. Du kan manuellt ange detta ID från en utlösare genom att skicka ett `x-ms-client-tracking-id` huvud med ditt anpassade ID-värde i trigger-begäran. Du kan använda en begär ande utlösare, HTTP-utlösare eller webhook-utlösare.

* `trackedProperties`: om du vill spåra indata eller utdata i diagnostikdata kan du lägga till spårade egenskaper till åtgärder i din Logic Apps JSON-definition. Spårade egenskaper kan bara spåra en enda åtgärds indata och utdata, men du kan använda `correlation` egenskaper för händelser för att korrelera mellan åtgärder i en körning.

  Om du vill spåra en eller flera egenskaper lägger du till avsnittet `trackedProperties` och de egenskaper som du vill använda i åtgärds definitionen. Anta till exempel att du vill spåra data som "order-ID" i din telemetri:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```
  Här är ett annat exempel som använder åtgärden **initiera variabel** . Exemplet lägger till spårade egenskaper från åtgärdens indata där indata är en matris, inte en post.  

  ``` json
  "actions": { 
   "Initialize_variable": { 
      "inputs": { 
         "variables": [{ 
            "name": "ConnectorName", 
            "type": "String", 
            "value": "SFTP-SSH" 
         }]
      },
      "runAfter": {},
      "trackedProperties": { 
         "Track1": "@action().inputs.variables[0].value"
      },
      "type": "InitializeVariable"
   } 
  }
  ```

## <a name="next-steps"></a>Nästa steg

* [Automatisera Logic app-distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [B2B-scenarier med Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Övervaka B2B-meddelanden](../logic-apps/logic-apps-monitor-b2b-message.md)
