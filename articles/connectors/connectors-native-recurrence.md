---
title: Schemalägga aktiviteter och regelbundet kör arbetsflöden - Azure Logic Apps | Microsoft Docs
description: Skapa och schemalägga regelbundet kör aktiviteter, åtgärder, arbetsflöden, processer och arbetsbelastningar med logic apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 3bd396355681cdde486cfbea7004c9c1aece09da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296795"
---
# <a name="create-and-schedule-regularly-running-tasks-with-azure-logic-apps"></a>Skapa och schemalägga aktiviteter som regelbundet körs med Azure Logikappar

Om du vill schemalägga aktiviteter, åtgärder, arbetsbelastningar eller processer som körs regelbundet, kan du skapa ett arbetsflöde för logik app som börjar med den **schema - upprepning** [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts). Du kan ange ett datum och tid för att starta återkommande och ett återkommande schema för att utföra uppgifter som att de här exemplen och mer med den här utlösaren:

* Hämta interna data: [köra en SQL-lagrade procedur](../connectors/connectors-create-api-sqlazure.md) varje dag.
* Hämta externa data: hämta väderrapporter från NOAA var 15: e minut.
* Rapportera data: e-en samling med alla order som är större än ett visst belopp under den senaste veckan.
* Bearbeta data: komprimera idag har överfört bilder varje veckodag vid låg belastning.
* Rensa data: ta bort alla tweets som är äldre än tre månader.
* Arkivering av data: Push fakturor till en tjänst som säkerhetskopiering varje månad.

Den här utlösaren stöder många mönster, till exempel:

* Kör omedelbart och Upprepa var *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.
* Starta vid en viss tid och sedan köra och Upprepa var *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.
* Kör och upprepa på en eller flera gånger varje dag, till exempel 8:00:00 och 17:00.
* Kör och upprepa varje vecka, men endast för särskilda dagar, till exempel lördag och söndag.
* Kör och upprepa varje vecka, men endast för särskilda dagar och tidpunkter, till exempel måndag-fredag på 8:00:00 och 17:00.

