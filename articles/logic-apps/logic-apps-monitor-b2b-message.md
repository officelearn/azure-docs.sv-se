---
title: "Övervaka B2B-transaktioner och konfigurera loggning - Azure Logic Apps | Microsoft Docs"
description: "Övervaka AS2-, X 12- och EDIFACT-meddelanden, start diagnostikloggning för ditt konto för integrering"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: f717dae9a70a96944b623f22b90cf8c5a943f382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>Övervaka och konfigurera diagnostikloggning för B2B-kommunikation i integrationskonton

När du har skapat B2B-kommunikation mellan två kör affärsprocesser eller program via kontot integration dessa enheter kan utbyta meddelanden med varandra. Bekräfta den här kommunikationen fungerar som förväntat, du kan konfigurera övervakning för AS2-, X12, och EDIFACT-meddelanden, tillsammans med diagnostikloggning för ditt konto för integrering med den [Azure logganalys](../log-analytics/log-analytics-overview.md) service. Den här tjänsten i [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) övervakar molnet och lokala miljöer, vilket hjälper dig att upprätthålla sin tillgänglighet och prestanda, och samlar även in information om körning och händelser för bättre felsökning. Du kan också [använda diagnostiska data med andra tjänster](#extend-diagnostic-data), t.ex. Azure Storage och Händelsehubbar i Azure.

## <a name="requirements"></a>Krav

* En logikapp som har konfigurerats med diagnostikloggning. Läs [hur du ställer in loggning för att logikapp](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > När du har uppfyllt detta krav, bör du ha en arbetsyta den [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Du bör använda samma OMS-arbetsyta när du ställer in loggning för ditt konto för integrering. Lär dig mer om du inte har en OMS-arbetsyta [hur du skapar en OMS-arbetsyta](../log-analytics/log-analytics-get-started.md).

* Ett konto som är länkad till din logikapp, integration. Läs [hur du skapar ett konto för integrering med en länk till din logikapp](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Aktivera diagnostik loggning för ditt konto för integrering

Du kan aktivera loggning antingen direkt från ditt konto för integrering eller [via tjänsten Azure-Monitor](#azure-monitor-service). Övervakare för Azure tillhandahåller grundläggande övervakning med infrastrukturnivå data. Lär dig mer om [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Aktivera diagnostik loggning direkt från ditt konto för integrering

1. I den [Azure-portalen](https://portal.azure.com), söka efter och välj ditt konto för integrering. Under **övervakning**, Välj **diagnostik loggar** som visas här:

   ![Hitta och markera kontot integration, Välj ”diagnostikloggar”](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Följande värden markeras automatiskt när du har valt integration-konto. Om dessa värden är korrekta, välja **aktivera diagnostiken**. Annars väljer du de värden som du vill:

   1. Under **prenumeration**, Välj den Azure-prenumeration som du använder med ditt konto för integrering.
   2. Under **resursgruppen**, markera den resursgrupp som du använder med ditt konto för integrering.
   3. Under **resurstypen**väljer **integrationskonton**. 
   4. Under **resurs**, Välj ditt konto för integrering. 
   5. Välj **aktivera diagnostiken**.

   ![Konfigurera diagnostik för ditt konto för integrering](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Under **diagnostikinställningarna**, och sedan **Status**, Välj **på**.

   ![Aktivera Azure-diagnostik](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Välj nu OMS-arbetsytan och data som ska användas för loggning som visas:

   1. Välj **skicka till logganalys**. 
   2. Under **logganalys**, Välj **konfigurera**. 
   3. Under **OMS arbetsytor**, Välj OMS-arbetsyta för loggning.
   4. Under **loggen**, Välj den **IntegrationAccountTrackingEvents** kategori.
   5. Välj **Spara**.

   ![Ställ in logganalys så att du kan skicka diagnostikdata till en logg](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Nu [konfigurera spårning för dina B2B-meddelanden i OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Aktivera diagnostikloggning via Azure-Monitor

1. I den [Azure-portalen](https://portal.azure.com), på Azure huvudmenyn, Välj **övervakaren**, **diagnostik loggar**. Välj ditt konto, integration som visas här:

   ![Välj ”övervaka”, ”diagnostikloggar”, Välj integration-konto](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Följande värden markeras automatiskt när du har valt integration-konto. Om dessa värden är korrekta, välja **aktivera diagnostiken**. Annars väljer du de värden som du vill:

   1. Under **prenumeration**, Välj den Azure-prenumeration som du använder med ditt konto för integrering.
   2. Under **resursgruppen**, markera den resursgrupp som du använder med ditt konto för integrering.
   3. Under **resurstypen**väljer **integrationskonton**.
   4. Under **resurs**, Välj ditt konto för integrering.
   5. Välj **aktivera diagnostiken**.

   ![Konfigurera diagnostik för ditt konto för integrering](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Under **diagnostikinställningarna**, Välj **på**.

   ![Aktivera Azure-diagnostik](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Nu välja OMS-arbetsytan och händelse-kategori för loggning enligt:

   1. Välj **skicka till logganalys**. 
   2. Under **logganalys**, Välj **konfigurera**. 
   3. Under **OMS arbetsytor**, Välj OMS-arbetsyta för loggning.
   4. Under **loggen**, Välj den **IntegrationAccountTrackingEvents** kategori.
   5. När du är klar väljer **spara**.

   ![Ställ in logganalys så att du kan skicka diagnostikdata till en logg](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Nu [konfigurera spårning för dina B2B-meddelanden i OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Utöka hur och när du använder diagnostikdata med andra tjänster

Du kan utöka hur du använder din logikapp diagnostikdata med andra Azure-tjänster, till exempel tillsammans med Azure Log Analytics: 

* [Arkivera Azure Diagnostics loggar i Azure-lagring](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Dataströmmen Azure Diagnostics loggar till Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Du kan sedan get realtid övervakning med hjälp av telemetri och analyser från andra tjänster som [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och [Power BI](../log-analytics/log-analytics-powerbi.md). Exempel:

* [Dataströmmen data från Händelsehubbar till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysera strömmande data med Stream Analytics och skapa en instrumentpanel för analys i realtid i Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Baserat på de alternativ som du vill konfigurera, ska du se till att du första [skapa ett Azure storage-konto](../storage/common/storage-create-storage-account.md) eller [skapar en Azure händelsehubb](../event-hubs/event-hubs-create.md). Välj alternativ för där du vill skicka diagnostikdata:

![Skicka data till Azure storage-konto eller händelse-hubb](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Kvarhållningsperioder gäller endast när du väljer att använda ett lagringskonto.

## <a name="supported-tracking-schemas"></a>Spåra scheman som stöds

Azure stöder detta spårning schematyper, som har åtgärdats scheman utom typen anpassad.

* [AS2-spårningsschema](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsschema](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassat spårningsschema](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Spåra B2B-meddelanden i OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "spåra B2B-meddelanden i OMS")
* [Mer information om Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")

