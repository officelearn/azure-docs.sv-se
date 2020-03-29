---
title: Schemalägga aktiviteter för att hantera sammanhängande data
description: Skapa och köra återkommande uppgifter som hanterar sammanhängande data med hjälp av skjutbara fönster i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786919"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Schemalägga och köra uppgifter för sammanhängande data med hjälp av utlösaren skjutfönster i Azure Logic Apps

Om du regelbundet vill köra uppgifter, processer eller jobb som måste hantera data i sammanhängande segment kan du starta logikapparbetsflödet med **utlösaren Skjutfönster.** Du kan ange ett datum och en tid samt en tidszon för att starta arbetsflödet och en upprepning för att upprepa arbetsflödet. Om upprepningar missas av någon anledning bearbetar den här utlösaren de missade upprepningarna. När du till exempel synkroniserar data mellan databasen och lagring för säkerhetskopiering använder du utlösaren skjutfönster så att data synkroniseras utan att det uppstår luckor. Mer information om de inbyggda schedule-utlösarena och åtgärderna finns i [Schemalägga och köra återkommande automatiserade, uppgifter och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Här är några mönster som den här utlösaren stöder:

* Kör omedelbart och upprepa varje *n* antal sekunder, minuter eller timmar.

* Börja vid ett visst datum och en viss tid och kör och upprepa varje *n antal* sekunder, minuter eller timmar. Med den här utlösaren kan du ange en starttid tidigare, som kör alla tidigare upprepningar.

* Fördröja varje upprepning under en viss tid innan du kör.

Skillnader mellan den här utlösaren och utlösaren För återkommande eller mer information om schemaläggning av återkommande arbetsflöden finns i [Schemalägga och köra återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Om du bara vill utlösa logikappen och köra en gång i framtiden läser du [Kör jobb en gång](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration kan du [registrera dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [logikappar](../logic-apps/logic-apps-overview.md). Om du inte har tidigare i logikappar kan du läsa om hur du [skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Lägga till utlösare av skjutfönster

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic App Designer visas anger du "skjutfönster" som filter i sökrutan. Välj den här utlösaren som det första steget i logikappens arbetsflöde i listan utlösare: **Skjutfönster**

   ![Välj utlösare för skjutfönster](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Ange intervall och frekvens för upprepningen. I det här exemplet anger du att dessa egenskaper ska köra arbetsflödet varje vecka.

   ![Ange intervall och frekvens](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Egenskap | Krävs | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **Intervall** | Ja | interval | Integer | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på frekvensen. Här är de lägsta och högsta intervallen: <p>- Timme: 1-12 000 timmar </br>- Minut: 1-72 000 minuter </br>- Andra: 1-9,999,999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "Timme", är upprepningen var sjätte timme. |
   | **Frekvens** | Ja | frequency | String | Tidsenheten för upprepningen: **Sekund,** **Minut**eller **Timme** |
   ||||||

   ![Avancerade upprepningsalternativ](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Om du vill ha fler upprepningsalternativ öppnar du listan **Lägg till ny parameter.** 
   Alla alternativ som du väljer visas på utlösaren efter markeringen.

   | Egenskap | Krävs | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **Fördröjning** | Inga | Försening | String | Varaktigheten för att fördröja varje upprepning med hjälp av [iso 8601 datum tidsspecifikation](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Tidszon** | Inga | Tidszon | String | Gäller endast när du anger en starttid eftersom den här utlösaren inte accepterar [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Markera den tidszon som du vill använda. |
   | **Starttid** | Inga | startTime | String | Ange ett startdatum och en starttid i det här formatet: <p>YYYY-MM-DDThh:mm:ss om du väljer en tidszon <p>ELLER <p>Å ÅYY-MM-DDThh:mm:ssZ om du inte väljer en tidszon <p>Så om du till exempel vill ha 18 september 2017 klockan 14:00 anger du "2017-09-18T14:00:00" och väljer en tidszon som Stillahavsstandardtid. Du kan också ange "2017-09-18T14:00:00Z" utan tidszon. <p>**Anm.:** Den här starttiden måste följa [datumtidsspecifikationen ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC-datumtidsformatet,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon måste du lägga till bokstaven "Z" i slutet utan blanksteg. Detta "Z" avser motsvarande [nautisk tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkla scheman är starttiden den första förekomsten, medan utlösaren inte utlöses tidigare än starttiden för avancerade upprepningar. [*Vilka sätt kan jag använda startdatum och starttid?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Bygg nu ditt återstående arbetsflöde med andra åtgärder. Fler åtgärder som du kan lägga till finns i [Kopplingar för Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Arbetsflödesdefinition - Skjutfönster

I logikappens underliggande arbetsflödesdefinition, som använder JSON, kan du visa utlösardefinitionen för skjutfönstret med de alternativ som du har valt. Om du vill visa den här definitionen väljer du **Kodvy**i verktygsfältet Designer . Om du vill återgå till designern väljer du i designerverktygsfältet **Designer**.

Det här exemplet visar hur en utlösardefinition för skjutfönster kan se ut i en underliggande arbetsflödesdefinition där fördröjningen för varje upprepning är fem sekunder för en timmes upprepning:

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

* [Fördröja nästa åtgärd i arbetsflöden](../connectors/connectors-native-delay.md)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)