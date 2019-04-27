---
title: Skapa frågor om att spåra B2B-meddelanden i Azure Monitor-loggar – Azure Logic Apps | Microsoft Docs
description: Skapa frågor som spårar AS2-, X 12 och EDIFACT-meddelanden i Azure Log Analytics för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846619"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Skapa frågor om att spåra B2B-meddelanden i Azure Monitor-loggar för Azure Logic Apps

Om du vill hitta AS2 X12 eller EDIFACT meddelanden du spårar med [Azure Monitor loggar](../log-analytics/log-analytics-overview.md), du kan skapa frågor som filtrerar åtgärder baserat på specifika villkor. Du kan till exempel hitta meddelanden baserat på en specifik interchange-kontrollnummer.

> [!NOTE]
> Den här sidan som beskrevs tidigare anvisningar att utföra dessa uppgifter med den Microsoft Operations Management Suite (OMS), vilket är [tas ur bruk i januari 2019](../azure-monitor/platform/oms-portal-transition.md), ersätter de här stegen med Azure Log Analytics i stället. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* En logikapp som har konfigurerats med diagnostikloggning. Lär dig [så här skapar du en logikapp](quickstart-create-first-logic-app-workflow.md) och [hur du ställer in loggning för den logikappen](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Ett integrationskonto som har konfigurerats med övervakning och loggning. Lär dig [hur du skapar ett integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [hur du konfigurerar övervakning och loggning för det kontot](../logic-apps/logic-apps-monitor-b2b-message.md).

* Om du inte redan gjort [publicera diagnostiska data till Azure Monitor-loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) och [konfigurera meddelandespårning i Azure Monitor-loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Skapa frågor med filter

Du kan skapa frågor som använder filter för att hitta meddelanden baserat på specifika egenskaper eller värden. 

1. I [Azure-portalen](https://portal.azure.com), väljer du **Alla tjänster**. Hitta ”log analytics” i sökrutan och välj **Log Analytics**.

   ![Välj Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Under **Log Analytics**, hitta och välj din Log Analytics-arbetsyta. 

   ![Välj Log Analytics-arbetsyta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. På din arbetsyta-menyn, under **Allmänt**, väljer du antingen **loggar (klassisk)** eller **loggar**. 

   Det här exemplet visar hur du använder den klassiska loggar vyn. 
   Om du väljer **visa loggar** i den **maximera din Log Analytics-upplevelse** under avsnittet **Sök och analysera loggar**, du får den **loggar (klassiskt läge)** . 

   ![Visa klassiska loggar](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. I frågan redigeringsruta, börja skriva fältnamn som du vill söka efter. När du börjar skriva, visar frågeredigeraren möjliga matchningar och åtgärder som du kan använda. När du har skapat din fråga väljer **kör** eller tryck på RETUR-tangenten.

   Det här exemplet söker efter matchningar på **LogicAppB2B**. 
   Läs mer om [hitta data i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md).

   ![Börja skriva frågesträng](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Om du vill ändra en mer specifik tidsram som du vill visa i den vänstra rutan, Välj från listan över varaktighet eller dra skjutreglaget. 

   ![Ändra tidsramen](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Om du vill lägga till ett filter i frågan, Välj **Lägg till**. 

   ![Lägg till filter i frågan](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. Under **Lägg till filter**, ange filternamnet som du vill söka efter. Om du hittar filtret väljer du filtret. I den vänstra rutan väljer **Lägg till** igen.

   Här är till exempel en annan fråga som söker på **typ == ”AzureDiagnostics”** händelser och söker efter resultat baserat på interchange-kontrollnummer genom att välja den **event_record_messageProperties_ interchangeControlNumber_s** filter.

   ![Välj filtervärde](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   När du har valt **Lägg till**, frågan uppdateras med din händelsen valda filter och värde. 
   Dina tidigare resultat filtreras nu för. 

   Exempelvis kan den här frågan söker efter **typ == ”AzureDiagnostics”** och söker efter resultat baserat på ett interchange-kontrollnummer med hjälp av den **event_record_messageProperties_interchangeControlNumber_s**filter.

   ![Filtrerade resultat](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Spara fråga

Spara frågan i **loggar (klassisk)** visa, Följ dessa steg:

1. Från din fråga på den **loggar (klassisk)** väljer **Analytics**. 

   ![Välj ”Analytics”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Välj fråga-verktygsfältet **spara**.

   ![Välja ”Save” (Spara)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Ange information om din fråga, till exempel, namnge din fråga, väljer **fråga**, och ange ett kategorinamn. När du är klar väljer du **Spara**.

   ![Välja ”Save” (Spara)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Gå tillbaka till sidan om du vill visa sparade frågor. Välj fråga-verktygsfältet **sparade sökningar**.

   ![Välj ”sparade sökningar”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Under **sparade sökningar**, Välj din fråga så att du kan visa resultatet. 

   ![Välj din fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Uppdatera frågan så att du kan hitta olika resultat genom att redigera frågan.

## <a name="find-and-run-saved-queries"></a>Hitta och köra sparade frågor

1. I [Azure-portalen](https://portal.azure.com), väljer du **Alla tjänster**. Hitta ”log analytics” i sökrutan och välj **Log Analytics**.

   ![Välj Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Under **Log Analytics**, hitta och välj din Log Analytics-arbetsyta. 

   ![Välj Log Analytics-arbetsyta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. På din arbetsyta-menyn, under **Allmänt**, väljer du antingen **loggar (klassisk)** eller **loggar**. 

   Det här exemplet visar hur du använder den klassiska loggar vyn. 

1. När sidan öppnas i verktygsfältet frågan väljer **sparade sökningar**.

   ![Välj ”sparade sökningar”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Under **sparade sökningar**, Välj din fråga så att du kan visa resultatet. 

   ![Välj din fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   Frågan körs automatiskt, men om frågan kan inte köras av någon anledning i frågeredigeraren väljer **kör**.

## <a name="next-steps"></a>Nästa steg

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade spårningsscheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)