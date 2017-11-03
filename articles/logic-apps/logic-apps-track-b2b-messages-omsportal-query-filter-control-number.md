---
title: "Frågan för B2B-meddelanden i Operations Management Suite - Azure Logic Apps | Microsoft Docs"
description: "Skapa frågor för att spåra AS2-, X 12- och EDIFACT-meddelanden i Operations Management Suite"
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
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 2748d3d3daf7c13dca05f663a4a088598e1b3605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>Fråga efter AS2-, X 12- och EDIFACT-meddelanden i Microsoft Operations Management Suite (OMS)

Om du vill hitta AS2 X12 eller EDIFACT meddelanden som du spårar med [Azure logganalys](../log-analytics/log-analytics-overview.md) i den [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), du kan skapa frågor som filtrerar åtgärder baserat på specifika villkor. Du kan till exempel hitta meddelanden baserat på specifika interchange kontroll.

## <a name="requirements"></a>Krav

* En logikapp som har konfigurerats med diagnostikloggning. Läs [hur du skapar en logikapp](../logic-apps/logic-apps-create-a-logic-app.md) och [hur du ställer in loggning för att logikapp](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Ett integration-konto som har konfigurerats med övervakning och loggning. Läs [hur du skapar ett konto för integrering](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [hur du konfigurerar övervakning och loggning för det kontot](../logic-apps/logic-apps-monitor-b2b-message.md).

* Om du inte redan har gjort [publicera diagnostikdata till logganalys](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) och [konfigurera meddelandespårning i OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> När du har uppfyllt kraven tidigare, bör du ha en arbetsyta den [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Du bör använda samma OMS-arbetsyta för att spåra dina B2B-kommunikation i OMS. 
>  
> Lär dig mer om du inte har en OMS-arbetsyta [hur du skapar en OMS-arbetsyta](../log-analytics/log-analytics-get-started.md).

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>Skapa frågor med filter i Operations Management Suite-portalen

Det här exemplet visar hur du kan hitta meddelanden baserat på deras interchange kontrollnummer.

> [!TIP] 
> Om du vet OMS arbetsytans namn, gå till startsidan arbetsytan (`https://{your-workspace-name}.portal.mms.microsoft.com`), och starta i steg 4. Annars starta i steg 1.

1. I den [Azure-portalen](https://portal.azure.com), Välj **fler tjänster**. Sök efter ”logganalys” och välj sedan **logganalys** som visas här:

   ![Hitta logganalys](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. Under **logganalys**, söka efter och välj din OMS-arbetsyta.

   ![Välj din OMS-arbetsyta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. Under **Management**, Välj **OMS-portalen**.

   ![Välj OMS-portalen](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. Välj på startsidan OMS **loggen Sök**.

   ![På startsidan OMS väljer du ”loggen Sök”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   ELLER

   ![På menyn OMS väljer du ”loggen Sök”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. I sökrutan anger du ett fält som du vill söka efter och tryck på **RETUR**. När du börjar skriva in visas OMS möjliga matchningar och åtgärder som du kan använda. Lär dig mer om [hitta data i logganalys](../log-analytics/log-analytics-log-searches.md).

   Det här exemplet söker efter händelser med **typ = AzureDiagnostics**.

   ![Börja skriva frågesträng](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. Välj den tidsperiod som du vill visa i fältet till vänster. Om du vill lägga till ett filter i frågan, Välj **+ Lägg till**.

   ![Lägg till filter fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. Under **Lägg till filter**, ange filternamnet så att du kan hitta de filter som du vill använda. Välj filtret och välj **+ Lägg till**.

   Om du vill interchange kontroll, det här exemplet söker efter ordet ”interchange” och väljer **event_record_messageProperties_interchangeControlNumber_s** som filter.

   ![Välj filter](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. I fältet till vänster väljer filtervärde som du vill använda och välj **tillämpa**.

   Det här exemplet väljer interchange Kontrollnumret för meddelanden som vi vill.

   ![Välj filtervärdet](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. Gå nu tillbaka till den fråga som du utvecklar. Frågan har uppdaterats med ditt valda filter-händelsen och värdet. Tidigare resultaten filtreras nu för.

    ![Gå tillbaka till din fråga med filtrerade resultat](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Spara frågan för framtida användning

1. I frågan på den **loggen Sök** väljer **spara**. Namnge din fråga, Välj en kategori och välj **spara**.

   ![Ge din fråga namn och kategori](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Välj för att visa frågan **Favoriter**.

   ![Välj ”Favoriter”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. Under **sparade sökningar**, Markera frågan så att du kan visa resultaten. Uppdatera frågan så att du kan hitta olika resultat genom att redigera frågan.

   ![Välj din fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>Hitta och köra sparade frågor i Operations Management Suite-portalen

1. Öppna startsidan för OMS-arbetsytan (`https://{your-workspace-name}.portal.mms.microsoft.com`), och välj **loggen Sök**.

   ![På startsidan OMS väljer du ”loggen Sök”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   ELLER

   ![På menyn OMS väljer du ”loggen Sök”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. På den **loggen Sök** startsidan, Välj **Favoriter**.

   ![Välj ”Favoriter”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. Under **sparade sökningar**, Markera frågan så att du kan visa resultaten. Uppdatera frågan så att du kan hitta olika resultat genom att redigera frågan.

   ![Välj din fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Nästa steg

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Spårning av anpassade scheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)