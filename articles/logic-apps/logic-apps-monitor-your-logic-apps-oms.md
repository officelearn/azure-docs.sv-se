---
title: Övervaka Logic Apps med Azure Monitor
description: Få insikter och felsöknings data för att felsöka och diagnostisera Logi Kap par körningar med Azure Monitor loggar
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: 305b50c86a468354f049fcc57fcb79b537e8dfed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791891"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Hämta insikter och fel söknings data för logi Kap par genom att använda Azure Monitor loggar

Om du vill övervaka och få bättre fel söknings information om dina Logi Kap par aktiverar du [Azure Monitor loggar](../log-analytics/log-analytics-overview.md) när du skapar din Logic app. Azure Monitor loggar tillhandahåller diagnostikloggning och övervakning för dina Logi Kap par när du installerar Logic Apps hanterings lösning i Azure Portal. Den här lösningen ger också aggregerad information för din Logic app som körs med information som status, körnings tid, status för återsändning och korrelations-ID. Den här artikeln visar hur du aktiverar Azure Monitor loggar så att du kan visa körnings händelser och data för din Logic app-körning.

I det här avsnittet visas hur du konfigurerar Azure Monitor loggar när du skapar din Logic app. Aktivera Azure Monitor loggar för befintliga Logi Kap par genom att följa de här stegen för att [Aktivera diagnostisk loggning och skicka körnings data för Logic app till Azure Monitor loggar](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Den här sidan beskriver tidigare steg för hur du utför dessa uppgifter med Microsoft Operations Management Suite (OMS), som [dras tillbaka i januari 2019](../azure-monitor/platform/oms-portal-transition.md)och ersätter dessa steg med [Azure Monitor loggar](../azure-monitor/platform/data-platform-logs.md)som ersatte termen Log Analytics. Loggdata lagras fortfarande i en Log Analytics arbets yta och samlas in och analyseras fortfarande av samma Log Analytics-tjänst. Mer information finns i [Azure Monitor terminologi ändringar](../azure-monitor/terminology.md).

## <a name="prerequisites"></a>Krav

Innan du börjar måste du ha en Log Analytics-arbetsyta. Lär dig [hur du skapar en arbets yta för Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="turn-on-logging-for-new-logic-apps"></a>Aktivera loggning för nya Logic Apps

1. Skapa din Logic app i [Azure Portal](https://portal.azure.com). Från huvud menyn i Azure väljer du **skapa en resurs** > **integration** > **Logic app**.

   ![Skapa ny Logic-app](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. Följ de här stegen under **Logic app**:

   1. Ange ett namn för din Logic app och välj din Azure-prenumeration.

   1. Skapa eller Välj en Azure-resurs grupp. Välj plats för din Logic app.

   1. Under **Log Analytics**väljer du **på**.

   1. I listan **Log Analytics arbets yta** väljer du den arbets yta där du vill skicka data från din Logic app-körningar.

      ![Ange information om din logikapp](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      När du har slutfört det här steget skapar Azure din Logic app, som nu är kopplad till din Log Analytics-arbetsyta. Det här steget installerar också Logic Apps hanterings lösningen i din arbets yta.

   1. När du är klar väljer du **Skapa**.

1. [Fortsätt med de här stegen](#view-logic-app-runs-oms)om du vill visa dina Logic app-körningar.

## <a name="install-logic-apps-management-solution"></a>Installera Logic Apps hanterings lösning

Hoppa över det här steget om du redan har konfigurerat Azure Monitor loggar när du skapade din Logic app. Du har redan den Logic Apps hanterings lösningen installerad.

1. I [Azure-portalen](https://portal.azure.com), väljer du **Alla tjänster**. Sök efter "Log Analytics-arbetsytor" i sökrutan och välj **Log Analytics arbets ytor**.

   ![Välj Log Analytics arbets ytor](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Under **Log Analytics arbets ytor**väljer du din arbets yta.

   ![Välj din Log Analytics arbets yta](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. I fönstret Översikt, under **Kom igång med Log Analytics** > **Konfigurera övervaknings lösningar**, väljer du **Visa lösningar**.

   ![Välj "Visa lösningar"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Under **Översikt**väljer du **Lägg till**.

   ![Välj "Lägg till"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. När **Marketplace** öppnas går du till rutan Sök och anger "Logic Apps Management" och väljer **Logic Apps hantering**.

   ![Välj Logic Apps hantering](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. I fönstret lösnings Beskrivning väljer du **skapa**.

   ![Välj "skapa" för "Logic Apps hantering"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. Granska och bekräfta Log Analytics arbets ytan där du vill installera lösningen och välj sedan **skapa** igen.

   ![Välj "skapa" för "Logic Apps hantering"](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   När Azure har distribuerat lösningen till den Azure-resurs grupp som innehåller din Log Analytics arbets yta, visas lösningen i arbets ytans sammanfattnings fönster.

   ![Fönstret Sammanfattning av arbets yta](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Visa körnings information för Logic app

När din Logic app har körts kan du Visa status och antal för dessa körningar på panelen **Logic Apps hantering** .

1. Gå till Log Analytics arbets ytan och välj **Sammanfattning för arbets ytor** > **Logic Apps hantering**.

   ![Körnings status och antal för Logic app](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   Här kommer din Logic app att köras grupperat efter namn eller körnings status. Den här sidan visar också information om felen i åtgärder eller utlösare för Logic app-körningar.

   ![Status sammanfattning för din Logic App körs](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. Om du vill visa alla körningar för en speciell Logic app eller status väljer du raden för en Logic app eller en status.

   Här är ett exempel som visar alla körningar för en speciell Logic-app:

   ![Visa körnings-och status för Logic app](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Den här sidan har avancerade alternativ: 

   * Kolumnen **spårade egenskaper** : för en Logic-app där du ställer in spårade egenskaper, som är grupperade efter åtgärder, kan du visa dessa egenskaper från den här kolumnen. Om du vill visa de här spårade egenskaperna väljer du **Visa**. Om du vill söka efter de spårade egenskaperna använder du kolumn filtret.

      ![Visa spårade egenskaper för en Logic app](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      Alla nyligen tillagda spårade egenskaper kan ta 10-15 minuter innan de visas första gången. Lär dig [hur du lägger till spårade egenskaper i din Logic app](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Skicka igen**: du kan skicka om en eller flera Logic Apps-körningar som misslyckades, lyckades eller fortfarande körs. Markera kryss rutorna för de körningar som du vill skicka igen och välj sedan **Skicka igen**.

     ![Skicka om Logic app-körningar](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. För att filtrera resultaten kan du utföra både klient-och Server sidans filtrering.

   * **Filter på klient sidan**: för varje kolumn väljer du de filter som du vill använda, till exempel:

     ![Exempel kolumn filter](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filter på Server sidan**: om du vill välja en tids period eller begränsa antalet körningar som visas använder du kontrollen omfång överst på sidan. Som standard visas endast 1 000 poster i taget.

     ![Ändra tids perioden](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. Om du vill visa alla åtgärder och deras information för en speciell körning väljer du raden för en Logic app-körning.

   Här är ett exempel som visar alla åtgärder och utlösare för en speciell Logic app-körning:

   ![Visa åtgärder för en Logic app-körning](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. På alla resultat sidor kan du Visa frågan bakom resultaten eller Visa alla resultat genom att välja **Visa alla**, som öppnar sidan **loggar** .

   ![Visa alla resultat](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   På sidan **loggar** kan du välja följande alternativ:

   * Om du vill visa frågeresultaten i en tabell väljer du **tabell**.

   * Frågor använder [Kusto frågespråk](https://aka.ms/LogAnalyticsLanguageReference), som du kan redigera om du vill visa olika resultat. Om du vill ändra frågan uppdaterar du frågesträngen och väljer **Kör** för att visa resultaten i tabellen. 

     ![Log Analytics-frågevy](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden](../logic-apps/logic-apps-monitor-b2b-message.md)