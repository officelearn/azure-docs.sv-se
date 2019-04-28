---
title: Övervaka B2B-meddelanden med Azure Monitor - loggar i Azure Logic Apps | Microsoft Docs
description: Övervaka AS2-, X 12 och EDIFACT-meddelanden för integrationskonton och Azure Logic Apps och konfigurera diagnostikloggning med Azure Monitor-loggar
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: 12799a308157c3c0e19de1f82c0fe3df44fad37e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106308"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>Övervaka B2B-meddelanden med Azure Monitor-loggar i Azure Logic Apps

När du har konfigurerat B2B-kommunikation mellan handelspartner i ditt integrationskonto kan partner utbyta meddelanden med varandra. Kontrollera att den här kommunikationen fungerar på samma sätt som du förväntar dig kan du övervaka AS2, X12, och EDIFACT-meddelanden och ställer in diagnostik loggning för ditt konto för integrering med [Azure Monitor loggar](../log-analytics/log-analytics-overview.md). Den här tjänsten övervakar dina molnet och lokala miljöer, hjälper dig att bibehålla tillgänglighet och prestanda, och samlar in runtime samt händelser för bättre felsökning. Du kan också använda dessa data med andra tjänster som Azure Storage och Azure Event Hubs.

> [!NOTE]
> Den här sidan kan fortfarande ha referenser till Microsoft Operations Management Suite (OMS), vilket är [tas ur bruk i januari 2019](../azure-monitor/platform/oms-portal-transition.md), men ersätter de här stegen med Azure Log Analytics där det är möjligt. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* En logikapp som har konfigurerats med diagnostikloggning. Lär dig [så här skapar du en logikapp](quickstart-create-first-logic-app-workflow.md) och [hur du ställer in loggning för den logikappen](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* När du uppfyller föregående krav måste också en Log Analytics-arbetsyta som du använder för att övervaka och spåra B2B-kommunikation via Azure Monitor-loggar. Om du inte har en Log Analytics-arbetsyta kan du läsa [hur du skapar en Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md).

* Ett integrationskonto som är länkad till din logikapp. Lär dig [hur du skapar ett integrationskonto med en länk till din logikapp](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Aktivera diagnostikloggning

Du kan aktivera loggning antingen direkt från ditt integrationskonto eller [via Azure Monitor-tjänsten](#azure-monitor-service). Azure Monitor innehåller grundläggande övervakning med infrastrukturnivå. Läs mer om [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Aktivera loggning från integrationskontot

1. I den [Azure-portalen](https://portal.azure.com), hitta och välj ditt integrationskonto. Under **övervakning**väljer **diagnostikinställningar**.

   ![Hitta och välj ditt integrationskonto, Välj ”diagnostikinställningar”](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Nu hitta och välj ditt integrationskonto. I filtret listor, väljer du de värden som gäller för ditt integrationskonto.
När du är klar väljer **Lägg till diagnostikinställning**.

   | Egenskap  | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-prenumerationsnamn*> | Azure-prenumerationen som är associerat med ditt integrationskonto | 
   | **Resursgrupp** | <*Azure-resource-group-name*> | Azure-resursgrupp för ditt integrationskonto | 
   | **Resurstyp** | **Integrationskonton** | Typen för den Azure-resurser där du vill aktivera loggning | 
   | **Resurs** | <*integration-account-name*> | Namn för din Azure-resurs där du vill aktivera loggning | 
   ||||  

   Exempel:

   ![Konfigurera diagnostik för ditt integrationskonto](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Ange ett namn för din nya diagnostikinställning och välj Log Analytics-arbetsytan och de data som du vill logga.

   1. Välj **skicka till Log Analytics**. 

   1. Under **Log Analytics**väljer **konfigurera**. 

   1. Under **OMS-arbetsytor**, Välj Log Analytics-arbetsytan som du vill använda för loggning. 

      > [!NOTE]
      > OMS-arbetsytor ersätts med Log Analytics-arbetsytor. 

   1. Under **Log**väljer den **IntegrationAccountTrackingEvents** kategori, och välj **spara**.

   Exempel: 

   ![Konfigurera Azure Monitor-loggar så att du kan skicka diagnostikdata till en logg](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Nu [konfigurera spårning för B2B-meddelanden i Azure Monitor-loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Aktivera loggning med Azure Monitor

1. I den [Azure-portalen](https://portal.azure.com), på Azure-huvudmenyn väljer **övervakaren**. Under **inställningar**väljer **diagnostikinställningar**. 

   ![Välj ”Övervakare” > ”diagnostikinställningar” > ditt integrationskonto](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Nu hitta och välj ditt integrationskonto. I filtret listor, väljer du de värden som gäller för ditt integrationskonto.
När du är klar väljer **Lägg till diagnostikinställning**.

   | Egenskap  | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-prenumerationsnamn*> | Azure-prenumerationen som är associerat med ditt integrationskonto | 
   | **Resursgrupp** | <*Azure-resource-group-name*> | Azure-resursgrupp för ditt integrationskonto | 
   | **Resurstyp** | **Integrationskonton** | Typen för den Azure-resurser där du vill aktivera loggning | 
   | **Resurs** | <*integration-account-name*> | Namn för din Azure-resurs där du vill aktivera loggning | 
   ||||  

   Exempel:

   ![Konfigurera diagnostik för ditt integrationskonto](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Ange ett namn för din nya diagnostikinställning och välj Log Analytics-arbetsytan och de data som du vill logga.

   1. Välj **skicka till Log Analytics**. 

   1. Under **Log Analytics**väljer **konfigurera**. 

   1. Under **OMS-arbetsytor**, Välj Log Analytics-arbetsytan som du vill använda för loggning. 

      > [!NOTE]
      > OMS-arbetsytor ersätts med Log Analytics-arbetsytor. 

   1. Under **Log**väljer den **IntegrationAccountTrackingEvents** kategori, och välj **spara**.

   Exempel: 

   ![Konfigurera Azure Monitor-loggar så att du kan skicka diagnostikdata till en logg](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Nu [konfigurera spårning för B2B-meddelanden i Azure Monitor-loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Använda diagnostiska data med andra tjänster

Du kan utöka hur du använda diagnostikdata för din logikapp med andra Azure-tjänster, till exempel tillsammans med Azure Monitor-loggar: 

* [Arkivera Azure Diagnostics-loggar i Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Stream Azure-diagnostikloggar för Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Du kan sedan hämta i realtid övervakningen med hjälp av telemetri och analyser från andra tjänster som [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och [Power BI](../azure-monitor/platform/powerbi.md). Exempel:

* [Stream-data från Event Hubs till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysera data med Stream Analytics och skapa en instrumentpanel för analys i realtid i Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Utifrån de alternativ som du vill ange, ska du se till att du första [skapa ett Azure storage-konto](../storage/common/storage-create-storage-account.md) eller [skapar en Azure event hub](../event-hubs/event-hubs-create.md). Du kan sedan välja mål där du vill skicka diagnostikdata.
Lagringstider gäller endast när du väljer att använda ett lagringskonto.

![Skicka data till Azure storage-konto eller event hub](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Stöd för spårningsscheman

Azure stöder dessa spåra schematyper där alla har åtgärdat scheman utom typen anpassad.

* [AS2-spårningsschema](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsschema](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassat spårningsschema](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Spåra B2B-meddelanden i Azure Monitor-loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "spåra B2B-meddelanden i Azure Monitor-loggar")
* [Mer information om Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")

