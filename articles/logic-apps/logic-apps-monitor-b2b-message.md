---
title: Övervaka B2B-meddelanden och ställer in loggning – Azure Logic Apps | Microsoft Docs
description: Övervaka AS2-, X 12 och EDIFACT-meddelanden. Konfigurera diagnostikloggning för ditt integrationskonto i Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 07/21/2017
ms.openlocfilehash: 63aa455851633d1e49fd1b26861aaac8a670ef15
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404792"
---
# <a name="monitor-b2b-messages-and-set-up-logging-for-integration-accounts-in-azure-logic-apps"></a>Övervaka B2B-meddelanden och konfigurera loggning för integrationskonton i Azure Logic Apps

När du har konfigurerat B2B-kommunikation mellan två kör affärsprocesser eller program via ditt integrationskonto dessa entiteter kan utbyta meddelanden med varandra. Bekräfta den här kommunikationen fungerar som förväntat och du kan ställa in övervakning för AS2-, X12, EDIFACT-meddelanden, tillsammans med diagnostikloggning för ditt integrationskonto via den [Azure Log Analytics](../log-analytics/log-analytics-overview.md) service. Tjänsten övervakar dina molnbaserade och lokala miljöer, vilket hjälper dig att bibehålla tillgänglighet och prestanda, och samlar även in runtime samt händelser för bättre felsökning. Du kan också [använda dina diagnostiska data med andra tjänster](#extend-diagnostic-data), som Azure Storage och Azure Event Hubs.

## <a name="requirements"></a>Krav

* En logikapp som har konfigurerats med diagnostikloggning. Lär dig [hur du ställer in loggning för den logikappen](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > När du har uppfyllt detta krav, bör du ha en arbetsyta i Log Analytics. Du bör använda samma Log Analytics-arbetsytan när du ställer in loggning för ditt integrationskonto. Om du inte har en Log Analytics-arbetsyta kan du läsa [hur du skapar en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md).

* Ett integrationskonto som är länkad till din logikapp. Lär dig [hur du skapar ett integrationskonto med en länk till din logikapp](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Slå på diagnostik loggning för ditt integrationskonto

Du kan aktivera loggning antingen direkt från ditt integrationskonto eller [via Azure Monitor-tjänsten](#azure-monitor-service). Azure Monitor innehåller grundläggande övervakning med infrastrukturnivå. Läs mer om [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Slå på diagnostik loggning direkt från ditt integrationskonto

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj ditt integrationskonto. Under **övervakning**, Välj **diagnostikloggar** som visas här:

   ![Hitta och välj ditt integrationskonto, Välj ”diagnostikloggar”](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. När du har valt ditt integrationskonto väljs automatiskt följande värden. Om dessa värden är korrekta, välja **slå på diagnostik**. Annars väljer du de värden som du vill:

   1. Under **prenumeration**, Välj den prenumeration som du använder med ditt integrationskonto.
   2. Under **resursgrupp**, Välj den resursgrupp som du använder med ditt integrationskonto.
   3. Under **resurstyp**väljer **integrationskonton**. 
   4. Under **Resource**, Välj ditt integrationskonto. 
   5. Välj **slå på diagnostik**.

   ![Konfigurera diagnostik för ditt integrationskonto](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Under **diagnostikinställningar**, och sedan **Status**, Välj **på**.

   ![Aktivera Azure-diagnostik](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Nu väljer du Log Analytics-arbetsytan och data som ska användas för loggning som visas:

   1. Välj **skicka till Log Analytics**. 
   2. Under **Log Analytics**, Välj **konfigurera**. 
   3. Under **OMS-arbetsytor**, Välj Log Analytics-arbetsytan ska användas för loggning. 
   > [!NOTE]
   > OMS-arbetsytor kallas nu för Log Analytics-arbetsytor. 
   4. Under **Log**väljer den **IntegrationAccountTrackingEvents** kategori.
   5. Välj **Spara**.

   ![Konfigurera Log Analytics så att du kan skicka diagnostikdata till en logg](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Nu [konfigurera spårning för B2B-meddelanden i Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Aktivera diagnostikloggning via Azure Monitor

1. I den [Azure-portalen](https://portal.azure.com), på Azure-huvudmenyn väljer **övervakaren**, **diagnostikloggar**. Välj sedan ditt integrationskonto som visas här:

   ![Välj ”övervaka”, ”diagnostikloggar”, Välj ditt integrationskonto](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. När du har valt ditt integrationskonto väljs automatiskt följande värden. Om dessa värden är korrekta, välja **slå på diagnostik**. Annars väljer du de värden som du vill:

   1. Under **prenumeration**, Välj den prenumeration som du använder med ditt integrationskonto.
   2. Under **resursgrupp**, Välj den resursgrupp som du använder med ditt integrationskonto.
   3. Under **resurstyp**väljer **integrationskonton**.
   4. Under **Resource**, Välj ditt integrationskonto.
   5. Välj **slå på diagnostik**.

   ![Konfigurera diagnostik för ditt integrationskonto](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Under **diagnostikinställningar**, Välj **på**.

   ![Aktivera Azure-diagnostik](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Nu välja den Log Analytics-arbetsytan och händelsen kategorin för loggning som visas:

   1. Välj **skicka till Log Analytics**. 
   2. Under **Log Analytics**, Välj **konfigurera**. 
   3. Under **OMS-arbetsytor**, Välj Log Analytics-arbetsytan ska användas för loggning.
   > [!NOTE]
   > OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.
   4. Under **Log**väljer den **IntegrationAccountTrackingEvents** kategori.
   5. När du är klar väljer du **Spara**.

   ![Konfigurera Log Analytics så att du kan skicka diagnostikdata till en logg](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Nu [konfigurera spårning för B2B-meddelanden i Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Utöka hur och var du kan använda diagnostikdata med andra tjänster

Tillsammans med Azure Log Analytics kan utöka du hur du använda diagnostikdata för din logikapp med andra Azure-tjänster, till exempel: 

* [Arkivera Azure Diagnostics-loggar i Azure Storage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Stream Azure-diagnostikloggar för Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Du kan sedan hämta i realtid övervakningen med hjälp av telemetri och analyser från andra tjänster som [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och [Power BI](../log-analytics/log-analytics-powerbi.md). Exempel:

* [Stream-data från Event Hubs till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysera data med Stream Analytics och skapa en instrumentpanel för analys i realtid i Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Baserat på de alternativ som du vill konfigurera, ska du se till att du första [skapa ett Azure storage-konto](../storage/common/storage-create-storage-account.md) eller [skapar en Azure event hub](../event-hubs/event-hubs-create.md). Välj sedan alternativ för där du vill skicka diagnostikdata:

![Skicka data till Azure storage-konto eller event hub](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Lagringstider gäller endast när du väljer att använda ett lagringskonto.

## <a name="supported-tracking-schemas"></a>Stöd för spårningsscheman

Azure stöder dessa spåra schematyper där alla har åtgärdat scheman utom typen anpassad.

* [AS2-spårningsschema](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsschema](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassat spårningsschema](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Spåra B2B-meddelanden i Azure Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "spåra B2B-meddelanden i Azure Log Analytics")
* [Mer information om Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")

