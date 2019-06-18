---
title: Schemalägg återkommande uppgifter med glidande fönstret utlösare – Azure Logic Apps
description: Schemalägga och köra återkommande automatiserade uppgifter och arbetsflöden med utlösare för glidande fönster i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299509"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>Skapa, schemalägga och kör återkommande uppgifter och arbetsflöden med utlösare för glidande fönster i Azure Logic Apps

För att köra aktiviteter, processer eller jobb som måste hantera data i segment om kontinuerlig regelbundet, kan du starta logikappens arbetsflöde med den **glidande fönstret - schema** utlösaren. Du kan ange ett datum och tid samt en tidszon för att starta arbetsflödet och en upprepning för Upprepa det här arbetsflödet. Om upprepningar missade oavsett orsak, bearbetar dessa missade upprepningar i den här utlösaren. Till exempel använda när du synkroniserar data mellan din databas och lagring av säkerhetskopior, utlösare för glidande fönster så att data som synkroniseras klusteruppgradering luckor. Mer information om inbyggda schema-utlösare och åtgärder som finns i [schema och kör återkommande automatiserade uppgifter och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Här följer några mönster som har stöd för den här utlösaren:

* Kör omedelbart och upprepa varje *n* antal sekunder, minuter eller timmar.

* Starta vid ett specifikt datum och tid, och sedan köra och upprepa varje *n* antal sekunder, minuter eller timmar. Du kan ange en starttid tidigare som kör alla tidigare upprepningar med den här utlösaren.

* Fördröja varje upprepning under en viss tid innan du kör.

Skillnader mellan den här utlösaren och upprepningsutlösaren eller mer information om att schemalägga återkommande arbetsflöden finns i [schema och kör återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Om du vill utlösa logikappen och kör bara en gång i framtiden, se [kör jobb bara en gång](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [logikappar](../logic-apps/logic-apps-overview.md). Om du är nybörjare till logic apps lär du dig [hur du skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Lägg till utlösare glidande fönster

1. Logga in på [Azure Portal](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic App Designer visas i sökrutan anger du ”skjutfönster” som filter. Välj den här utlösaren som det första steget i ditt logikapparbetsflöde från listan över utlösare: **Hoppande fönster**

   ![Välj ”glidande fönster”](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Ange intervall och frekvens för upprepningen. I det här exemplet anger du egenskaperna för att köra arbetsflödet varje vecka.

   ![Ange intervall och frekvens](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Egenskap | Obligatoriskt | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **Intervall** | Ja | interval | Integer | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på åtkomstfrekvensen. Här är de minsta och största intervall: <p>-Timme: 1 – 12 000 timmar </br>-Minut: 1-72,000 minuter </br>-Sekund: 1-9,999,999 sekunder<p>Om intervallet är 6 och frekvensen är ”Hour”, är upprepningen var sjätte timme. |
   | **Frekvens** | Ja | frequency | String | Tidsenhet för upprepningen: **Andra**, **minut**, eller **timme** |
   ||||||

   ![Avancerade upprepningsalternativ](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Mer upprepningsalternativ, öppna den **Lägg till ny parameter** lista. 
   Alla alternativ som du väljer visas på utlösaren efter markering.

   | Egenskap | Obligatoriskt | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **fördröjning** | Nej | delay | String | Varaktighet för att fördröja varje upprepning med hjälp av den [tidsangivelse för ISO 8601-datum](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Tidszon** | Nej | Tidszon | String | Gäller endast när du anger en starttid eftersom den här utlösaren inte acceptera [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Välj den tidszon som du vill använda. |
   | **Starttid** | Nej | startTime | String | Ange ett startdatum och starttid i följande format: <p>ÅÅÅÅ-MM-ddTHH om du väljer en tidszon <p>ELLER <p>ÅÅÅÅ-MM-: ssZ om du inte väljer en tidszon <p>Till exempel om du vill 18 September 2017 kl 2:00, sedan ange ”2017-09-18T14:00:00” och välj en tidszon som Pacific Standard Time. Alternativt kan du ange ”2017-09-18T14:00:00Z” utan en tidszon. <p>**Obs:** Starttiden måste följa den [ISO 8601 datum tidsangivelse](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [tidsformat för UTC-datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon, måste du lägga till Bokstaven ”Z” i slutet utan blanksteg. Den här ”Z” avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>Starttiden är den första förekomsten för enkla scheman och för avancerade upprepningar inte utlösaren utlöses alla snabbare än starttiden. [*Vad är hur kan jag använda startdatum och tidpunkt?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Skapa nu ditt återstående arbetsflöde med andra åtgärder. Fler åtgärder som du kan lägga till, se [Anslutningsappar för Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Arbetsflödesdefinitionen - glidande fönster

Du kan visa utlösardefinition glidande fönster med de alternativ som du har valt i din logikapp underliggande arbetsflödesdefinitionen, som använder JSON. Välj för att visa den här definitionen designerverktygsfältet **Kodvyer**. Om du vill återgå till designern väljer på verktygsfältet för appdesignern **Designer**.

Det här exemplet visar hur en glidande fönstret utlösardefinition kan se ut i en underliggande arbetsflödesdefinitionen där fördröjningen för varje upprepning är fem sekunder för en timvis upprepning:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Fördröjning nästa åtgärd i arbetsflöden](../connectors/connectors-native-delay.md)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)