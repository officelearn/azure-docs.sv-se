---
title: Övervaka logikappar med Azure Monitor - loggar i Azure Logic Apps | Microsoft Docs
description: Få insikter och data för felsökning och diagnostisera dina logikappskörningar med Azure Log Analytics-felsökning
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3f890e6cabd757fdd38374befaaccd1a10c9bd96
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192470"
---
# <a name="monitor-logic-apps-with-azure-monitor-logs"></a>Övervaka logikappar med Azure Monitor-loggar

Om du vill övervaka och få bättre information om felsökning om logic apps, aktiverar [Azure Monitor loggar](../log-analytics/log-analytics-overview.md) när du skapar din logikapp. Azure Monitor-loggar har diagnostik loggning och övervakning för logic apps när du installerar lösningen Logic Apps-hantering i Azure-portalen. Den här lösningen ger också samlad information för din logikapp körs med specifik information som status, körningstid, omsändning status och Korrelations-ID: N. Den här artikeln visar hur du aktiverar Azure Monitor-loggar så att du kan visa körningshändelser och data för din logikapp körs.

Följ dessa steg för att aktivera Azure Monitor-loggar för befintliga logikappar genom [aktivera Diagnostisk loggning och skicka logic app runtime data till Azure Monitor-loggar](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Den här sidan som beskrevs tidigare anvisningar att utföra dessa uppgifter med den Microsoft Operations Management Suite (OMS), vilket är [tas ur bruk i januari 2019](../azure-monitor/platform/oms-portal-transition.md), ersätter de här stegen med Azure Log Analytics i stället. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du en Log Analytics-arbetsyta. Lär dig [hur du skapar en Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md). 

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

## <a name="install-logic-apps-management-solution"></a>Installera lösningen för Logic Apps-hantering

Om du redan har aktiverat Azure Monitor loggar när du har skapat din logikapp, hoppa över det här steget. Du har redan en lösning för Logic Apps-hantering installerad.

1. I [Azure-portalen](https://portal.azure.com), väljer du **Alla tjänster**. Hitta ”log analytics” i sökrutan och välj **Log Analytics**.

   ![Välj ”Log Analytics”](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Under **Log Analytics**, hitta och välj din Log Analytics-arbetsyta. 

   ![Välj Log Analytics-arbetsytan](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Under **Kom igång med Log Analytics** > **konfigurera övervakningslösningar**, Välj **visa lösningar**.

   ![Välj ”Visa lösningar”](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. På sidan Översikt väljer **Lägg till**, vilket öppnar den **hanteringslösningar** lista. Välj den listan **Logic Apps-hantering**. 

   ![Välj ”Logic Apps-hantering”](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Om du inte hittar lösningen längst ned i listan, Välj **Läs in fler** tills lösningen visas.

1. Välj **skapa**, bekräfta Log Analytics-arbetsyta där du vill installera lösningen och välj sedan **skapa** igen.   

   ![Välj ”Skapa” för ”Logic Apps-hantering”](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   Om du inte vill använda en befintlig arbetsyta kan du också skapa en ny arbetsyta just nu.

   När du är klar visas lösning för Logic Apps-hantering på översiktssidan. 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Visa logikappkörningen information

När logikappen körs kan du visa status och antal för de körs på den **Logic Apps-hantering** panelen. 

1. Gå till Log Analytics-arbetsytan och öppna sidan Översikt. Välj **Logic Apps-hantering**. 

   ![Logikappskörningen status och antal](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

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

   * **Skicka:** Du kan skicka en eller flera logikapp-körningar som har misslyckats, lyckades, eller körs fortfarande. Markera kryssrutorna för körningar som du vill skicka igen och välj **skicka**. 

     ![Skicka logikapp-körningar](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Du kan utföra både på klientsidan och serversidan filtrering för att filtrera resultaten.

   * **Klientsidan filter**: Välj de filter som du vill, till exempel för varje kolumn:

     ![Exempel kolumnfiltren](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Serversidan filter**: Att välja ett specifikt tidsfönster eller för att begränsa antalet körningar som visas, kan du använda scope-kontroll högst upp på sidan. Som standard visas endast 1 000 poster i taget.
   
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
     
     På sidan log analytics kan du uppdatera frågor och visa resultat från tabellen. Den här frågan använder [Kusto-frågespråket](https://aka.ms/LogAnalyticsLanguageReference), som du kan redigera om du vill visa olika resultat. 

     ![log analytics - frågevyn](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden](../logic-apps/logic-apps-monitor-b2b-message.md)