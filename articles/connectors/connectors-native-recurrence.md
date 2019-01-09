---
title: Schemalägga och köra automatiserade uppgifter och arbetsflöden med Azure Logic Apps | Microsoft Docs
description: Automatisera schemalagda och återkommande uppgifter med återkommande-anslutningsapp i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
tags: connectors
ms.topic: article
ms.date: 01/08/2019
ms.openlocfilehash: a1f89ca6e9dc2d05180df14ff0f4dc52729a7e03
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107845"
---
# <a name="create-and-run-recurring-tasks-and-workflows-with-azure-logic-apps"></a>Skapa och kör återkommande uppgifter och arbetsflöden med Azure Logic Apps

Om du vill schemalägga åtgärder, arbetsbelastningar och processer som körs regelbundet, skapa en logikapparbetsflöde som börjar med den **schema – återkommande** [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts). Du kan ange ett datum och tid för att starta arbetsflödet och ett upprepningsschema för att utföra uppgifter som de här exemplen och mer:

* Hämta interna data: [Kör en SQL-lagrade proceduren](../connectors/connectors-create-api-sqlazure.md) varje dag.
* Hämta externa data: Hämta väderprognoser från amerikanska NOAA var 15: e minut.
* Rapportdata: E-post för en samling med alla order som är större än ett specifikt belopp under den senaste veckan.
* Bearbeta data: Komprimera dagens överförda bilder varje vardag vid låg belastning.
* Rensa data: Ta bort alla tweets som är äldre än tre månader.
* Arkivera data: Skicka fakturor till en tjänst för säkerhetskopiering varje månad.

Den här utlösaren stödjer många mönster, till exempel:

* Kör omedelbart och upprepa varje *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.
* Starta vid en viss tidpunkt och sedan köra och upprepa varje *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.
* Kör och upprepa på en eller flera gånger varje dag, till exempel kl 8:00 och 17:00:00.
* Kör och upprepa varje vecka, men endast för särskilda dagar, till exempel lördag och söndag.
* Kör och upprepa varje vecka, men endast för särskilda dagar och tidpunkter, till exempel måndag till fredag kl 8:00 och 17:00:00.

När den återkommande utlösaren utlöses varje gång Logic Apps skapar och kör en ny instans av ditt logikapparbetsflöde. 

