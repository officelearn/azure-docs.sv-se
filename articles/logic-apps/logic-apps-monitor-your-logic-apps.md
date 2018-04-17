---
title: Kontrollera status, ställa in loggning och få varningar – Azure Logic Apps | Microsoft Docs
description: Övervaka statusen och prestanda för logic apps loggar diagnostikdata och ställa in aviseringar
author: jeffhollan
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: c1d5bc55b132b449ebc2964ef95016a6a4780c19
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Övervaka status, konfigurera diagnostikloggning och aktivera aviseringar för Azure Logic Apps

När du [skapa och köra en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), du kan kontrollera dess körs historik, utlösare historik, status och prestanda. För händelseövervakning och bättre felsökning, ställa in [diagnostikloggning](#azure-diagnostics) för din logikapp. På så sätt kan du kan [söka efter och visa händelser](#find-events), t.ex. utlösaren händelser, kör händelser och händelser för åtgärden. Du kan också använda detta [diagnostikdata med andra tjänster](#extend-diagnostic-data), t.ex. Azure Storage och Händelsehubbar i Azure. 

Om du vill få meddelanden om fel eller andra möjliga problem, Ställ in [aviseringar](#add-azure-alerts). Du kan till exempel skapa en avisering som identifierar ”när fler än fem körs inte på en timme”. Du kan även konfigurera övervakning, spårning och loggning programmässigt med hjälp av [egenskaper och inställningar för Azure-diagnostik händelse](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Visa körs och utlösa historik för din logikapp

1. Hitta din logikapp i den [Azure-portalen](https://portal.azure.com), på Azure huvudmenyn, Välj **alla tjänster**. I sökrutan skriver du ”logikappar” och välj **logikappar**.

   ![Hitta din logikapp](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure-portalen visar alla logikappar som är associerade med din Azure-prenumeration. 

2. Välj din logikapp och sedan **översikt**.

   Azure-portalen visar körs historik och utlösare historik för din logikapp. Exempel:

   ![Logikapp körs historik och utlösare historik](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Kör historik** visar alla körningar för din logikapp. 
   * **Utlösa historik** visar alla Utlösaraktivitet för din logikapp.

   Status finns i [felsöka din logikapp](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Om du inte hittar de data som du förväntar dig, i verktygsfältet väljer du **uppdatera**.

3. Visa stegen från en specifik kördes under **körs historik**, Välj som körs. 

   Övervakaren innehåller varje steg i som körs. Exempel:

   ![Åtgärder för en specifik kör](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Om du vill ha mer information om hur du kör väljer **kör information**. Den här informationen sammanfattas steg, status, indata och utdata för körning. 

   ![Välj ”Kör information”](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Du kan till exempel få kör **Korrelations-ID**, som du kan behöva när du använder den [REST API för Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Om du vill få information om ett specifikt steg, väljer du steget. Nu kan du granska information som indata, utdata och eventuella fel som inträffade för steget. Exempel:

   ![Steg-information](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Alla runtime information och händelser som är krypterade inom Logic Apps-tjänsten. De dekrypteras endast när en användare begär för att visa dessa data. Du kan också styra åtkomst till dessa händelser med [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md).

6. Om du vill få information om en specifik utlösare, gå tillbaka till den **översikt** fönstret. Under **utlösa historik**, Välj händelsen utlösare. Nu kan du granska information som indata och utdata, till exempel:

   ![Utlösaren händelseinformationen för utdata](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Aktivera loggning för din logikapp diagnostik

För bättre felsökning med runtime information och händelser som du kan ställa in diagnostik loggning med [Azure logganalys](../log-analytics/log-analytics-overview.md). Log Analytics är en tjänst i Azure som övervakar molnet och lokala miljöer som hjälper dig att upprätthålla sin tillgänglighet och prestanda. 

Innan du börjar behöver du ha logganalys-arbetsytan. Läs [hur du skapar en logganalys-arbetsytan](../log-analytics/log-analytics-quick-create-workspace.md).

1. I den [Azure-portalen](https://portal.azure.com), söka efter och välj din logikapp. 

2. På menyn logik app bladet under **övervakning**, Välj **diagnostik** > **diagnostikinställningar**.

   ![Gå till inställningar för övervakning, diagnostik, diagnostik](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. Under **diagnostikinställningarna**, Välj **på**.

   ![Aktivera diagnostikloggar](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Nu välja logganalys-arbetsytan och händelse-kategori för loggning enligt:

   1. Välj **skicka till logganalys**. 
   2. Under **logganalys**, Välj **konfigurera**. 
   3. Under **OMS arbetsytor**, Välj logganalys-arbetsytan ska användas för loggning.
   4. Under **loggen**, Välj den **WorkflowRuntime** kategori.
   5. Välj det mått intervallet.
   6. När du är klar väljer du **Spara**.

   ![Välj logganalys-arbetsytan och data för loggning](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Nu hittar du händelser och andra data för utlösaren händelser, kör händelser och händelser för åtgärden.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Sök efter händelser och data för din logikapp

Om du vill söka efter och visa händelser i din logikapp, t.ex. utlösa händelser, kör händelser, och åtgärden händelser, Följ dessa steg.

1. I den [Azure-portalen](https://portal.azure.com), Välj **alla tjänster**. Sök efter ”logganalys” och välj sedan **logganalys** som visas här:

   ![Välj ”logganalys”](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. Under **logganalys**, söka efter och välj logganalys-arbetsytan. 

   ![Välj logganalys-arbetsytan](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. Under **Management**, Välj **OMS-portalen**.

   ![Välj ”OMS-portalen”](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. På startsidan, Välj **loggen Sök**.

   ![På startsidan, väljer du ”loggen Sök”](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   ELLER

   ![På menyn, väljer du ”loggen Sök”](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. I sökrutan anger du ett fält som du vill söka efter och tryck på **RETUR**. När du börjar skriva visas möjliga matchningar och åtgärder som du kan använda. 

   Till exempel vill hitta topp 10-händelser som har inträffat, ange och välj sökfrågan: **söka kategori == ”WorkflowRuntime” | begränsa 10**

   ![Ange söksträngen](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Lär dig mer om [hitta data i logganalys](../log-analytics/log-analytics-log-searches.md).

6. Välj den tidsperiod som du vill visa i fältet till vänster på resultatsidan.
Om du vill begränsa frågan genom att lägga till ett filter, Välj **+ Lägg till**.

   ![Välj tidsram för frågeresultat](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. Under **Lägg till filter**, ange filternamnet så att du kan hitta de filter som du vill använda. Välj filtret och välj **+ Lägg till**.

   Det här exemplet används ordet ”status” för att hitta misslyckade händelser under **AzureDiagnostics**.
   Här filtret för **status_s** har redan valts.

   ![Välj filter](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. I fältet till vänster väljer filtervärde som du vill använda och välj **tillämpa**.

   ![Välj filtervärdet, välj ”Använd”](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. Gå nu tillbaka till den fråga som du utvecklar. Frågan har uppdaterats med ditt valda filter och värdet. Tidigare resultaten filtreras nu för.

   ![Gå tillbaka till din fråga med filtrerade resultat](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. Om du vill spara frågan för framtida användning, Välj **spara**. Läs [hur du sparar din fråga](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Utöka hur och när du använder diagnostikdata med andra tjänster

Du kan utöka hur du använder din logikapp diagnostikdata med andra Azure-tjänster, till exempel tillsammans med Azure Log Analytics: 

* [Arkivera Azure Diagnostics loggar i Azure-lagring](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Dataströmmen Azure Diagnostics loggar till Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Du kan sedan get realtid övervakning med hjälp av telemetri och analyser från andra tjänster som [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och [Power BI](../log-analytics/log-analytics-powerbi.md). Exempel:

* [Dataströmmen data från Händelsehubbar till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysera strömmande data med Stream Analytics och skapa en instrumentpanel för analys i realtid i Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Baserat på de alternativ som du vill konfigurera, ska du se till att du första [skapa ett Azure storage-konto](../storage/common/storage-create-storage-account.md) eller [skapar en Azure händelsehubb](../event-hubs/event-hubs-create.md). Välj alternativ för där du vill skicka diagnostikdata:

![Skicka data till Azure storage-konto eller händelse-hubb](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Kvarhållningsperioder gäller endast när du väljer att använda ett lagringskonto.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Konfigurera aviseringar för din logikapp

Om du vill övervaka specifika mått eller överskred tröskelvärden för din logikapp, ställa in [aviseringar i Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). Lär dig mer om [mått i Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Ställa in aviseringar utan [Azure logganalys](../log-analytics/log-analytics-overview.md), Följ dessa steg. För mer avancerade kriterier som aviseringar och åtgärder, [konfigurera logganalys](#azure-diagnostics) för.

1. På menyn logik app bladet under **övervakning**, Välj **diagnostik** > **Varna regler** > **Lägg till avisering** som visas här:

   ![Lägga till en avisering för din logikapp](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. På den **lägga till en varningsregel** bladet skapa aviseringen som visas:

   1. Under **resurs**, Välj din logikapp, om det inte redan är markerad. 
   2. Ange ett namn och beskrivning för aviseringen.
   3. Välj en **mått** eller den händelse som du vill spåra.
   4. Välj en **villkoret**, ange en **tröskelvärdet** mått och välj den **Period** för övervakning av det här måttet.
   5. Välj om du vill skicka e-post för aviseringen. 
   6. Ange andra e-postadresser för att skicka aviseringen. 
   Du kan också ange en Webhooksadressen där du vill skicka en avisering.

   Till exempel regeln skickar en avisering när fem eller fler körningar misslyckas på en timme:

   ![Skapa mått varningsregel](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Om du vill köra en logikapp från en avisering som du kan inkludera den [begäran utlösaren](../connectors/connectors-native-reqres.md) i arbetsflödet, där du kan utföra åtgärder som de här exemplen:
> 
> * [Posta till Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Skicka en text](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Lägga till ett meddelande till en kö](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Azure Diagnostics händelse inställningar och information

Varje diagnostiska händelsen innehåller information om din logikapp och att händelsen, till exempel status, starttid, Sluttid och så vidare. Om du vill konfigurera övervakning, spårning och loggning, kan du använda dessa uppgifter med den [REST API: et för Azure Logikappar](https://docs.microsoft.com/rest/api/logic) och [REST API för Azure-diagnostik](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

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

* `clientTrackingId`: Om det inte angetts i Azure genererar detta ID och korrelerar händelser över en logikapp som körs automatiskt, inklusive alla kapslade arbetsflöden som anropas från logikappen. Du kan ange detta ID från en utlösare manuellt genom att skicka en `x-ms-client-tracking-id` huvud med din anpassade ID-värde i dess utlösare. Du kan använda en utlösare för begäran, http-utlösaren eller webhook-utlösare.

* `trackedProperties`: Du kan lägga till egenskaper för spårade åtgärder i din logikapp JSON-definitionen om du vill spåra indata eller utdata i diagnostikdata. Spårade egenskaper kan spåra bara en enda åtgärd indata och utdata, men du kan använda den `correlation` egenskaper för händelse för korrelation över åtgärder körs.

  Om du vill spåra en eller flera egenskaper, lägger du till den `trackedProperties` avsnitt och egenskaper som du vill skapa en åtgärdsdefinition. Anta att du vill spåra data som en ”order-ID” i din telemetri:

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

* [Skapa mallar för logik app-distribution och versionshantering](../logic-apps/logic-apps-create-deploy-template.md)
* [B2B-scenarier med Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Övervaka B2B-meddelanden](../logic-apps/logic-apps-monitor-b2b-message.md)
