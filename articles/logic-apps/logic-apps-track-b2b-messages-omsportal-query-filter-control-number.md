---
title: Skapa spårnings frågor för B2B-meddelanden – Azure Logic Apps
description: Skapa frågor som spårar AS2-, X12-och EDIFACT-meddelanden i Azure Log Analytics för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 7d7bb53d24a113ea78b5bac3f9682fbb61ce2de9
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680101"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Skapa spårnings frågor för B2B-meddelanden i Azure Monitor loggar för Azure Logic Apps

Om du vill hitta AS2-, X12-eller EDIFACT-meddelanden som du spårar med [Azure Monitor loggar](../log-analytics/log-analytics-overview.md)kan du skapa frågor som filtrerar åtgärder baserat på vissa kriterier. Du kan till exempel hitta meddelanden baserat på ett speciellt utbytes kontroll nummer.

> [!NOTE]
> Den här sidan beskriver tidigare steg för hur du utför dessa uppgifter med Microsoft Operations Management Suite (OMS), som tas [ur bruk i januari 2019](../azure-monitor/platform/oms-portal-transition.md), ersätter dessa steg med Azure Log Analytics i stället. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav

* En Logic-app som är konfigurerad med diagnostikloggning. Lär dig [hur du skapar en logisk app](quickstart-create-first-logic-app-workflow.md) och [hur du ställer in loggning för den Logic app](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Ett integrations konto som har kon figurer ATS med övervakning och loggning. Lär dig [hur du skapar ett integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [hur du konfigurerar övervakning och loggning för det kontot](../logic-apps/logic-apps-monitor-b2b-message.md).

* Om du inte redan har gjort det, [publicera diagnostikdata till Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) och [Konfigurera meddelande spårning i Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Skapa frågor med filter

Om du vill hitta meddelanden baserat på vissa egenskaper eller värden kan du skapa frågor som använder filter. 

1. I [Azure-portalen](https://portal.azure.com), väljer du **Alla tjänster**. Sök efter "Log Analytics" i sökrutan och välj **Log Analytics**.

   ![Välj Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Under **Log Analytics**, leta upp och välj arbets ytan Log Analytics. 

   ![Välj Log Analytics arbets yta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Välj antingen **loggar (klassisk)** eller **loggar**under **Allmänt**på arbets ytans meny. 

   Det här exemplet visar hur du använder vyn klassiska loggar. 
   Om du väljer **Visa loggar** i avsnittet **maximera ditt Log Analytics Experience** , under **Sök och analysera loggar**, hämtar du **loggarna (klassisk vy)** . 

   ![Visa klassiska loggar](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. I redigerings rutan fråga börjar du skriva det fält namn som du vill söka efter. När du börjar skriva, visar Frågeredigeraren de möjliga matchningar och åtgärder som du kan använda. När du har skapat din fråga väljer du **Kör** eller trycker på RETUR-tangenten.

   Det här exemplet söker efter matchningar på **LogicAppB2B**. 
   Lär dig mer om [hur du hittar data i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md).

   ![Börja skriva frågesträngen](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Om du vill ändra den tidsram som du vill visa väljer du från listan varaktighet eller drar skjutreglaget i det vänstra fönstret. 

   ![Ändra tidsram](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Om du vill lägga till ett filter i frågan väljer du **Lägg till**. 

   ![Lägg till filter i fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. Under **Lägg till filter**anger du det filter namn som du vill söka efter. Om du hittar filtret väljer du det filtret. I den vänstra rutan väljer du **Lägg till** igen.

   Här är ett exempel på en annan fråga som söker efter **typ = = "AzureDiagnostics"** -händelser och hittar resultat baserat på utbytes kontroll numret genom att välja filtret **event_record_messageProperties_interchangeControlNumber_s** .

   ![Välj filter värde](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   När du har valt **Lägg till**uppdateras frågan med den valda filter händelsen och det aktuella värdet. 
   Dina tidigare resultat har nu filtrerats. 

   Den här frågan söker till exempel efter **typ = = "AzureDiagnostics"** och hittar resultat baserat på ett utbytes kontroll nummer med hjälp av **event_record_messageProperties_interchangeControlNumber_s** -filtret.

   ![Filtrerade resultat](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Spara fråga

Följ dessa steg om du vill spara din fråga i **loggar (klassisk)** vy:

1. Från din fråga på sidan **loggar (klassisk)** väljer du **analys**. 

   ![Välj "analys"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. I verktygsfältet fråga väljer du **Spara**.

   ![Välja ”Save” (Spara)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Ange information om frågan, till exempel ge frågan ett namn, Välj **fråga**och ange ett kategori namn. När du är klar väljer du **Spara**.

   ![Välja ”Save” (Spara)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Gå tillbaka till sidan fråga om du vill visa sparade frågor. I verktygsfältet fråga väljer du **sparade sökningar**.

   ![Välj "sparade sökningar"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Under **sparade sökningar**väljer du din fråga så att du kan visa resultatet. 

   ![Välj din fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Om du vill uppdatera frågan så att du kan hitta olika resultat kan du redigera frågan.

## <a name="find-and-run-saved-queries"></a>Hitta och köra sparade frågor

1. I [Azure-portalen](https://portal.azure.com), väljer du **Alla tjänster**. Sök efter "Log Analytics" i sökrutan och välj **Log Analytics**.

   ![Välj Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Under **Log Analytics**, leta upp och välj arbets ytan Log Analytics. 

   ![Välj Log Analytics arbets yta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Välj antingen **loggar (klassisk)** eller **loggar**under **Allmänt**på arbets ytans meny. 

   Det här exemplet visar hur du använder vyn klassiska loggar. 

1. När sidan fråga öppnas i verktygsfältet fråga väljer du **sparade sökningar**.

   ![Välj "sparade sökningar"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Under **sparade sökningar**väljer du din fråga så att du kan visa resultatet. 

   ![Välj din fråga](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   Frågan körs automatiskt, men om frågan inte körs av någon anledning går du till Frågeredigeraren och väljer **Kör**.

## <a name="next-steps"></a>Nästa steg

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade spårnings scheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)