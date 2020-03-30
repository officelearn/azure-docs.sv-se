---
title: Övervaka logikappar med hjälp av Azure Monitor-loggar
description: Felsöka logikappar genom att konfigurera Azure Monitor-loggar och samla in diagnostikdata för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270243"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Konfigurera Azure Monitor-loggar och samla in diagnostikdata för Azure Logic Apps

Om du vill få mer information om felsökning av dina logikappar under körning kan du konfigurera och använda [Azure Monitor-loggar](../azure-monitor/platform/data-platform-logs.md) för att registrera och lagra information om körningsdata och händelser, till exempel utlösa händelser, köra händelser och åtgärdshändelser på en [Log Analytics-arbetsyta](../azure-monitor/platform/resource-logs-collect-workspace.md). [Azure Monitor](../azure-monitor/overview.md) hjälper dig att övervaka dina molnmiljöer och lokala miljöer så att du lättare kan underhålla deras tillgänglighet och prestanda. Genom att använda Azure Monitor-loggar kan du skapa [loggfrågor](../azure-monitor/log-query/log-query-overview.md) som hjälper dig att samla in och granska den här informationen. Du kan också [använda dessa diagnostikdata med andra Azure-tjänster](#extend-data), till exempel Azure Storage och Azure Event Hubs.

Om du vill konfigurera loggning för logikappen kan du [aktivera Log Analytics när du skapar logikappen](#logging-for-new-logic-apps)eller installera logic apps [management-lösningen](#install-management-solution) i logganalysarbetsytan för befintliga logikappar. Den här lösningen innehåller aggregerad information för logikappkörningar och innehåller specifika detaljer som status, körningstid, återinmatningsstatus och korrelations-ID:er. Sedan, för att aktivera loggning och skapa frågor för den här informationen, [ställa in Azure Monitor loggar](#set-up-resource-logs).

Den här artikeln visar hur du aktiverar Log Analytics när du skapar logikappar, hur du installerar och konfigurerar Logic Apps Management-lösningen och hur du konfigurerar och skapar frågor för Azure Monitor-loggar.

## <a name="prerequisites"></a>Krav

Innan du börjar behöver du en [Log Analytics-arbetsyta](../azure-monitor/platform/resource-logs-collect-workspace.md). Om du inte har en arbetsyta kan du läsa om hur du [skapar en Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Aktivera Log Analytics för nya logikappar

Du kan aktivera Logganalys när du skapar logikappen.

1. I [Azure-portalen](https://portal.azure.com)gör du så här i fönstret där du anger informationen för att skapa logikappen:

   1. Under **Logga analytics**väljer du **På**.

   1. I **listan Logganalysarbetsyta** väljer du den arbetsyta där du vill skicka data från logikappen körs.

      ![Ange information om din logikapp](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      När du är klar med det här steget skapar Azure din logikapp, som nu är associerad med din Log Analytics-arbetsyta. Det här steget installerar också logic apps management-lösningen automatiskt på arbetsytan.

1. När du är klar väljer du **Skapa**.

1. När du har kört logikappen fortsätter du [med de här stegen](#view-logic-app-runs)om du vill visa logikappens körningar.

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Installera logic apps management-lösning

Om du har aktiverat Log Analytics när du skapade logikappen hoppar du över det här steget. Du har redan logic apps management-lösningen installerad på din Log Analytics-arbetsyta.

1. I sökrutan [för Azure-portalen](https://portal.azure.com)anger du `log analytics workspaces`och väljer sedan Log **Analytics-arbetsytor**.

   ![Välj "Log Analytics-arbetsytor"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. Välj arbetsyta under **Log Analytics-arbetsytor.**

   ![Välj arbetsytan Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. Välj **Visa lösningar**under **Kom igång med Logganalys** > **Konfigurera övervakningslösningar**i **fönstret Översikt** .

   ![I översiktsfönstret väljer du "Visa lösningar"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Under **Översikt**väljer du **Lägg till**.

   ![Lägg till ny lösning i översiktsfönstret](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. När **Marketplace** har öppnats anger `logic apps management`du i sökrutan och väljer **Logic Apps Management**.

   ![På Marketplace väljer du "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. Välj **Skapa**i fönstret lösningsbeskrivning .

   ![Välj "Skapa" om du vill lägga till lösning för hantering av "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Granska och bekräfta log analytics-arbetsytan där du vill installera lösningen och välj **Skapa** igen.

   ![Välj "Skapa" för "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   När Azure har distribuerat lösningen till Azure-resursgruppen som innehåller din Log Analytics-arbetsyta visas lösningen i arbetsytans sammanfattningsfönster.

   ![Sammanfattningsfönster för arbetsyta](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Konfigurera Azure Monitor-loggar

När du lagrar information om körningshändelser och data i [Azure Monitor-loggar](../azure-monitor/platform/data-platform-logs.md)kan du skapa [loggfrågor](../azure-monitor/log-query/log-query-overview.md) som hjälper dig att hitta och granska den här informationen.

1. Leta reda på och välj logikapp i [Azure-portalen.](https://portal.azure.com)

1. Välj **Diagnostikinställningar** > **Lägg till diagnostikinställning**under **Övervakning**på logikapp-menyn .

   ![Under "Övervakning" väljer du "Diagnostikinställningar" > "Lägg till diagnostikinställning"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Så här skapar du inställningen:

   1. Ange ett namn för inställningen.

   1. Välj **Skicka till logganalys**.

   1. För **prenumeration**väljer du den Azure-prenumeration som är associerad med din Log Analytics-arbetsyta.

   1. För **Logganalysarbetsyta**väljer du den arbetsyta som du vill använda.

   1. Under **loggen**väljer du kategorin **WorkflowRuntime,** som anger den händelsekategori som du vill spela in.

   1. Om du vill välja alla mått väljer du **AllMetrics**under **mått**.

   1. När du är klar väljer du **Spara**.

   Ett exempel:

   ![Välj Log Analytics-arbetsyta och data för loggning](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Visa status för logikappkörningar

När logikappen har körts kan du visa data om dessa körningar på logganalysarbetsytan.

1. Leta reda på och öppna arbetsytan Log Analytics i [Azure-portalen.](https://portal.azure.com)

1. På arbetsytans meny väljer du**Logic Apps Management**för **arbetsyta.** > 

   ![Status och antal för logikappkörning](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Om panelen Logic Apps Management inte omedelbart visar resultat efter en körning kan du prova att välja **Uppdatera** eller vänta en kort stund innan du försöker igen.

   Här grupperas dina logikappkörningar efter namn eller körningsstatus. På den här sidan visas också information om fel i åtgärder eller utlösare för logikappkörningar.

   ![Statussammanfattning för logikappkörningar](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Om du vill visa alla körningar för en viss logikapp eller status markerar du raden för den logikappen eller statusen.

   Här är ett exempel som visar alla körningar för en viss logikapp:

   ![Visa körningar och status för logikappar](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   För åtgärder där du [ställer in spårade egenskaper](#extend-data)kan du också visa dessa egenskaper genom att välja **Visa** i kolumnen **Spårade egenskaper.** Om du vill söka i de spårade egenskaperna använder du kolumnfiltret.

   ![Visa spårade egenskaper för en logikapp](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Spårade egenskaper eller slutförda händelser kan uppstå 10–15 minuters fördröjning innan de visas på logganalysarbetsytan.
   > Dessutom är funktionen **Skicka om** på den här sidan inte tillgänglig.

1. Om du vill filtrera resultaten kan du utföra filtrering på både klient- och serversidan.

   * **Filter på klientsidan**: För varje kolumn väljer du önskade filter, till exempel:

     ![Exempel på kolumnfilter](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Serverfilter:** Om du vill välja ett visst tidsfönster eller begränsa antalet körningar som visas använder du scopekontrollen högst upp på sidan. Som standard visas bara 1 000 poster åt gången.

     ![Ändra tidsfönstret](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Om du vill visa alla åtgärder och deras information för en viss körning markerar du raden för en logikappkörning.

   Här är ett exempel som visar alla åtgärder och utlösare för en viss logikappkörning:

   ![Visa åtgärder för en logikappkörning](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Skicka diagnostikdata till Azure Storage och Azure Event Hubs

Tillsammans med Azure Monitor-loggar kan du utöka hur du använder logikappens diagnostikdata med andra Azure-tjänster, till exempel:

* [Arkivera Azure-resursloggar till lagringskonto](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Strömma Azure-plattformsloggar till Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Du kan sedan få övervakning i realtid med hjälp av telemetri och analyser från andra tjänster, till exempel [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och Power [BI](../azure-monitor/platform/powerbi.md). Ett exempel:

* [Strömma data från händelsehubbar till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysera strömmande data med Stream Analytics och skapa en instrumentpanel för analys i realtid i Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Baserat på de platser där du vill skicka diagnostikdata, se till att du först [skapar ett Azure-lagringskonto](../storage/common/storage-create-storage-account.md) eller [skapar en Azure-händelsehubb](../event-hubs/event-hubs-create.md). Du kan sedan välja de destinationer där du vill skicka dessa data. Kvarhållningsperioder gäller endast när du använder ett lagringskonto.

![Skicka data till Azure-lagringskonto eller händelsehubb](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Diagnostikhändelser för Azure Monitor

Varje diagnostikhändelse innehåller information om logikappen och den händelsen, till exempel status, starttid, sluttid och så vidare. Om du vill konfigurera övervakning, spårning och loggning programmatiskt kan du använda den här informationen med [REST API för Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) och REST API för Azure [Monitor](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows). Du kan också `clientTrackingId` `trackedProperties` använda egenskaperna och som visas i 

* `clientTrackingId`: Om det inte tillhandahålls genererar Azure automatiskt detta ID och korrelerar händelser över en logikappkörning, inklusive alla kapslade arbetsflöden som anropas från logikappen. Du kan manuellt ange detta ID i `x-ms-client-tracking-id` en utlösare genom att skicka ett huvud med ditt anpassade ID-värde i utlösarbegäran. Du kan använda en begäran trigger, HTTP-utlösare, eller webhook utlösare.

* `trackedProperties`: Om du vill spåra indata eller utdata `trackedProperties` i diagnostikdata kan du lägga till ett avsnitt i en åtgärd antingen med hjälp av Logic App Designer eller direkt i logikappens JSON-definition. Spårade egenskaper kan spåra endast en enskild åtgärds indata och `correlation` utdata, men du kan använda egenskaperna för händelser för att korrelera mellan åtgärder i en körning. Om du vill spåra mer än en `trackedProperties` egenskap, en eller flera egenskaper, lägger du till avsnittet och de egenskaper som du vill ha i åtgärdsdefinitionen.

  Här är ett exempel som visar hur **den initierade variabelåtgärdsdefinitionen** innehåller spårade egenskaper från åtgärdens indata där indata är en matris, inte en post.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  I det här exemplet visas flera spårade egenskaper:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Det här exemplet `ActionCompleted` visar `clientTrackingId` hur `trackedProperties` händelsen innehåller attributen och:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
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
         "workflowId": "<logic-app-workflow-ID>",
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
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Skapa frågor om övervakning och spårning](../logic-apps/create-monitoring-tracking-queries.md)
* [Övervaka B2B-meddelanden med Azure Monitor-loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)
