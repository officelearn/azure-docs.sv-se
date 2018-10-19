---
title: Övervaka logic app körs med Log Analytics – Azure Logic Apps | Microsoft Docs
description: Få insikter och data om dina logikappskörningar med Log Analytics-felsökning för felsökning och diagnostik
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: 9fbb2c8d8563e88f6241b6b7ab6b3ddf08111914
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409058"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Övervaka och få insikter om logikapp-körningar med Log Analytics

Övervaknings- och mer omfattande felsökning information kan du aktivera Log Analytics på samma gång när du skapar en logikapp. Log Analytics tillhandahåller diagnostik loggning och övervakning för din logikapp körs via Azure portal. När du lägger till Logic Apps-hantering-lösningen kan du få aggregerad status för logikapp-körningar och specifik information som status, körningstid, omsändning status och Korrelations-ID: N.

Den här artikeln visar hur du aktiverar Log Analytics så att du kan visa körningshändelser och data för din logikapp körs.

 > [!TIP]
 > För att övervaka dina befintliga logikappar, följer du dessa steg för att [aktivera Diagnostisk loggning och skicka logic app runtime-data till Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Krav

Innan du börjar måste du har en Log Analytics-arbetsyta. Lär dig [hur du skapar en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Aktivera diagnostikloggning när du skapar logikappar

1. I [Azure-portalen](https://portal.azure.com), skapa en logikapp. Välj **skapa en resurs** > **Företagsintegrering** > **Logikapp**.

   ![Skapa en logikapp](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. I den **skapa en logikapp** utför dessa uppgifter som visas:

   1. Ange ett namn för din logikapp och välj din Azure-prenumeration. 
   2. Skapa eller välj en Azure-resursgrupp.
   3. Ange **Log Analytics** till **på**. 
   Välj Log Analytics-arbetsyta där du vill skicka data för din logikapp körs. 
   4. När du är klar kan du välja **fäst på instrumentpanelen** > **skapa**.

      ![Skapa en logikapp](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      När du har slutfört det här steget skapar Azure logikappen, som nu är kopplad till Log Analytics-arbetsytan. 
      Det här steget installerar också automatiskt Logic Apps-hantering-lösningen i din arbetsyta.

3. Visa din logikapp körs [fortsätter du med dessa steg](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>Installera lösningen för Logic Apps-hantering

Om du redan aktiverat Log Analytics när du skapade din logikapp kan du hoppa över det här steget. Du har redan en lösning för Logic Apps-hantering installerad.

1. I den [Azure-portalen](https://portal.azure.com), Välj **fler tjänster**. Sök efter ”log analytics” som filter och välj **Log Analytics** enligt:

   ![Välj ”Log Analytics”](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. Under **Log Analytics**, hitta och välj din Log Analytics-arbetsyta. 

   ![Välj Log Analytics-arbetsytan](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. Under **Management**, Välj **arbetsytan Sammanfattning**.

   ![Välj ”översikt”](media/logic-apps-monitor-your-logic-apps-oms/ibiza-portal-page.png)

4. På sidan Översikt väljer **Lägg till** att öppna panelen hanteringslösningar. 

   ![Välj ”Logic Apps-hantering”](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

5. Bläddra igenom listan över **hanteringslösningar**, Välj **Logic Apps-hantering** lösning, och välj **skapa** att installera den på sidan Översikt.

   ![Välj ”Lägg till” för ”Logic Apps-hantering”](media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-log-analytics-workspace"></a>Visa dina logikappskörningar i Log Analytics-arbetsytan

1. Om du vill visa antalet och status för din logikapp-körningar, går du till översiktssidan för Log Analytics-arbetsytan. Granska informationen på den **Logic Apps-hantering** panelen.

   ![Översikt över panelen som visar logic app kör antal och status](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

2. Om du vill visa en sammanfattning med mer information om dina logikappskörningar, Välj den **Logic Apps-hantering** panelen.

   Här kan är din logikapp-körningar grupperade efter namn eller efter Körningsstatus. Du kan också se information om fel i åtgärder eller utlösare för de logikapp-körningarna.

   ![Översikt över statusen för din logikapp körs](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. Välj raden för en logikapp eller ett status om du vill visa alla körningar för en specifik logikapp eller status.

   Här är ett exempel som visar alla körningar för en specifik logikapp:

   ![Visa körs i en logikapp eller ett status](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Det finns två avancerade alternativ på sidan:
   * **Spårade egenskaper:** denna kolumn visar spårade egenskaper, som är grupperade efter åtgärder för logikappen. Välj för att visa de spårade egenskaperna **visa**. Du kan söka de spårade egenskaperna med hjälp av kolumnfilter.
   
     ![Visa spårade egenskaperna för en logikapp](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Alla nytillagda spårade egenskaper kan ta 10 – 15 minuter innan de kan visas första gången. Lär dig [hur du lägger till spårade egenskaperna i logikappen](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Skicka:** du skicka minst en logikapp-körningar som har misslyckats, lyckades, eller är fortfarande körs. Markera kryssrutorna för körningar som du vill skicka igen och välj **skicka**. 

     ![Skicka logikapp-körningar](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

4. Du kan utföra både på klientsidan och serversidan filtrering för att filtrera resultaten.

   * Filter för klientsidan: för varje kolumn väljer du de filter som du vill. 
   Här följer några exempel:

     ![Exempel kolumnfiltren](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * Serversidan filter: att välja ett specifikt tidsfönster eller begränsa antalet körningar som visas måste du använda scope-kontroll högst upp på sidan. 
   Som standard visas endast 1 000 poster i taget. 
   
     ![Ändra tidsfönstret](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. Om du vill visa alla åtgärder och deras information för en specifik körning, markerar du en rad för en logikappskörning.

   Här är ett exempel som visar alla åtgärder för en specifik logikapp-körningen:

   ![Visa åtgärder för en logikappskörning](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
6. På sidan alla resultat kan du se frågan bakom resultaten eller för att se alla resultat väljer **finns i alla**, vilket öppnar du loggsökningssidan.
   
   ![Se alla på resultatsidor](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   På sidan Log Search
   * Om du vill visa resultatet av frågan i en tabell, Välj **tabell**.
   * Om du vill ändra frågan kan du redigera frågesträngen i sökfältet. 
   För en bättre användarupplevelse och välja **Advanced Analytics**.

     ![Visa åtgärder och information för en logikappskörning](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     På sidan Azure Log Analytics kan du här uppdatera frågor och visa resultat från tabellen. 
     Den här frågan använder [Kusto-frågespråket](https://aka.ms/LogAnalyticsLanguageReference), som du kan redigera om du vill visa olika resultat. 

     ![Azure Log Analytics - frågevyn](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden](../logic-apps/logic-apps-monitor-b2b-message.md)

