---
title: Visa och skapa frågor för logikappar i Azure Monitor-loggar
description: Visa och skapa frågor i Azure Monitor-loggar för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908077"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Visa och skapa frågor för övervakning och spårning i Azure Monitor-loggar för Azure Logic Apps

Du kan visa underliggande frågor som ger resultat från [Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) och skapa frågor som filtrerar resultaten baserat på dina specifika kriterier. Du kan till exempel hitta meddelanden baserat på ett visst utbyteskontrollnummer. Frågor använder [frågespråket Kusto](https://aka.ms/LogAnalyticsLanguageReference)som du kan redigera om du vill visa olika resultat. Mer information finns i [Azure Monitor-loggfrågor](../azure-monitor/log-query/query-language.md).

## <a name="prerequisites"></a>Krav

* En Log Analytics-arbetsyta. Om du inte har en Log Analytics-arbetsyta kan du läsa om hur du [skapar en Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md).

* En logikapp som konfigureras med Azure Monitor-loggning och skickar den informationen till en Log Analytics-arbetsyta. Lär dig hur du [konfigurerar Azure Monitor-loggar för logikappen](../logic-apps/monitor-logic-apps.md).

* Om du använder ett integrationskonto kontrollerar du att du har konfigurerat kontot med Azure Monitor-loggning för att skicka informationen till en Log Analytics-arbetsyta. Lär dig hur du [konfigurerar Azure Monitor-loggning för ditt integrationskonto](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Visa frågor bakom resultat

Så här visar eller redigerar du frågan som ger resultaten i sammanfattningen av arbetsytan:

1. På en resultatsida längst ned väljer du **Visa alla**.

   ![Se alla resultat](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Sidan Loggar öppnas och visar frågan bakom föregående resultatsida.

   ![Logs sida - frågevy](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. På sidan **Loggar** kan du välja följande alternativ:

   * Om du vill visa frågeresultaten som en tabell väljer du **Tabell**under frågeredigeraren .

   * Om du vill ändra frågan uppdaterar du frågesträngen och väljer **Kör** för att visa resultaten i tabellen.

## <a name="create-your-own-query"></a>Skapa en egen fråga

Om du vill söka efter eller filtrera resultat baserat på specifika egenskaper eller värden kan du skapa en egen fråga genom att starta från en tom fråga eller använda en befintlig fråga. Mer information finns i [Komma igång med loggfrågor i Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

1. Leta reda på och välj arbetsytan Log Analytics i [Azure-portalen.](https://portal.azure.com)

1. Välj **Loggar**under **Allmänt**på arbetsytans meny.

1. Börja från en tom fråga eller tillgängliga befintliga frågor.

   * Om du vill kontrollera om det finns några befintliga frågor väljer du antingen **Exempelfrågorhistorik** > **History**i frågeverktygsfältet , som visar frågor från tidigare frågekörningar eller väljer **Frågeutforskaren**, som visar fördefinierade frågor.

     Logic Apps B2B-lösningen innehåller till exempel dessa fördefinierade frågor:

     ![Börja med "Logic Apps B2B"-lösning fördefinierade frågor](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Om du vill börja från en tom fråga börjar du skriva [om frågespråket Kusto](../azure-monitor/log-query/query-language.md) för frågan i frågeredigeraren.

     ![Börja med tom fråga](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Nästa steg

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade spårningsscheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)