---
title: Övervaka B2B-meddelanden med Azure Monitor-Azure Logic Apps
description: Konfigurera diagnostik-loggning för AS2-, X12-och EDIFACT-meddelanden i Azure Logic Apps med Azure Monitor
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: e5c8e5f3d42f4e85406fcc7dd5a2f6602045c8ed
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680193"
---
# <a name="set-up-diagnostics-logging-for-b2b-messages-in-azure-logic-apps-by-using-azure-monitor"></a>Konfigurera diagnostikloggning för B2B-meddelanden i Azure Logic Apps med Azure Monitor

När du har konfigurerat B2B-kommunikation mellan handels partner i ditt integrations konto kan dessa partners utbyta meddelanden med varandra. För att kontrol lera att den här kommunikationen fungerar som förväntat kan du övervaka AS2-, X12-och EDIFACT-meddelanden och konfigurera diagnostikloggning för integrations kontot med [Azure Monitor loggar](../log-analytics/log-analytics-overview.md). Den här tjänsten övervakar molnet och lokala miljöer, hjälper dig att underhålla deras tillgänglighet och prestanda och samlar in körnings information och händelser för bättre fel sökning. Du kan också använda dessa data med andra tjänster, till exempel Azure Storage och Azure Event Hubs.

> [!NOTE]
> Den här sidan kan fortfarande innehålla referenser till Microsoft Operations Management Suite (OMS), som tas [ur bruk i januari 2019](../azure-monitor/platform/oms-portal-transition.md), men ersätter dessa steg med Azure Log Analytics där det är möjligt. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav

* En Logic-app som är konfigurerad med diagnostikloggning. Lär dig [hur du skapar en logisk app](quickstart-create-first-logic-app-workflow.md) och [hur du ställer in loggning för den Logic app](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* När du uppfyller de tidigare kraven måste du också ha en Log Analytics arbets yta som du använder för att övervaka och spåra B2B-kommunikation via Azure Monitor loggar. Om du inte har en Log Analytics arbets yta, lär du dig [hur du skapar en Log Analytics arbets yta](../azure-monitor/learn/quick-create-workspace.md).

* Ett integrations konto som är länkat till din Logic app. Lär dig [hur du skapar ett integrations konto med en länk till din Logic app](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Aktivera diagnostikloggning

Du kan aktivera loggning antingen direkt från ditt integrations konto eller [via tjänsten Azure Monitor](#azure-monitor-service). Azure Monitor ger grundläggande övervakning med data på infrastruktur nivå. Läs mer om [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Aktivera loggning från integrations konto

1. I [Azure Portal](https://portal.azure.com)letar du reda på och väljer ditt integrations konto. Under **övervakning**väljer du **diagnostikinställningar**.

   ![Sök efter och välj ditt integrations konto, Välj diagnostikinställningar](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Nu hittar och väljer du ditt integrations konto. I filter listor väljer du de värden som ska gälla för ditt integrations konto.
När du är klar väljer du **Lägg till diagnostisk inställning**.

   | Egenskap | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-prenumerationsnamn*> | Den Azure-prenumeration som är associerad med ditt integrations konto | 
   | **Resursgrupp** | <*Azure-resurs-grupp-namn* > | Azure-resurs gruppen för ditt integrations konto | 
   | **Resurstyp** | **Integrationskonton** | Typen för den Azure-resurs där du vill aktivera loggning | 
   | **Resurs** | <*integration – konto namn* > | Namnet på den Azure-resurs där du vill aktivera loggning | 
   ||||  

   Exempel:

   ![Konfigurera diagnostik för ditt integrations konto](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Ange ett namn för den nya diagnostikinställningar och välj Log Analytics arbets ytan och de data som du vill logga.

   1. Välj **Skicka till Log Analytics**. 

   1. Under **Log Analytics**väljer du **Konfigurera**. 

   1. Under **OMS-arbetsytor**väljer du den Log Analytics arbets yta som du vill använda för loggning. 

      > [!NOTE]
      > OMS-arbetsytor ersätts med Log Analytics-arbetsytor. 

   1. Under **logg**väljer du kategorin **IntegrationAccountTrackingEvents** och väljer **Spara**.

   Exempel: 

   ![Konfigurera Azure Monitor loggar så att du kan skicka diagnostikdata till en logg](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Nu [ska du konfigurera spårning för dina B2B-meddelanden i Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Aktivera loggning via Azure Monitor

1. I [Azure Portal](https://portal.azure.com)väljer du **övervaka**på huvud menyn i Azure. Under **Inställningar**väljer du **diagnostikinställningar**. 

   ![Välj "övervaka" > "diagnostikinställningar" > ditt integrations konto](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Nu hittar och väljer du ditt integrations konto. I filter listor väljer du de värden som ska gälla för ditt integrations konto.
När du är klar väljer du **Lägg till diagnostisk inställning**.

   | Egenskap | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-prenumerationsnamn*> | Den Azure-prenumeration som är associerad med ditt integrations konto | 
   | **Resursgrupp** | <*Azure-resurs-grupp-namn* > | Azure-resurs gruppen för ditt integrations konto | 
   | **Resurstyp** | **Integrationskonton** | Typen för den Azure-resurs där du vill aktivera loggning | 
   | **Resurs** | <*integration – konto namn* > | Namnet på den Azure-resurs där du vill aktivera loggning | 
   ||||  

   Exempel:

   ![Konfigurera diagnostik för ditt integrations konto](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Ange ett namn för den nya diagnostikinställningar och välj Log Analytics arbets ytan och de data som du vill logga.

   1. Välj **Skicka till Log Analytics**. 

   1. Under **Log Analytics**väljer du **Konfigurera**. 

   1. Under **OMS-arbetsytor**väljer du den Log Analytics arbets yta som du vill använda för loggning. 

      > [!NOTE]
      > OMS-arbetsytor ersätts med Log Analytics-arbetsytor. 

   1. Under **logg**väljer du kategorin **IntegrationAccountTrackingEvents** och väljer **Spara**.

   Exempel: 

   ![Konfigurera Azure Monitor loggar så att du kan skicka diagnostikdata till en logg](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Nu [ska du konfigurera spårning för dina B2B-meddelanden i Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Använda diagnostikdata med andra tjänster

Tillsammans med Azure Monitor loggar kan du utöka hur du använder dina Logic Apps diagnostikdata med andra Azure-tjänster, till exempel: 

* [Arkivera Azure-diagnostik loggar i Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Strömma Azure-diagnostik loggar till Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Du kan sedan få real tids övervakning genom att använda telemetri och analys från andra tjänster, t. ex. [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) och [Power BI](../azure-monitor/platform/powerbi.md). Exempel:

* [Strömma data från Event Hubs till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysera strömmande data med Stream Analytics och skapa en instrument panel för real tids analys i Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Kontrol lera att du först [skapar ett Azure Storage-konto](../storage/common/storage-create-storage-account.md) eller [skapa en Azure Event Hub](../event-hubs/event-hubs-create.md)baserat på de alternativ du vill konfigurera. Du kan sedan välja de destinationer där du vill skicka diagnostikdata.
Kvarhållning gäller endast när du väljer att använda ett lagrings konto.

![Skicka data till Azure Storage-konto eller händelsehubben](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Spårnings scheman som stöds

Azure stöder dessa spårnings schema typer, som alla har fasta scheman förutom den anpassade typen.

* [AS2-spårningsschema](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsschema](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassat spårningsschema](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Spåra B2B-meddelanden i Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Spåra B2B-meddelanden i Azure Monitor loggar")
* [Läs mer om Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise-integrationspaket")

