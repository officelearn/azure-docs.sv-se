---
title: Skapa frågor för B2B-meddelanden i Log Analytics – Azure Logic Apps | Microsoft Docs
description: Skapa frågor som spårar AS2-, X 12 och EDIFACT-meddelanden med Log Analytics för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: baccd255fc2812eae0de3a98dfcef3dcbc7e1b46
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124278"
---
# <a name="create-queries-for-tracking-as2-x12-and-edifact-messages-in-log-analytics-for-azure-logic-apps"></a>Skapa frågor för att spåra AS2-, X 12 och EDIFACT-meddelanden i Log Analytics för Azure Logic Apps

Om du vill hitta AS2 X12 eller EDIFACT meddelanden du spårar med [Azure Log Analytics](../log-analytics/log-analytics-overview.md), du kan skapa frågor som filtrerar åtgärder baserat på specifika villkor. Du kan till exempel hitta meddelanden baserat på en specifik interchange-kontrollnummer.

## <a name="requirements"></a>Krav

* En logikapp som har konfigurerats med diagnostikloggning. Lär dig [så här skapar du en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) och [hur du ställer in loggning för den logikappen](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Ett integrationskonto som har konfigurerats med övervakning och loggning. Lär dig [hur du skapar ett integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [hur du konfigurerar övervakning och loggning för det kontot](../logic-apps/logic-apps-monitor-b2b-message.md).

* Om du inte redan gjort [publicera diagnostiska data till Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) och [konfigurera meddelandespårning i Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> När du har uppfyllt kraven tidigare, bör du ha en arbetsyta i Log Analytics. Du bör använda samma arbetsyta för att spåra dina B2B-kommunikation i Log Analytics. 
>  
> Om du inte har en Log Analytics-arbetsyta kan du läsa [hur du skapar en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="create-message-queries-with-filters-in-log-analytics"></a>Skapa frågor med filter i Log Analytics

Det här exemplet visar hur du kan hitta meddelanden baserat på deras interchange-kontrollnummer.

> [!TIP] 
> Om du vet ditt Log Analytics-Arbetsytenamn, gå till startsidan arbetsytan (`https://{your-workspace-name}.portal.mms.microsoft.com`), och börja med steg 4. Annars starta i steg 1.

1. I den [Azure-portalen](https://portal.azure.com), Välj **alla tjänster**. Sök efter ”log analytics” och välj sedan **Log Analytics** som visas här:

   ![Hitta Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. Under **Log Analytics**, hitta och välj din Log Analytics-arbetsyta.

   ![Välj Log Analytics-arbetsytan](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. Under **Management**, Välj **Loggsökning**.

   ![Välj Lo sökning](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/azure-portal-page.png)

4. I sökrutan anger du ett fält som du vill söka efter och tryck på **RETUR**. När du börjar skriva visar Log Analytics möjliga matchningar och åtgärder som du kan använda. Läs mer om [hitta data i Log Analytics](../log-analytics/log-analytics-log-searches.md).

   Det här exemplet söker efter händelser med **typ = AzureDiagnostics**.

   ![Börja skriva frågesträng](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

5. I det vänstra fältet väljer du en mer specifik tidsram som du vill visa. Om du vill lägga till ett filter i frågan, Välj **+ Lägg till**.

   ![Lägg till filter i frågan](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

6. Under **Lägg till filter**, ange namnet på filtret så att du kan hitta önskat filter. Välj filtret och välj **+ Lägg till**.

   Du hittar interchange-kontrollnummer genom det här exemplet söker efter ordet ”utbytet” och väljer **event_record_messageProperties_interchangeControlNumber_s** som i filtreringen.

   ![Välj filter](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

7. I det vänstra fältet väljer du det filtervärde som du vill använda och välj **tillämpa**.

   Det här exemplet väljer interchange-kontrollnummer för meddelanden som vi vill.

   ![Välj filtervärde](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

8. Gå nu tillbaka till den fråga som du skapar. Frågan har uppdaterats med händelsen valda filter och värde. Dina tidigare resultat filtreras nu för.

    ![Gå tillbaka till din fråga med filtrerade resultat](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Spara frågan för framtida användning

1. Från din fråga på den **Loggsökning** väljer **spara**. Namnge din fråga, Välj en kategori och välj **spara**.

   ![Ge din fråga ett namn och kategori](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Om du vill visa din fråga väljer **Favoriter**.

   ![Välj ”Favoriter”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. Under **sparade sökningar**, Välj din fråga så att du kan se resultaten. Uppdatera frågan så att du kan hitta olika resultat genom att redigera frågan.

   ![Välj din fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Hitta och köra sparade frågor i Log Analytics

1. Öppna startsidan för Log Analytics-arbetsyta (`https://{your-workspace-name}.portal.mms.microsoft.com`), och välj **Loggsökning**.

   ![På startsidan Log Analytics väljer du ”Log Search”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   ELLER

   ![På menyn, väljer du ”Log Search”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. På den **Loggsökning** startsidan, Välj **Favoriter**.

   ![Välj ”Favoriter”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. Under **sparade sökningar**, Välj din fråga så att du kan se resultaten. Uppdatera frågan så att du kan hitta olika resultat genom att redigera frågan.

   ![Välj din fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Nästa steg

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade spårningsscheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)