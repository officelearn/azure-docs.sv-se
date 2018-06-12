---
title: Övervaka och få insikter om din logikapp körs med logganalys - Azure Logic Apps | Microsoft Docs
description: Övervaka logik appen körs med logganalys få insikter och bättre felsökning information för felsökning och diagnostik
author: divyaswarnkar
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/9/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 493d82f59600d83a083bb13cdd0a21b872d77a43
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299951"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Övervaka och få insikter om logik appen körs med logganalys

För övervakning och bättre felsökningsinformation kan du aktivera logganalys samtidigt när du skapar en logikapp. Log Analytics tillhandahåller diagnostik loggning och övervakning för din logikapp körs via Azure-portalen. När du lägger till hanteringslösning för Logic Apps får du aggregerad status för din logik app körs och en specifik information som status, körningstid, omsändning status och Korrelations-ID: N.

Det här avsnittet beskrivs hur du aktiverar logganalys så att du kan visa runtime händelser och data för din logikapp kör.

 > [!TIP]
 > Så här för att övervaka dina befintliga logikappar [aktivera diagnostikloggning och skicka logik app Körningsdata till logganalys](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Krav

Innan du börjar behöver du ha logganalys-arbetsytan. Läs [hur du skapar en logganalys-arbetsytan](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Aktivera diagnostikloggning när du skapar logikappar

1. I [Azure-portalen](https://portal.azure.com), skapa en logikapp. Välj **skapar du en resurs** > **Enterprise Integration** > **Logikapp**.

   ![Skapa en logikapp](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. I den **skapa logikapp** utför dessa uppgifter som visas:

   1. Ange ett namn för din logikapp och välj din Azure-prenumeration. 
   2. Skapa eller välj en Azure-resursgrupp.
   3. Ange **logga Analytics** till **på**. 
   Välj logganalys-arbetsyta där du vill skicka data för din logikapp körs. 
   4. När du är klar kan du välja **fäst på instrumentpanelen** > **skapa**.

      ![Skapa en logikapp](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      När du har slutfört det här steget Azure skapar din logikapp nu som är kopplade till logganalys-arbetsytan. 
      Det här steget installerar också automatiskt Logic Apps hanteringslösning i arbetsytan.

3. Visa din logikapp körs [fortsätta med de här stegen](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>Installera hanteringslösning för Logic Apps

Om du redan aktiverat logganalys när du skapade din logikapp, hoppar du över det här steget. Du har redan Logic Apps hanteringslösningen installerad.

1. I den [Azure-portalen](https://portal.azure.com), Välj **fler tjänster**. Sök efter ”logganalys” som filter och välja **logganalys** som visas:

   ![Välj ”logganalys”](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. Under **logganalys**, söka efter och välj logganalys-arbetsytan. 

   ![Välj logganalys-arbetsytan](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. Under **Management**, Välj **OMS-portalen**.

   ![Välj ”OMS-portalen”](media/logic-apps-monitor-your-logic-apps-oms/oms-portal-page.png)

4. Under **alla lösningar för**, söka efter och välj panelen för den **Logic Apps Management** lösning.

   ![Välj ”Logic Apps Management”](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-management-tile2.png)

5. Om du vill installera lösningen i logganalys-arbetsytan, Välj **Lägg till**.

   ![Välj ”Lägg till” för ”Logic Apps Management”](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-log-analytics-workspace"></a>Visa din logikapp som körs i logganalys-arbetsytan

1. Om du vill visa antalet och status för logik appen körs, går du till översiktssidan för logganalys-arbetsytan. Granska informationen på den **Logic Apps Management** panelen.

   ![Översikt över panelen visar logik app kör antal och status](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

2. Välj för att visa en sammanfattning med mer information om logik appen körs den **Logic Apps Management** panelen.

   Här kan grupperas logik appen körs efter namn eller Körstatus. Du kan också se information om fel i åtgärder eller utlösare för logic appen körs.

   ![Översikt över statusen för din logikapp körs](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. Om du vill visa alla Välj körs för en specifik logikapp eller status, raden för en logikapp eller status.

   Här är ett exempel som visar alla körs för en specifik logikapp:

   ![Vyn körs för en logikapp eller status](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Det finns två avancerade alternativ på sidan:
   * **Spåras egenskaper:** denna kolumn visar spårade egenskaper, som är grupperade efter åtgärder för logikappen. Välj för att visa egenskaperna spårade **visa**. Du kan söka spårade egenskaper med hjälp av kolumnen filtret.
   
     ![Visa spårade egenskaperna för en logikapp](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Alla nyligen tillagda spårade egenskaper kan ta 10 – 15 minuter innan de kan visas första gången. Läs [lägga till spårade egenskaper i din logikapp](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Skicka:** du kan skicka en eller flera logik appen körs som inte har installerats, eller är fortfarande körs. Markera kryssrutorna för Kör som du vill skicka och välj **skicka**. 

     ![Skicka logik appen körs](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

4. Du kan utföra både på klientsidan och serversidan filtrering för att filtrera de här resultaten returneras.

   * Klientsidans filter: Välj filtren som du vill använda för varje kolumn. 
   Här följer några exempel:

     ![Exempel kolumnfiltren](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * Serversidan filter: att välja ett specifikt tidsfönster eller begränsa antalet körs som visas använder du kontrollen scope överst på sidan. 
   Som standard visas endast 1 000 poster i taget. 
   
     ![Ändra tidsfönstret](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. Välj en rad för en logikapp kör om du vill visa alla åtgärder och information för en specifik kör.

   Här är ett exempel som visar alla åtgärder för en specifik logikapp som kör:

   ![Visa åtgärder för en logikapp kör](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
6. På sidan alla resultat kan du se frågan bakom resultaten eller för att se alla resultat, väljer du **finns alla**, som öppnas sidan Sök efter logg.
   
   ![Se alla på resultatsidor](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   På sidan Logga sökning
   * Om du vill visa resultatet av frågan i en tabell, Välj **tabellen**.
   * Om du vill ändra frågan kan du redigera frågesträngen i sökfältet. 
   En bättre upplevelse, Välj **Advanced Analytics**.

     ![Visa information för en logikapp som körs och åtgärder](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Här på Azure logganalys-sida kan du uppdatera frågor och visa resultat från tabellen. 
     Den här frågan använder [Kusto frågespråk](https://docs.loganalytics.io/docs/Language-Reference), som du kan redigera om du vill visa andra resultat. 

     ![Azure logganalys - frågevy](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden](../logic-apps/logic-apps-monitor-b2b-message.md)

