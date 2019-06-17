---
title: Schemalägg återkommande uppgifter med upprepningsutlösare – Azure Logic Apps
description: Schemalägga och köra återkommande automatiserade uppgifter och arbetsflöden med upprepningsutlösare i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297549"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Skapa, schemalägga och kör återkommande uppgifter och arbetsflöden med upprepningsutlösare i Azure Logic Apps

För att regelbundet köra aktiviteter, processer eller jobb på specifika schema, du kan starta logikappens arbetsflöde med inbyggt **upprepning - schema** utlösaren. Du kan ange ett datum och tid samt en tidszon för att starta arbetsflödet och en upprepning för Upprepa det här arbetsflödet. Om upprepningar missade av någon anledning, fortsätter den här utlösaren återkommande vid nästa schemalagda intervall. Mer information om inbyggda schema-utlösare och åtgärder som finns i [schema och kör återkommande automatiserade uppgifter och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Här följer några mönster som den här utlösaren stödjer tillsammans med mer avancerade upprepningar och komplexa scheman:

* Kör omedelbart och upprepa varje *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.

* Starta vid ett specifikt datum och tid, och sedan köra och upprepa varje *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.

* Kör och upprepa på en eller flera gånger varje dag, till exempel kl 8:00 och 17:00:00.

* Kör och upprepa varje vecka, men endast för särskilda dagar, till exempel lördag och söndag.

* Kör och upprepa varje vecka, men endast för särskilda dagar och tidpunkter, till exempel måndag till fredag kl 8:00 och 17:00:00.

Skillnader mellan den här utlösaren och utlösare för glidande fönster för mer information om att schemalägga återkommande arbetsflöden finns [schema och kör återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Om du vill utlösa logikappen och kör bara en gång i framtiden, se [kör jobb bara en gång](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [logikappar](../logic-apps/logic-apps-overview.md). Om du är nybörjare till logic apps lär du dig [hur du skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Lägg till utlösare för upprepning

1. Logga in på [Azure Portal](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic App Designer visas i sökrutan anger du ”återkommande” som filter. Välj den här utlösaren som det första steget i ditt logikapparbetsflöde från listan över utlösare: **Frekvens**

   ![Välj ”återkommande” utlösare](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Ange intervall och frekvens för upprepningen. I det här exemplet anger du egenskaperna för att köra arbetsflödet varje vecka.

   ![Ange intervall och frekvens](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Egenskap | Obligatoriskt | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **Intervall** | Ja | interval | Integer | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på åtkomstfrekvensen. Här är de minsta och största intervall: <p>-Månad: 1 – 16 månader </br>-Dag: 1 – 500 dagar </br>-Timme: 1 – 12 000 timmar </br>-Minut: 1-72,000 minuter </br>-Sekund: 1-9,999,999 sekunder<p>Om intervallet är 6 och frekvensen är ”Month”, är upprepningen var sjätte månad. |
   | **Frekvens** | Ja | frequency | String | Tidsenhet för upprepningen: **Andra**, **minut**, **timme**, **dag**, **vecka**, eller **månad** |
   ||||||

   Fler alternativ för schemaläggning, öppna den **Lägg till ny parameter** lista. 
   Alla alternativ som du väljer visas på utlösaren efter markering.

   ![Avancerade alternativ för schemaläggning](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Egenskap | Obligatoriskt | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **Tidszon** | Nej | Tidszon | String | Gäller endast när du anger en starttid eftersom den här utlösaren inte acceptera [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Välj den tidszon som du vill använda. |
   | **Starttid** | Nej | startTime | String | Ange ett startdatum och starttid i följande format: <p>ÅÅÅÅ-MM-ddTHH om du väljer en tidszon <p>ELLER <p>ÅÅÅÅ-MM-: ssZ om du inte väljer en tidszon <p>Till exempel om du vill 18 September 2017 kl 2:00, sedan ange ”2017-09-18T14:00:00” och välj en tidszon som Pacific Standard Time. Alternativt kan du ange ”2017-09-18T14:00:00Z” utan en tidszon. <p>**Obs:** Starttiden måste följa den [ISO 8601 datum tidsangivelse](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [tidsformat för UTC-datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon, måste du lägga till Bokstaven ”Z” i slutet utan blanksteg. Den här ”Z” avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>Starttiden är den första förekomsten för enkla scheman och för komplexa scheman inte utlösaren utlöses alla snabbare än starttiden. [*Vad är hur kan jag använda startdatum och tidpunkt?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Dessa dagar** | Nej | weekDays | Sträng eller strängmatris | Om du väljer ”Week” kan välja du en eller flera dagar när du vill köra arbetsflödet: **Måndag**, **tisdag**, **onsdag**, **torsdag**, **fredag**, **lördag**, och **Söndag** |
   | **Vid dessa timmar** | Nej | hours | Heltal eller heltalsmatris | Om du väljer ”Day” eller ”Week” kan du välja en eller flera heltal mellan 0 och 23 som timmar på dagen när du vill köra arbetsflödet. <p><p>Exempel: Om du anger ”10”, ”12” och ”14”, får du 10 AM, 12 PM och 14: 00 för timmar på dagen, men minuter på dagen beräknas baserat på när återkomst startar. Om du vill ange minuter på dagen, anger du värdet för den **vid dessa minuter** egenskapen. |
   | **Vid dessa minuter** | Nej | minutes | Heltal eller heltalsmatris | Om du väljer ”Day” eller ”Week” kan du välja en eller flera heltal mellan 0 och 59 minuter på den timma som när du vill köra arbetsflödet. <p>Exempelvis kan du ange ”30” som minut mark och använder exemplet ovan för timmar på dagen, får du 10:30 AM, 12:30:00 och 14:30:00. |
   |||||

   Anta exempelvis att dagens datum är måndag 4 September 2017. Följande upprepningsutlösaren inte utlöses *alla körningstillfället* än startdatum och starttid, vilket är måndag 18 September 2017 vid 8:00 AM PST. Dock upprepningsschemat har angetts för 10:30 AM, 12:30:00 och 14:30:00 på måndagar endast. Det första gången du att utlösaren utlöses och skapar en logikappinstans för arbetsflödet är kl. 10:30. Om du vill veta mer om hur start tid arbetet kan se dessa [starta tid exempel](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Framtida körningar inträffa vid 12:30:00 och 14:30:00 samma dag. Varje upprepning skapar sina egna arbetsflödesinstansen. Efter det upprepas hela schemat över igen nästa måndag. [*Vad är en del andra exempel förekomster?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Avancerade schemaläggning exempel](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Utlösaren visas en förhandsgranskning för den angivna upprepningen bara när du väljer ”Day” eller ”Week” som frekvensen.

1. Skapa nu ditt återstående arbetsflöde med andra åtgärder. Fler åtgärder som du kan lägga till, se [Anslutningsappar för Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Arbetsflödesdefinitionen - upprepning

Du kan visa i din logikapp underliggande arbetsflödesdefinitionen, som använder JSON, den [upprepning utlösardefinition](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) med de alternativ som du har valt. Välj för att visa den här definitionen designerverktygsfältet **Kodvyer**. Om du vill återgå till designern väljer på verktygsfältet för appdesignern **Designer**.

Det här exemplet visar hur en återkommande utlösardefinition kan se ut i en underliggande arbetsflödesdefinitionen:

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

* [Pausa arbetsflöden med fördröjning åtgärder](../connectors/connectors-native-delay.md)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)