Om du vill utlösa logikappen och kör bara en gång i framtiden, se [kör jobb bara en gång](#run-once) senare i det här avsnittet.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du [registrera dig för en prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Lägg till en återkommande utlösare i logikappen

1. Logga in på [Azure Portal](https://portal.azure.com). Skapa en tom logikapp eller Läs [så här skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. När Logic Apps Designer visas under sökrutan väljer **alla**. I sökrutan anger du ”återkommande” som filter. Välj den här utlösaren från listan över utlösare: **Upprepning - schema** 

   ![Välj ”--upprepningsschemat” utlösare](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Den här utlösaren är nu det första steget i din logikapp.

3. Ange intervall och frekvens för upprepningen. I det här exemplet anger du egenskaperna för att köra arbetsflödet varje vecka. 

   ![Ange intervall och frekvens](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Flera schemaläggningsalternativ väljer **visa avancerade alternativ**. 

   ![Fler alternativ](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Du kan nu ange dessa alternativ: 

   * Ange ett startdatum och starttid för aktiveringen av utlösaren. 
   Om du anger ett startdatum och starttid, kan du också använda en tidszon. 

   * Om du väljer ”Day” eller ”Week” för hur ofta kan du välja specifika tider för upprepningen. 

   * Om du väljer ”Week” kan du välja särskilda dagar i veckan för.
   
   ![Avancerade alternativ för schemaläggning](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Anta exempelvis att dagens datum är måndag 4 September 2017. 
   Följande upprepningsutlösaren inte utlöses *alla körningstillfället* än startdatum och starttid, vilket är måndag 18 September 2017 vid 8:00 AM PST. 
   Dock upprepningsschemat har angetts för 10:30 AM, 12:30:00 och 14:30:00 på måndagar endast. Det första gången du att utlösaren utlöses och skapar en logikappinstans för arbetsflödet är kl. 10:30. 
   Om du vill veta mer om hur start tid arbetet kan se dessa [starta tid exempel](#start-time).
   Framtida körningar inträffa vid 12:30:00 och 14:30:00 samma dag. 
   Varje upprepning skapar sina egna arbetsflödesinstansen. Efter det upprepas hela schemat över igen nästa måndag. 
   [*Vad är en del andra exempel förekomster?*](#example-recurrences)

   ![Avancerade schemaläggning exempel](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Utlösaren visas en förhandsgranskning för den angivna upprepningen bara när du väljer ”Day” eller ”Week” som frekvensen.
   
6. Nu skapar ditt återstående arbetsflöde med åtgärder eller flow kontrolluttryck. Fler åtgärder som du kan lägga till, se [Anslutningsappar](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Utlösarinformation

Du kan konfigurera dessa egenskaper för utlösare för upprepning.

| Namn | Krävs | Egenskapsnamn | Typ | Beskrivning | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Frekvens** | Ja | frequency | Sträng | Tidsenhet för upprepningen: **Andra**, **minut**, **timme**, **dag**, **vecka**, eller **månad** | 
| **Intervall** | Ja | interval | Integer | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på åtkomstfrekvensen. <p>Standardintervallet är 1. Här är de minsta och största intervall: <p>-Månad: 1 – 16 månader </br>-Dag: 1 – 500 dagar </br>-Timme: 1 – 12 000 timmar </br>-Minut: 1-72,000 minuter </br>-Sekund: 1-9,999,999 sekunder<p>Om intervallet är 6 och frekvensen är ”Month”, är upprepningen var sjätte månad. | 
| **Tidszon** | Nej | Tidszon | Sträng | Gäller endast när du anger en starttid eftersom den här utlösaren inte acceptera [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Välj den tidszon som du vill använda. | 
| **Starttid** | Nej | startTime | Sträng | Ange en starttid i följande format: <p>ÅÅÅÅ-MM-ddTHH om du väljer en tidszon <p>ELLER <p>ÅÅÅÅ-MM-: ssZ om du inte väljer en tidszon <p>Till exempel om du vill 18 September 2017 kl 2:00, sedan ange ”2017-09-18T14:00:00” och välj en tidszon som Pacific Time. Alternativt kan du ange ”2017-09-18T14:00:00Z” utan en tidszon. <p>**Obs!** Starttiden måste följa den [ISO 8601 datum tidsangivelse](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [tidsformat för UTC-datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon, måste du lägga till Bokstaven ”Z” i slutet utan blanksteg. Den här ”Z” avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>Starttiden är den första förekomsten för enkla scheman och för komplexa scheman inte utlösaren utlöses alla snabbare än starttiden. [*Vad är hur kan jag använda startdatum och tidpunkt?*](#start-time) | 
| **Dessa dagar** | Nej | weekDays | Sträng eller strängmatris | Om du väljer ”Week” kan välja du en eller flera dagar när du vill köra arbetsflödet: **Måndag**, **tisdag**, **onsdag**, **torsdag**, **fredag**, **lördag**, och **Söndag** | 
| **Vid dessa timmar** | Nej | hours | Heltal eller heltalsmatris | Om du väljer ”Day” eller ”Week” kan du välja en eller flera heltal mellan 0 och 23 som timmar på dagen när du vill köra arbetsflödet. <p>Exempel: Om du anger ”10”, ”12” och ”14”, får du 10 AM, 12 PM och 14: 00 som timme markerar. | 
| **Vid dessa minuter** | Nej | minutes | Heltal eller heltalsmatris | Om du väljer ”Day” eller ”Week” kan du välja en eller flera heltal mellan 0 och 59 minuter på den timma som när du vill köra arbetsflödet. <p>Exempelvis kan du ange ”30” som minut mark och använder exemplet ovan för timmar på dagen, får du 10:30 AM, 12:30:00 och 14:30:00. | 
||||| 

## <a name="json-example"></a>JSON-exempel

Här är ett exempel [upprepning utlösardefinition](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

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
         "startTime": "2017-09-07T14:00:00",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

<a name="run-once"></a>

**F:** Vad händer om jag vill köra en logikapp en gång endast i framtiden? </br>
**S:** Du kan använda för att utlösa logikappen och kör en gång utan återkommande, den **Scheduler: Kör en gång jobb** mall. När du har skapat en ny logikapp, men innan du öppnar Logic Apps-designern under den **mallar** avsnittet från den **kategori** väljer **schema**, och välj sedan mallen:

![Välj ”Scheduler: Kör en gång jobb ”mall](./media/connectors-native-recurrence/choose-run-once-template.png)

Eller, om du använder en tom mall för logikapp börjar din logikapp med den **när en HTTP-begäran tas emot - begäran** utlösaren. Skicka utlösarens starttid som en parameter. Nästa steg, lägger du till den **Fördröj tills – schemalägga** åtgärd, och ange tiden för när nästa åtgärd börjar köras.

<a name="example-recurrences"></a>

**F:** Vad är andra exempel återkommande scheman? </br>
**S:** Här följer mer exempel:

| Upprepning | Intervall | Frekvens | Starttid | Dessa dagar | Vid dessa timmar | Vid dessa minuter | Obs! |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Körs varje kvart (inga startdatum och tidpunkt) | 15 | Minut | {Ingen} | {otillgänglig} | {Ingen} | {Ingen} | Det här schemat startar omedelbart och beräknar framtida upprepningar baserat på senaste körningstid. | 
| Kör var 15: e minut (med startdatum och tidpunkt) | 15 | Minut | *startDate*T*startTime*Z | {otillgänglig} | {Ingen} | {Ingen} | Det här schemat inte startar *alla körningstillfället* än den angivna startdatum och starttid, beräknar sedan framtida upprepningar baserat på senaste körningstid. | 
| Körs varje hel timme (med startdatum och tidpunkt) | 1 | Timme | *startDate*Thh:00:00Z | {otillgänglig} | {Ingen} | {Ingen} | Det här schemat inte startar *alla körningstillfället* än den angivna starta datum och tid. Framtida upprepningar körs varje timme ”00” minut märket. <p>Om frekvensen är ”Week” eller ”Month”, körs schemat respektive bara en gång i veckan eller en dag i månaden. | 
| Kör varje timme, varje dag (inga startdatum och tidpunkt) | 1 | Timme | {Ingen} | {otillgänglig} | {Ingen} | {Ingen} | Det här schemat startar omedelbart och beräknar framtida upprepningar baserat på senaste körningstid. <p>Om frekvensen är ”Week” eller ”Month”, körs schemat respektive bara en gång i veckan eller en dag i månaden. | 
| Kör varje timme, varje dag (med startdatum och tidpunkt) | 1 | Timme | *startDate*T*startTime*Z | {otillgänglig} | {Ingen} | {Ingen} | Det här schemat inte startar *alla körningstillfället* än den angivna startdatum och starttid, beräknar sedan framtida upprepningar baserat på senaste körningstid. <p>Om frekvensen är ”Week” eller ”Month”, körs schemat respektive bara en gång i veckan eller en dag i månaden. | 
| Kör var 15 minuter efter timmen, varje timme (med startdatum och tidpunkt) | 1 | Timme | *startDate*T00:15:00Z | {otillgänglig} | {Ingen} | {Ingen} | Det här schemat inte startar *alla körningstillfället* än den angivna startdatum och tidpunkt som körs kl. 00:15, 01:15:00, 14:15:00 och så vidare. | 
| Körs varje kvart efter timmen, varje timme (inga startdatum och tidpunkt) | 1 | Dag | {Ingen} | {otillgänglig} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Schemat som körs kl. 00:15, 01:15:00, 14:15:00 och så vidare. Dessutom är det här schemat motsvarar en frekvens på ”Hour” och en starttid med ”15” minuter. | 
| Kör var 15: e minut i 15 minuter mark (inga startdatum och tidpunkt) | 1 | Dag | {Ingen} | {otillgänglig} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Det här schemat startar inte förrän nästa angetts 15 minuters mark. | 
| Kör kl 8:00 varje dag (inga startdatum och tidpunkt) | 1 | Dag | {Ingen} | {otillgänglig} | 8 | {Ingen} | Schemat som körs kl 8:00 varje dag, baserat på det angivna schemat. | 
| Kör kl 8:00 varje dag (med startdatum och tidpunkt) | 1 | Dag | *startDate*T08:00:00Z | {otillgänglig} | {Ingen} | {Ingen} | Schemat körs 8:00:00 varje dag, baserat på den angivna starttiden. | 
| Kör kl. 8:30 varje dag (inga startdatum och tidpunkt) | 1 | Dag | {Ingen} | {otillgänglig} | 8 | 30 | Schemat som körs kl. 8:30 varje dag, baserat på det angivna schemat. | 
| Kör kl. 8:30 varje dag (med startdatum och tidpunkt) | 1 | Dag | *startDate*T08:30:00Z | {otillgänglig} | {Ingen} | {Ingen} | Det här schemat startar på det angivna startdatumet kl 8:30. | 
| Kör kl. 8:30 och 4:30:00 varje dag | 1 | Dag | {Ingen} | {otillgänglig} | 8, 16 | 30 | | 
| Kör kl. 8:30, 8:45 AM, 4:30 PM och 4:45 varje dag | 1 | Dag | {Ingen} | {otillgänglig} | 8, 16 | 30, 45 | | 
| Kör varje lördag 17: 00 (inga startdatum och tidpunkt) | 1 | Vecka | {Ingen} | ”Lördag” | 17 | 00 | Schemat körs varje lördag kl 5:00. | 
| Kör varje lördag kl (med startdatum och tidpunkt) | 1 | Vecka | *startDate*T17:00:00Z | ”Lördag” | {Ingen} | {Ingen} | Det här schemat inte startar *alla körningstillfället* än den angivna starta datum och tid, i det här fallet den 9 September 2017 17:00:00. Framtida upprepningar kör varje lördag kl 5:00. | 
| Körs varje tisdag, torsdag 17: 00 | 1 | Vecka | {Ingen} | ”Tisdag”, ”torsdag” | 17 | {Ingen} | Schemat körs varje tisdag och torsdag 17:00:00. | 
| Kör varje timme under arbetstid | 1 | Vecka | {Ingen} | Välj alla dagar utom lördag och söndag. | Välj tidpunkter på dagen som du vill. | Välj några minuter på den timma som önskas. | Exempel: om din arbetstid är 8:00:00 till 5:00, välj sedan ”8, 9, 10, 11, 12, 13, 14, 15, 16, 17” som timmar på dagen. <p>Om din arbetstid kl. 8:30 till 5:30 PM, väljer du de senaste timmarna dagen plus ”30” som minuter efter timmen. | 
| Kör en gång varje dag på helger | 1 | Vecka | {Ingen} | ”Lördag”, ”söndag” | Välj tidpunkter på dagen som du vill. | Välj några minuter för timmen efter behov. | Det här schemat körs varje lördag och söndag vid det angivna schemat. | 
| Körs varje kvart varannan vecka på måndagar endast | 2 | Vecka | {Ingen} | ”Måndag” | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Det här schemat körs varannan måndag vid varje 15 minuters mark. | 
| Kör varje timme under en dag i månaden | 1 | Månad | {se anmärkning} | {otillgänglig} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {se anmärkning} | Om du inte anger ett startdatum och starttid, använder det här schemat datum och tid. Ange minuters timme, en starttid för att styra minuter för upprepningsschemat, eller Använd tiden för skapandet. Till exempel om starttiden eller Skapandetid är kl. 8:25, det här schemat körs 8:25, 9:25 AM, 10:25 AM, och så vidare. | 
||||||||| 

<a name="start-time"></a>

**F:** Vad är hur kan jag använda startdatum och tidpunkt? </br>
**S:** Här följer några mönster som visar hur du kan styra upprepning med startdatum och starttid och hur Logic Apps-motorn utför dessa upprepningar:

| Starttid | Upprepning utan schema | Upprepning med schema | 
| ---------- | --------------------------- | ------------------------ | 
| {Ingen} | Körs arbetsbelastningen första direkt. <p>Kör framtida arbetsbelastningar baserat på senaste körningstid. | Körs arbetsbelastningen första direkt. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. | 
| Starttid tidigare | Beräknar körtider baserat på den angivna starttiden och rensningar senaste kör gånger. Kör den första arbetsbelastningen på nästa framtiden körtid. <p>Kör framtida arbetsbelastningar baserat på beräkningar från senaste körningstid. <p>Fler förklaringar finns i exemplet som följer den här tabellen. | Kör den första arbetsbelastningen *tidigast* än starttiden, baserat på schemat som beräknas från starttiden. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. <p>**Obs!** Om du anger en upprepning med ett schema, inte men timmar eller minuter för schemat, beräknas framtida körtider med timmar eller minuter, från den första körning. | 
| Starttid för närvarande eller i framtiden | Körs den första arbetsbelastningen vid den angivna starttiden. <p>Kör framtida arbetsbelastningar baserat på beräkningar från senaste körningstid. | Kör den första arbetsbelastningen *tidigast* än starttiden, baserat på schemat som beräknas från starttiden. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. <p>**Obs!** Om du anger en upprepning med ett schema, inte men timmar eller minuter för schemat, beräknas framtida körtider med timmar eller minuter, från den första körning. | 
||||

**Exempel för en senaste starttid med upprepning men utan schema** 

| Starttid | Aktuell tid | Upprepning | Schema |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09 -**07**T14:00:00Z | 2017-09 -**08**T13:00:00Z | Varje 2 dagar | {Ingen} | 
||||| 

I det här scenariot, Logic Apps-motor beräknar körtider baserat på starttiden, tar du bort tidigare körtider och använder nästa framtiden starttid för den första körningen. Efter den första körningen baserat framtida körningar på schemat som beräknas från starttiden. Här är hur den här upprepningen ser ut:

| Starttid | Första körning | Framtiden körtider | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** på 2:00 PM | 2017-09 -**09** på 2:00 PM | 2017-09 -**11** på 2:00 PM </br>2017-09 -**13** på 2:00 PM </br>2017-09 -**15** på 2:00 PM </br>och så vidare...
||||| 

Så det här scenariot, oavsett hur långt i det förflutna som du anger du start tid, till exempel 2017-09 -**05** kl 2:00 eller 2017-09 -**01** på 2:00 PM, din första körningstiden är samma.

## <a name="next-steps"></a>Nästa steg

* [Åtgärder och utlösare för arbetsflöde](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Anslutningsappar](../connectors/apis-list.md)
