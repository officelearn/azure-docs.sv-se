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
ms.date: 10/11/2018
ms.openlocfilehash: 177c361734a88acab5fc10d6b460645be82bf437
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457150"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Övervaka och få insikter om logikapp-körningar med Log Analytics

Övervaknings- och mer omfattande felsökning information kan du aktivera Log Analytics på samma gång när du skapar en logikapp. Log Analytics tillhandahåller diagnostik loggning och övervakning för din logikapp körs via Azure portal. När du lägger till Logic Apps-hantering-lösningen kan du få aggregerad status för logikapp-körningar och specifik information som status, körningstid, omsändning status och Korrelations-ID: N.

Den här artikeln visar hur du aktiverar Log Analytics så att du kan visa körningshändelser och data för din logikapp körs.

 > [!TIP]
 > För att övervaka dina befintliga logikappar, följer du dessa steg för att [aktivera Diagnostisk loggning och skicka logic app runtime-data till Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Krav

Innan du börjar måste du har en Log Analytics-arbetsyta. Lär dig [hur du skapar en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Aktivera diagnostikloggning när du skapar logikappar

1. I [Azure-portalen](https://portal.azure.com), skapa en logikapp. Välj **skapa en resurs** > **integrering** > **Logikapp**.

   ![Skapa en logikapp](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. Under **skapa en logikapp**, utföra åtgärderna som visas:

   1. Ange ett namn för din logikapp och välj din Azure-prenumeration. 

   1. Skapa eller välj en Azure-resursgrupp.

   1. Ange **Log Analytics** till **på**. 

   1. Markera arbetsytan där du vill skicka data för din logikapp körs från listan lista Log Analytics-arbetsyta. 

      ![Skapa en logikapp](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      När du har slutfört det här steget skapar Azure logikappen, som nu är kopplad till Log Analytics-arbetsytan. 
      Det här steget installerar också automatiskt Logic Apps-hantering-lösningen i din arbetsyta.

   1. När du är klar väljer du **Skapa**.

1. Visa din logikapp körs [fortsätter du med dessa steg](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>Installera lösningen för Logic Apps-hantering

Om du redan aktiverat Log Analytics när du skapade din logikapp kan du hoppa över det här steget. Du har redan en lösning för Logic Apps-hantering installerad.

1. I [Azure-portalen](https://portal.azure.com), väljer du **Alla tjänster**. I sökrutan anger du ”log analytics” som filter och välj **Log Analytics**.

   ![Välj ”Log Analytics”](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Under **Log Analytics**, hitta och välj din Log Analytics-arbetsyta. 

   ![Välj Log Analytics-arbetsytan](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Under **konfigurera övervakningslösningar**, Välj **visa lösningar**.

   ![Välj ”Visa lösningar”](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. På sidan Översikt väljer **Lägg till**, vilket öppnar den **hanteringslösningar** lista. Välj den listan **Logic Apps-hantering**. 

   ![Välj ”Logic Apps-hantering”](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Om du inte hittar lösningen längst ned i listan, Välj **Läs in fler** tills lösningen visas.

1. Välj **skapa**, som installerar lösningen.

   ![Välj ”Lägg till” för ”Logic Apps-hantering”](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-runs-in-log-analytics-workspace"></a>Visa logic app körs i Log Analytics-arbetsyta

1. För att visa antalet och status för din logikapp-körningar, gå till Log Analytics-arbetsytan och öppna sidan Översikt. 

   Information om dina logikappskörningar visas på den **Logic Apps-hantering** panelen. Om du vill visa en sammanfattning med mer information om dina logikappskörningar, Välj den **Logic Apps-hantering** panelen. 

   ![Översikt över panelen som visar logic app kör antal och status](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   Här kan är din logikapp-körningar grupperade efter namn eller efter Körningsstatus. 
   Information om fel visas i åtgärder eller utlösare för de logikapp-körningarna också i den här sidan.

   ![Översikt över statusen för din logikapp körs](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. Välj raden för en logikapp eller ett status om du vill visa alla körningar för en specifik logikapp eller status.

   Här är ett exempel som visar alla körningar för en specifik logikapp:

   ![Visa körs i en logikapp eller ett status](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Den här sidan har dessa avancerade alternativ:

   * **Spårade egenskaper:**

     Denna kolumn visar spårade egenskaper, som är grupperade efter åtgärder för logikappen. Välj för att visa de spårade egenskaperna **visa**. 
     Om du vill söka i de spårade egenskaperna, använder du filtret för kolumnen.
   
     ![Visa spårade egenskaperna för en logikapp](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Alla nytillagda spårade egenskaper kan ta 10 – 15 minuter innan de kan visas första gången. Lär dig [hur du lägger till spårade egenskaperna i logikappen](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Skicka:** du skicka minst en logikapp-körningar som har misslyckats, lyckades, eller är fortfarande körs. Markera kryssrutorna för körningar som du vill skicka igen och välj **skicka**. 

     ![Skicka logikapp-körningar](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Du kan utföra både på klientsidan och serversidan filtrering för att filtrera resultaten.

   * **Klientsidan filter**: för varje kolumn väljer du de filter som du vill, till exempel:

     ![Exempel kolumnfiltren](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Serversidan filter**: att välja ett specifikt tidsfönster eller begränsa antalet körningar som visas måste du använda scope-kontroll högst upp på sidan. Som standard visas endast 1 000 poster i taget.
   
     ![Ändra tidsfönstret](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. Om du vill visa alla åtgärder och deras information för en specifik körning, markerar du en rad för en logikappskörning.

   Här är ett exempel som visar alla åtgärder för en specifik logikapp-körningen:

   ![Visa åtgärder för en logikappskörning](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. På sidan alla resultat kan du se frågan bakom resultaten eller för att se alla resultat väljer **finns i alla**, vilket öppnar du loggsökningssidan.
   
   ![Se alla på resultatsidor](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   På sidan Log Search

   * Om du vill visa resultatet av frågan i en tabell, Välj **tabell**.

   * Om du vill ändra frågan kan du redigera frågesträngen i sökfältet. 
   För en bättre användarupplevelse och välja **Advanced Analytics**.

     ![Visa åtgärder och information för en logikappskörning](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     På sidan Azure Log Analytics kan du uppdatera frågor och visa resultat från tabellen. Den här frågan använder [Kusto-frågespråket](https://aka.ms/LogAnalyticsLanguageReference), som du kan redigera om du vill visa olika resultat. 

     ![Azure Log Analytics - frågevyn](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden](../logic-apps/logic-apps-monitor-b2b-message.md)