När upprepning utlösaren utlöses varje gång, Logic Apps skapar och kör en ny instans av arbetsflödet logik app.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du [registrera dig för en prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* Grundläggande kunskaper om [skapa logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Lägg till en upprepning utlösare i din logikapp

1. Logga in på [Azure Portal](https://portal.azure.com). Skapa en tom logikapp eller Läs [så här skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. När Logic Apps Designer visas i sökrutan anger du ”återkommande” som filter. Välj den **schema - upprepning** utlösare. 

   ![Schema - utlösare för upprepning](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Den här utlösaren är nu det första steget i din logikapp.

3. Ange intervall och frekvens för upprepningen. I det här exemplet anger du egenskaperna för att köra arbetsflödet varje vecka. 

   ![Angivet intervall och frekvens](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Flera schemaläggningsalternativ väljer **visa avancerade alternativ**. 

   ![Fler alternativ](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Nu kan du ange följande alternativ: 

   * Ange datum och tid för startar utlösaren. 
   Om du anger ett datum och tid, kan du också använda en tidszon. 

   * Om du väljer ”dag” eller ”vecka” för frekvensen måste välja du specifika tidpunkter för återkommande. 

   * Om du väljer ”vecka”, kan du välja särskilda dagar i veckan för.
   
   ![Avancerade alternativ för schemaläggning](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Anta exempelvis att dagens datum är måndag 4 September 2017. 
   Följande upprepning utlösaren inte utlösas *alla snabbare* än startdatum och starttid, vilket är måndag 18 September 2017 på 8:00 AM PST. 
   Dock upprepningsschemat har angetts för 10:30 AM, 12:30:00 och 14:30:00 på måndagen endast. Därför första gången som utlösaren utlöses och skapar en logik app arbetsflödesinstans är kl 10:30. 
   Mer information om hur start tid arbete finns dessa [starta tid exempel](#start-time).
   Framtida körs sker på 12:30:00 och 14:30:00 samma dag. 
   Varje upprepning skapar sina egna arbetsflödesinstans. Efter att upprepas hela schemat över igen nästa måndag. 
   [*Vad är en del andra exempel förekomster?*](#example-recurrences)

   ![Avancerade schemaläggning exempel](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Utlösaren visas en förhandsgranskning för den angivna återkommande bara när du väljer ”dag” eller ”vecka” som frekvensen.
   
6. Nu skapa återstående arbetsflödet med åtgärder eller flöda kontrolluttryck. Fler åtgärder som du kan lägga till finns [kopplingar](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Information om utlösaren

Du kan konfigurera dessa egenskaper för upprepning utlösaren.

| Namn | Krävs | Egenskapsnamn | Typ | Beskrivning | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Frekvens** | Ja | frequency | Sträng | Tidsenhet för återkommande: **andra**, **minut**, **timme**, **dag**, **vecka**, eller  **Månad** | 
| **Intervall** | Ja | interval | Integer | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på hur ofta. <p>Standardintervallet är 1. Här är de minsta och största intervall: <p>-Månad: 1-16 månader </br>-Dagars: 1-500 dagar </br>-Timmars: 1-12 000 timmar </br>-Minuters: 1-72,000 minuter </br>-Andra: 1-9,999,999 sekunder<p>Om intervallet är 6, och hur ofta är ”månad”, är upprepningen var sjätte månad. | 
| **Tidszon** | Nej | Tidszon | Sträng | Gäller endast när du anger en starttid eftersom utlösaren inte acceptera [UTC-förskjutningen](https://en.wikipedia.org/wiki/UTC_offset). Välj den tidszon som du vill använda. | 
| **Starttid** | Nej | startTime | Sträng | Ange en starttid i det här formatet: <p>ÅÅÅÅ-MM-ddTHH om du väljer en tidszon <p>ELLER <p>ÅÅÅÅ-MM-ddTHH om du inte väljer en tidszon <p>Exempelvis om du vill 18 September 2017 2:00 PM, anger du ”2017-09-18T14:00:00” och välj en tidszon som Pacific Time. Alternativt kan du ange ”2017-09-18T14:00:00Z” utan en tidszon. <p>**Obs:** starttiden måste följa den [ISO 8601 dag tidsangivelse](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC-datum tidsformat](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutningen](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon, måste du lägga till Bokstaven ”Z” i slutet utan blanksteg. ”Z” refererar till motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkel scheman starttiden är den första förekomsten medan för komplexa scheman utlösaren inte utlösa en snabbare än starttiden. [*Vad är hur kan jag använda startdatum och starttid?*](#start-time) | 
| **Dessa dagar** | Nej | weekDays | Sträng eller strängmatris | Om du väljer ”vecka”, kan du välja en eller flera dagar när du vill köra arbetsflödet: **måndag**, **tisdag**, **onsdag**, **torsdag** , **Fredag**, **lördag**, och **söndag** | 
| **Vid dessa timmar** | Nej | hours | Heltal eller heltalsmatris | Om du väljer ”dag” eller ”vecka” kan du välja ett eller flera heltal mellan 0 och 23 som tidpunkter på dagen när du vill köra arbetsflödet. <p>Till exempel, om du anger ”10”, ”12” och ”14”, får du 10 AM och 12 PM 14: 00 som timme markerar. | 
| **Vid dessa minuter** | Nej | minutes | Heltal eller heltalsmatris | Om du väljer ”dag” eller ”vecka” kan du välja ett eller flera heltal mellan 0 och 59 minuter för den när du vill köra arbetsflödet. <p>Exempelvis kan du ange ”30” som minut märket och använder det föregående exemplet för tidpunkter på dagen, du får 10:30 AM, 12:30 PM och 2:30 PM. | 
||||| 

## <a name="json-example"></a>JSON-exempel

Här är ett exempel [upprepning utlösardefinition](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

``` json
{
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
}
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

<a name="example-recurrences"></a>

**F:** vad är andra exempel återkommande scheman? </br>
**S:** följer fler exempel:

| Upprepning | Intervall | Frekvens | Starttid | Dessa dagar | Vid dessa timmar | Vid dessa minuter | Obs! |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Kör varje kvart (inget datum och tid) | 15 | Minut | {Ingen} | {tillgänglig} | {Ingen} | {Ingen} | Det här schemat startar omedelbart och beräknar framtida repetitioner baserat på senaste körning. | 
| Kör var 15: e minut (med datum och tid) | 15 | Minut | *startDate*T*startTime*Z | {tillgänglig} | {Ingen} | {Ingen} | Det här schemat startar inte *alla snabbare* än angivet datum och tid beräknas framtida repetitioner baserat på senaste körning. | 
| Kör varje timme på timmen (med datum och tid) | 1 | Timme | *startDate*Thh:00:00Z | {tillgänglig} | {Ingen} | {Ingen} | Det här schemat startar inte *alla snabbare* än det angivna start datum och tid. Framtida repetitioner körs varje timme vid ”00” minut märket. <p>Om frekvensen är ”vecka” eller ”månad”, körs schemat respektive bara en dag i veckan eller en dag i månaden. | 
| Kör varje timme, varje dag (inget datum och tid) | 1 | Timme | {Ingen} | {tillgänglig} | {Ingen} | {Ingen} | Det här schemat startar omedelbart och beräknar framtida repetitioner baserat på senaste körning. <p>Om frekvensen är ”vecka” eller ”månad”, körs schemat respektive bara en dag i veckan eller en dag i månaden. | 
| Kör varje timme, varje dag (med datum och tid) | 1 | Timme | *startDate*T*startTime*Z | {tillgänglig} | {Ingen} | {Ingen} | Det här schemat startar inte *alla snabbare* än angivet datum och tid beräknas framtida repetitioner baserat på senaste körning. <p>Om frekvensen är ”vecka” eller ”månad”, körs schemat respektive bara en dag i veckan eller en dag i månaden. | 
| Kör var 15: e minut efter timmen, varje timme (med datum och tid) | 1 | Timme | *startDate*T00:15:00Z | {tillgänglig} | {Ingen} | {Ingen} | Det här schemat startar inte *alla snabbare* än den angivna startdatum och starttid, kör kl 00:15, 1:15 AM, 2:15 AM, och så vidare. | 
| Kör var 15: e minut efter timmen, varje timme (inget datum och tid) | 1 | Dag | {Ingen} | {tillgänglig} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Schemat körs klockan 00:15, 1:15 AM, 2:15 AM, och så vidare. Det här schemat är också motsvarar en frekvens som ”timme” och en starttid med ”15” minuter. | 
| Kör var 15: e minut i 15 minut märket (inget datum och tid) | 1 | Dag | {Ingen} | {tillgänglig} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Det här schemat startas inte förrän nästa angetts 15 minuter markering. | 
| Kör på 8:00:00 varje dag (inget datum och tid) | 1 | Dag | {Ingen} | {tillgänglig} | 8 | {Ingen} | Schemat körs på 8:00:00 varje dag, baserat på det angivna schemat. | 
| Kör på 8:00:00 varje dag (med datum och tid) | 1 | Dag | *startDate*T08:00:00Z | {tillgänglig} | {Ingen} | {Ingen} | Schemat körs 8:00:00 varje dag, baserat på den angivna starttiden. | 
| Kör på 8:30:00 varje dag (inget datum och tid) | 1 | Dag | {Ingen} | {tillgänglig} | 8 | 30 | Schemat körs på 8:30:00 varje dag, baserat på det angivna schemat. | 
| Kör på 8:30:00 varje dag (med datum och tid) | 1 | Dag | *startDate*T08:30:00Z | {tillgänglig} | {Ingen} | {Ingen} | Det här schemat startar på det angivna startdatumet kl 8:30. | 
| Kör på 8:30 och 4:30:00 varje dag | 1 | Dag | {Ingen} | {tillgänglig} | 8, 16 | 30 | | 
| Kör på 8:30 AM, 8:45 AM, 4:30 PM och 4:45 PM varje dag | 1 | Dag | {Ingen} | {tillgänglig} | 8, 16 | 30, 45 | | 
| Kör varje lördag kl (inget datum och tid) | 1 | Vecka | {Ingen} | ”Lördag” | 17 | 0 | Schemat körs varje lördag kl: 00. | 
| Kör varje lördag kl (med datum och tid) | 1 | Vecka | *startDate*T17:00:00Z | ”Lördag” | {Ingen} | {Ingen} | Det här schemat startar inte *alla snabbare* än det angivna start datum och tid, i det här fallet 9 September 2017 17:00:00. Framtida repetitioner kör varje lördag kl: 00. | 
| Kör varje tisdag, torsdag kl | 1 | Vecka | {Ingen} | ”Tisdag”, ”torsdag” | 17 | {Ingen} | Schemat körs varje tisdag och torsdag kl: 00. | 
| Kör varje timme under arbetstid | 1 | Vecka | {Ingen} | Välj alla dagar utom lördag och söndag. | Välj tidpunkter på dagen som du vill. | Välj några minuter för den som du vill. | Till exempel om din arbetstid 8:00:00 till 5:00, välj ”8, 9, 10, 11, 12, 13, 14, 15, 16, 17” som tidpunkter på dagen. <p>Om din arbetstid är 8:30 AM till 5:30 PM, markerar du de föregående timmarna dagen plus ”30” minuter för timmen. | 
| Kör en gång för varje dag på helger | 1 | Vecka | {Ingen} | ”Lördag”, ”söndag” | Välj tidpunkter på dagen som du vill. | Välj några minuter för den efter behov. | Schemat körs varje lördag och söndag på det angivna schemat. | 
| Kör var 15: e minut varannan vecka på måndagen endast | 2 | Vecka | {Ingen} | ”Måndag” | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Schemat körs varannan måndag vid position var 15 minuter. | 
| Kör varje timme under en dag i månaden | 1 | Månad | {se anmärkning} | {tillgänglig} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {se anmärkning} | Om du inte anger ett datum och tid, använder det här schemat datum och tid. Ange timme, en starttid minut eller Använd tiden för skapandet av för att styra minuter för upprepningsschemat. Till exempel om starttiden eller Skapandetid är 8:25 AM, det här schemat har körts på 8:25 AM, 9:25 AM, 10:25 AM, och så vidare. | 
||||||||| 

<a name="start-time"></a>

**F:** vad är hur kan jag använda startdatum och starttid? </br>
**S:** följer vissa mönster som visar hur du kan styra upprepning med datum och tid och hur Logic Apps motorn utför dessa repetitioner:

| Starttid | Upprepning utan schema | Upprepning med schema | 
| ---------- | --------------------------- | ------------------------ | 
| {Ingen} | Kör den första arbetsbelastningen omedelbart. <p>Kör framtida arbetsbelastningar baserat på senaste körning. | Kör den första arbetsbelastningen omedelbart. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. | 
| Starttiden tidigare | Beräknar körtider baserat på den angivna starttiden och rensningar senaste kör gånger. Körs första arbetsbelastningen vid nästa framtiden körtiden. <p>Kör framtida arbetsbelastningar baserat på beräkningar från den senaste körning. <p>Flera förklaring finns i exemplet som följer den här tabellen. | Kör den första arbetsbelastningen *tidigast* än starttiden enligt det schema som beräknats från starttiden. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. <p>**Obs:** om du anger en upprepning med ett schema, men inte timmar eller minuter för schemat och sedan framtida körtider beräknas med hjälp av timmar eller minuter, från den första körning. | 
| Starttid för närvarande eller i framtiden | Kör den första arbetsbelastningen på den angivna starttiden. <p>Kör framtida arbetsbelastningar baserat på beräkningar från den senaste körning. | Kör den första arbetsbelastningen *tidigast* än starttiden enligt det schema som beräknats från starttiden. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. <p>**Obs:** om du anger en upprepning med ett schema, men inte timmar eller minuter för schemat och sedan framtida körtider beräknas med hjälp av timmar eller minuter, från den första körning. | 
||||

**Exempel på en tidigare starttid med återkommande men inget schema** 

| Starttid | Aktuell tid | Upprepning | Schema |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09 -**07**T14:00:00Z | 2017-09 -**08**T13:00:00Z | Varje 2 dagar | {Ingen} | 
||||| 

I det här scenariot Logikappar motorn beräknar körtider baserat på starttid, tar du bort tidigare körtider och använder nästa framtiden starttid för första gången du kör. Efter den första körningen baserat framtida körs på schemat beräknas från starttiden. Här är hur upprepningen ser ut:

| Starttid | Första körning | Framtiden körtider | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** på 2:00 PM | 2017-09 -**09** på 2:00 PM | 2017-09 -**11** på 2:00 PM </br>2017-09 -**13** på 2:00 PM </br>2017-09 -**15** på 2:00 PM </br>och så vidare...
||||| 

Så för det här scenariot, oavsett hur långt i det förflutna som du anger du start tid, till exempel 2017-09 -**05** på 2:00 PM eller 2017-09 -**01** på 2:00:00 kör din första gången är samma.

## <a name="next-steps"></a>Nästa steg

* [Åtgärder och utlösare för arbetsflöde](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Anslutningsappar](../connectors/apis-list.md)
