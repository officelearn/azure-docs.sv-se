---
title: Kontrollera status, ställa in loggning och få aviseringar – Azure Logic Apps | Microsoft Docs
description: Övervaka status, logga diagnostics-data och ställa in aviseringar för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: c2e487f421d2dfc875efde82c078f557f7bd03d2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405743"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Övervaka status, konfigurera diagnostikloggning och aktivera aviseringar för Azure Logic Apps

När du [skapar och kör en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), du kan kontrollera dess körningshistorik, utlösarhistorik, status och prestanda. För händelseövervakning och mer omfattande felsökning, ställa in [diagnostikloggning](#azure-diagnostics) för din logikapp. På så sätt kan du kan [hitta och visa händelser](#find-events), t.ex. utlösande händelser, kör händelser och händelser för åtgärden. Du kan också använda det här [diagnostics-data med andra tjänster](#extend-diagnostic-data), som Azure Storage och Azure Event Hubs. 

Om du vill få meddelanden om fel eller andra möjliga problem, Ställ in [aviseringar](#add-azure-alerts). Du kan till exempel skapa en avisering som identifierar ”när fler än fem körningar misslyckas på en timme”. Du kan också ställa in övervakning, spårning och loggning programmässigt med hjälp av [Azure Diagnostics händelse inställningar och egenskaper](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Visa körningar och utlösningshistorik för din logikapp

1. Du hittar din logikapp i den [Azure-portalen](https://portal.azure.com), på Azure-huvudmenyn väljer **alla tjänster**. Skriv ”logic apps” i sökrutan och välj **logikappar**.

   ![Hitta din logikapp](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure-portalen visar alla logikappar som är associerade med din Azure-prenumeration. 

2. Välj din logikapp och välj sedan **översikt**.

   Azure-portalen visar körnings- och utlösningshistorik för din logikapp. Exempel:

   ![Historik- och utlösningshistorik körningar med logikapp](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Körningshistorik** visas alla körningar för din logikapp. 
   * **Utlösningshistorik** visar alla aktiviteter för utlösaren för logikappen.

   För statusbeskrivningar finns i [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Om du inte hittar de data som du förväntar dig, i verktygsfältet väljer du **uppdatera**.

3. Visa stegen från en specifik körning under **Körningshistorik**, Välj som körs. 

   Övervaka innehåller varje steg i som körs. Exempel:

   ![Åtgärder för en specifik körning](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Om du vill ha mer information om körningen, Välj **körningsinformation**. Den här informationen sammanfattas steg, status, indata och utdata för körningen. 

   ![Välj ”körningsinformation”](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Du kan till exempel få kör **Korrelations-ID**, som du kan behöva när du använder den [REST API för Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Om du vill få information om ett specifikt steg, väljer du det steget. Nu kan du granska information som indata, utdata och eventuella fel som inträffade för det steget. Exempel:

   ![Information om steg](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Alla runtime samt händelser är krypterade i Logic Apps-tjänsten. De dekrypteras endast när en användare begär för att visa dessa data. Du kan också styra åtkomst till dessa händelser med [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md).

6. Om du vill få information om en specifik utlösarhändelse, gå tillbaka till den **översikt** fönstret. Under **Utlösningshistorik**, Välj utlösarhändelsen. Nu kan du granska information som indata och utdata, till exempel:

   ![Utlösaren händelseinformation för utdata](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Slå på diagnostik loggning för din logikapp

För bättre felsökning med runtime samt händelser, som du kan ställa in diagnostik loggning med [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics är en tjänst i Azure som övervakar dina molnbaserade och lokala miljöer för att hjälpa dig att bibehålla tillgänglighet och prestanda. 

Innan du börjar måste du har en Log Analytics-arbetsyta. Lär dig [hur du skapar en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md).

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj din logikapp. 

2. På bladet logikappmenyn under **övervakning**, Välj **diagnostik** > **diagnostikinställningar**.

   ![Gå till övervakning, diagnostik, diagnostikinställningar](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. Under **diagnostikinställningar**, Välj **på**.

   ![Aktivera diagnostikloggar](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Nu välja den Log Analytics-arbetsytan och händelsen kategorin för loggning som visas:

   1. Välj **skicka till Log Analytics**. 
   2. Under **Log Analytics**, Välj **konfigurera**. 
   3. Under **OMS-arbetsytor**, Välj arbetsytan som ska användas för loggning.
   > [!NOTE]
   > OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.
   4. Under **Log**väljer den **WorkflowRuntime** kategori.
   5. Välj det mått intervallet.
   6. När du är klar väljer du **Spara**.

   ![Välj Log Analytics-arbetsytan och data för loggning](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Nu kan hittar du händelser och andra data för utlösande händelser, händelser och händelser för åtgärden.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Hitta händelser och data för din logikapp

Som utlöser händelser, kör händelser, och åtgärden händelser, Följ stegen nedan för att hitta och visa händelser i din logikapp.

1. I den [Azure-portalen](https://portal.azure.com), Välj **alla tjänster**. Sök efter ”log analytics” och välj sedan **Log Analytics** som visas här:

   ![Välj ”Log Analytics”](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. Under **Log Analytics**, hitta och välj din Log Analytics-arbetsyta. 

   ![Välj Log Analytics-arbetsytan](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. Under **Management**, Välj **Loggsökning**.

   ![Välj ”Log Search”](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. I sökrutan anger du ett fält som du vill söka efter och tryck på **RETUR**. När du börjar skriva, visas möjliga matchningar och åtgärder som du kan använda. 

   Till exempel för att hitta de översta 10-händelser som har inträffat, ange och välj den här sökfrågan: **Sök kategori == ”WorkflowRuntime” | begränsa 10**

   ![Ange söksträngen](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Läs mer om [hitta data i Log Analytics](../log-analytics/log-analytics-log-searches.md).

5. Välj tidsram som du vill visa i fältet till vänster på resultatsidan.
Om du vill förfina frågan genom att lägga till ett filter, välja **+ Lägg till**.

   ![Välj tidsram för frågeresultat](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. Under **Lägg till filter**, ange namnet på filtret så att du kan hitta önskat filter. Välj filtret och välj **+ Lägg till**.

   Det här exemplet används ordet ”status” för att hitta misslyckade händelser under **AzureDiagnostics**.
   Här filtret för **status_s** har redan valts.

   ![Välj filter](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. I det vänstra fältet väljer du det filtervärde som du vill använda och välj **tillämpa**.

   ![Välj filtervärdet, välj ”Använd”](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Gå nu tillbaka till den fråga som du skapar. Frågan har uppdaterats med valda filter och värde. Dina tidigare resultat filtreras nu för.

   ![Gå tillbaka till din fråga med filtrerade resultat](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Om du vill spara frågan för framtida användning, Välj **spara**. Lär dig [hur du sparar din fråga](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Utöka hur och var du kan använda diagnostikdata med andra tjänster

Tillsammans med Azure Log Analytics kan utöka du hur du använda diagnostikdata för din logikapp med andra Azure-tjänster, till exempel: 

* [Arkivera Azure Diagnostics-loggar i Azure Storage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Stream Azure-diagnostikloggar för Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Du kan sedan hämta i realtid övervakningen med hjälp av telemetri och analyser från andra tjänster som [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och [Power BI](../log-analytics/log-analytics-powerbi.md). Exempel:

* [Stream-data från Event Hubs till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysera data med Stream Analytics och skapa en instrumentpanel för analys i realtid i Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Baserat på de alternativ som du vill konfigurera, ska du se till att du första [skapa ett Azure storage-konto](../storage/common/storage-create-storage-account.md) eller [skapar en Azure event hub](../event-hubs/event-hubs-create.md). Välj sedan alternativ för där du vill skicka diagnostikdata:

![Skicka data till Azure storage-konto eller event hub](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Lagringstider gäller endast när du väljer att använda ett lagringskonto.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Konfigurera aviseringar för din logikapp

Du kan övervaka specifika mått eller Överskriden tröskelvärden för din logikapp genom att ställa in [aviseringar i Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). Lär dig mer om [mått i Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Ställa in aviseringar utan [Azure Log Analytics](../log-analytics/log-analytics-overview.md), Följ stegen nedan. För mer avancerade villkor för aviseringar och åtgärder, [konfigurera Log Analytics](#azure-diagnostics) för.

1. På bladet logikappmenyn under **övervakning**, Välj **diagnostik** > **Aviseringsregler** > **Lägg till avisering**som visas här:

   ![Lägga till en avisering för din logikapp](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. På den **Lägg till en varningsregel** bladet skapa aviseringen som visas:

   1. Under **Resource**, Välj din logikapp, om inte redan är valt. 
   2. Ge ett namn och beskrivning för aviseringen.
   3. Välj en **mått** eller den händelse som du vill spåra.
   4. Välj en **villkor**, ange en **tröskelvärdet** för mått och välj den **Period** för övervakning av det här måttet.
   5. Välj om du vill skicka e-post för aviseringen. 
   6. Ange andra e-postadresser för att skicka aviseringen. 
   Du kan också ange en webhook-URL där du vill skicka aviseringen.

   Exempelvis kan den här regeln skickar en avisering när fem eller fler körningar misslyckas på en timme:

   ![Skapa måttaviseringsregel](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Om du vill köra en logikapp från en avisering, kan du inkludera den [begäransutlösare](../connectors/connectors-native-reqres.md) i arbetsflödet, vilket kan du utföra uppgifter, t.ex. de här exemplen:
> 
> * [Publicera till Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Skicka ett SMS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Lägg till ett meddelande till en kö](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Inställningar för Azure Diagnostics-händelser och information

Varje diagnostisk händelse har information om din logikapp och händelsen, till exempel status, starttid, Sluttid och så vidare. Om du vill konfigurera övervakning, spårning och loggning, kan du använda dessa uppgifter med de [REST API för Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) och [REST API för Azure Diagnostics](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

Till exempel den `ActionCompleted` händelsen har den `clientTrackingId` och `trackedProperties` egenskaper som du kan använda för att spåra och övervaka:

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

* `clientTrackingId`: Om den inte anges så är Azure automatiskt genererar detta ID och korrelerar händelser i en logikapp-körningen, inklusive alla kapslade arbetsflöden som anropas från logikappen. Du kan ange detta ID från en utlösare manuellt genom att skicka en `x-ms-client-tracking-id` huvud med din anpassade ID-värdet i begärans-utlösare. Du kan använda en begäransutlösare, HTTP-utlösare eller webhook-utlösaren.

* `trackedProperties`: För att spåra indata eller utdata i diagnostics-data måste du lägga till spårade egenskaper till åtgärder i din logikapp JSON-definition. Spårade egenskaper kan spåra bara en enda åtgärd indata och utdata, men du kan använda den `correlation` egenskaper för händelse för korrelation mellan åtgärder i en körning.

  För att spåra en eller flera egenskaper måste du lägga till den `trackedProperties` avsnittet och egenskaper som du vill skapa en åtgärdsdefinition. Anta exempelvis att du vill spåra data som en ”order-ID” i din telemetri:

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

## <a name="next-steps"></a>Nästa steg

* [Skapa mallar för logic app-distribution och versionshantering](../logic-apps/logic-apps-create-deploy-template.md)
* [B2B-scenarier med Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Övervaka B2B-meddelanden](../logic-apps/logic-apps-monitor-b2b-message.md)
