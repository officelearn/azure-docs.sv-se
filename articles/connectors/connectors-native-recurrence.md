---
title: Schemalägga återkommande uppgifter och arbetsflöden
description: Schemalägga och kör återkommande automatiserade uppgifter och arbetsflöden med utlösaren Återkommande i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445861"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Skapa, schemalägga och köra återkommande uppgifter och arbetsflöden med utlösaren Återkommande i Azure Logic Apps

Om du regelbundet vill köra uppgifter, processer eller jobb enligt ett visst schema kan du starta logikapparbetsflödet med den inbyggda **återkommande - schemautlösaren.** Du kan ange ett datum och en tid samt en tidszon för att starta arbetsflödet och en upprepning för att upprepa arbetsflödet. Om upprepningar av någon anledning missas fortsätter den här utlösaren att upprepas vid nästa schemalagda intervall. Mer information om de inbyggda schedule-utlösarena och åtgärderna finns i [Schemalägga och köra återkommande automatiserade, uppgifter och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Här är några mönster som den här utlösaren stöder tillsammans med mer avancerade upprepningar och komplexa scheman:

* Kör omedelbart och upprepa varje *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.

* Börja vid ett visst datum och en viss tid och kör och upprepa sedan varje *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.

* Kör och upprepa vid en eller flera gånger varje dag, till exempel klockan 8:00 och 17:00.

* Kör och upprepa varje vecka, men bara för specifika dagar, till exempel lördag och söndag.

* Kör och upprepa varje vecka, men bara för specifika dagar och tider, till exempel måndag till fredag klockan 8:00 och 17:00.

Skillnader mellan den här utlösaren och utlösaren sliding window eller mer information om schemaläggning av återkommande arbetsflöden finns i [Schemalägga och köra återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Om du bara vill utlösa logikappen och köra en gång i framtiden läser du [Kör jobb en gång](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [logikappar](../logic-apps/logic-apps-overview.md). Om du inte har tidigare i logikappar kan du läsa om hur du [skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Lägga till utlösare för återkommande

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic App Designer visas anger `recurrence` du som filter i sökrutan. Välj den här utlösaren som det första steget i logikapparbetsflödet i listan **utlösare: Upprepning**

   ![Välj utlösare för återkommande](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Ange intervall och frekvens för upprepningen. I det här exemplet anger du att dessa egenskaper ska köra arbetsflödet varje vecka.

   ![Ange intervall och frekvens](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Egenskap | JSON-namn | Krävs | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | **Intervall** | `interval` | Ja | Integer | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på frekvensen. Här är de lägsta och högsta intervallen: <p>- Månad: 1-16 månader </br>- Dag: 1-500 dagar </br>- Timme: 1-12 000 timmar </br>- Minut: 1-72 000 minuter </br>- Andra: 1-9,999,999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "Månad", är upprepningen var sjätte månad. |
   | **Frekvens** | `frequency` | Ja | String | Tidsenheten för upprepningen: **Sekund**, **Minut,** **Timme,** **Dag,** **Vecka**eller **Månad** |
   ||||||

   > [!IMPORTANT]
   > När upprepningar inte anger avancerade schemaläggningsalternativ baseras framtida upprepningar på den senaste körningstiden.
   > Starttiderna för dessa upprepningar kan drivas på grund av faktorer som svarstid under lagringsanrop. Om du vill vara säker på att logikappen inte missar en upprepning, särskilt när frekvensen är i dagar eller längre, använder du något av följande alternativ:
   > 
   > * Ange en starttid för upprepningen.
   > 
   > * Ange timmar och minuter för när upprepningen ska köras med hjälp av egenskaperna **Vid dessa timmar** och vid dessa **minuter.**
   > 
   > * Använd [utlösaren skjutfönster](../connectors/connectors-native-sliding-window.md)i stället för utlösaren För återkommande.

1. Om du vill ange avancerade schemaläggningsalternativ öppnar du listan **Lägg till ny parameter.** Alla alternativ som du väljer visas på utlösaren efter markeringen.

   ![Avancerade schemaläggningsalternativ](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Egenskap | JSON-namn | Krävs | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | **Tidszon** | `timeZone` | Inga | String | Gäller endast när du anger en starttid eftersom den här utlösaren inte accepterar [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Markera den tidszon som du vill använda. |
   | **Starttid** | `startTime` | Inga | String | Ange ett startdatum och en starttid i det här formatet: <p>YYYY-MM-DDThh:mm:ss om du väljer en tidszon <p>ELLER <p>Å ÅYY-MM-DDThh:mm:ssZ om du inte väljer en tidszon <p>Så om du till exempel vill ha 18 september 2017 klockan 14:00 anger du "2017-09-18T14:00:00" och väljer en tidszon som Stillahavsstandardtid. Du kan också ange "2017-09-18T14:00:00Z" utan tidszon. <p>**Anm.:** Denna starttid har högst 49 år i framtiden och måste följa [ISO 8601 datum tidsspecifikationen](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC datumtid format](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC offset](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon måste du lägga till bokstaven "Z" i slutet utan blanksteg. Detta "Z" avser motsvarande [nautisk tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkla scheman är starttiden den första förekomsten, medan utlösaren inte utlöses tidigare än starttiden för komplexa scheman. [*Vilka sätt kan jag använda startdatum och starttid?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Dessa dagar** | `weekDays` | Inga | Sträng- eller strängmatris | Om du väljer "Vecka" kan du välja en eller flera dagar när du vill köra arbetsflödet: **måndag,** **tisdag,** **onsdag,** **torsdag,** **fredag,** **lördag**och **söndag** |
   | **Vid dessa timmar** | `hours` | Inga | Heltals- eller heltalsmatris | Om du väljer "Dag" eller "Vecka" kan du välja ett eller flera heltal från 0 till 23 som timmar på dagen för när du vill köra arbetsflödet. <p><p>Om du till exempel anger "10", "12" och "14" får du 10,00, 12.00 och 14.00 för dygnets timmar, men dagens minuter beräknas baserat på när upprepningen startar. Om du vill ställa in dagens minuter anger du värdet för egenskapen **At these minutes.** |
   | **Vid dessa minuter** | `minutes` | Inga | Heltals- eller heltalsmatris | Om du väljer "Dag" eller "Vecka" kan du välja ett eller flera heltal från 0 till 59 som minuter i timmen när du vill köra arbetsflödet. <p>Du kan till exempel ange "30" som minutmarkering och använda föregående exempel under timmar på dagen får du 10:30, 12:30 och 14:30. |
   |||||

   Anta till exempel att det i dag är måndagen den 4 september 2017. Följande återkommande utlösare utlöses inte *tidigare* än startdatum och starttid, vilket är måndag, september 18, 2017 kl 8:00 PST. Upprepningsschemat är dock inställt för 10:30, 12:30 och 14:30 endast på måndagar. Så första gången som utlösaren utlöses och skapar en logikapp arbetsflödesinstans är klockan 10:30. Mer information om hur starttider fungerar finns i de här [exempel på starttid](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Framtida körningar sker klockan 12:30 och 14:30 samma dag. Varje upprepning skapar sin egen arbetsflödesinstans. Därefter upprepas hela schemat igen nästa måndag. [*Vad är några andra exempel förekomster?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Exempel på avancerad schemaläggning](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Utlösaren visar en förhandsgranskning för din angivna upprepning endast när du väljer "Dag" eller "Vecka" som frekvens.

1. Bygg nu ditt återstående arbetsflöde med andra åtgärder. Fler åtgärder som du kan lägga till finns i [Kopplingar för Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Arbetsflödesdefinition - Upprepning

I logikappens underliggande arbetsflödesdefinition, som använder JSON, kan du visa [definitionen för återkommande utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) med de alternativ som du har valt. Om du vill visa den här definitionen väljer du **Kodvy**i verktygsfältet Designer . Om du vill återgå till designern väljer du i designerverktygsfältet **Designer**.

I det här exemplet visas hur en definition av återkommande utlösare kan se ut i en underliggande arbetsflödesdefinition:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Pausa arbetsflöden med fördröjningsåtgärder](../connectors/connectors-native-delay.md)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)
