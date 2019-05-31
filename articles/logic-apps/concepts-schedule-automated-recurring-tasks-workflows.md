---
title: Schemalägga återkommande uppgifter och arbetsflöden i Azure Logic Apps
description: En översikt om att schemalägga återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 7f15dc5b28a44dc8405e2f0524913e6012ebe380
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356054"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Schemalägga och köra återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps

Logic Apps kan du skapa och köra automatiserade återkommande uppgifter och processer på ett schema. Genom att skapa en logikapparbetsflöde som börjar med en inbyggd upprepning eller glidande fönstret utlösare som är schematyp utlösare, kan du köra uppgifter direkt, vid ett senare tillfälle eller på ett återkommande intervall. Du kan anropa tjänster inuti och utanför Azure, till exempel HTTP eller HTTPS-slutpunkter, skicka meddelanden till Azure-tjänster som Azure Storage och Azure Service Bus eller hämta filer till en filresurs. Med upprepningsutlösare, kan du också ställa in komplexa scheman och avancerad upprepningar för att köra uppgifter. Mer information om inbyggda schema-utlösare och åtgärder som finns i [schema och kör återkommande automatiserade uppgifter och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Här följer några exempel som visar typerna av uppgifter som du kan köra:

* Hämta interna data, till exempel kör en SQL-lagrade proceduren varje dag.

* Hämta externa data, till exempel pull väderprognoser från amerikanska NOAA var 15: e minut.

* Skicka rapportdata, till exempel e-post en samling med alla order som är större än ett specifikt belopp under den senaste veckan.

* Bearbeta data, till exempel compress idag har överförda bilder varje vardag vid låg belastning.

* Rensa data, till exempel ta bort alla tweets som är äldre än tre månader.

* Arkivera data, till exempel push fakturor till säkerhetskopieringstjänsten kl 01:00 varje dag under de kommande nio månaderna.

Du kan också använda de inbyggda schema-åtgärderna för att pausa arbetsflödet innan nästa åtgärd körs, till exempel:

* Vänta tills en veckodag att skicka en statusuppdatering via e-post.

* Fördröja arbetsflödet tills ett HTTP-anrop har tid att slutföra innan du återupptar och hämta resultatet.

Den här artikeln beskriver funktionerna för inbyggda schema-utlösare och åtgärder.

## <a name="schedule-triggers"></a>Schemautlösare

Du kan starta logikappens arbetsflöde med hjälp av upprepningsutlösare eller utlösare för glidande fönster som inte är kopplade till någon specifik tjänst eller ett system, till exempel Office 365 Outlook eller SQL Server. Dessa utlösare startar och kör ditt arbetsflöde baserat på ditt angivna upprepningen där du väljer intervall och frekvens, till exempel hur många sekunder, minuter och timmar för både utlösare eller antalet dagar, veckor eller månader för upprepningsutlösaren. Du kan också ange startdatum och tid samt den aktuella tidszonen. Varje gång som en utlösare utlöses Logic Apps skapar och kör en ny arbetsflödesinstans för din logikapp.

Här är skillnaderna mellan dessa utlösare:

* **Upprepning**: Kör ditt arbetsflöde med jämna mellanrum baserat på ditt angivna schema. Om upprepningar är missade upprepningsutlösaren behandlar inte missade upprepningar men startar om upprepningar med nästa schemalagda intervall. Du kan ange ett startdatum och tid samt den aktuella tidszonen. Om du väljer ”Day”, kan du ange dygnets timmar och minuter timme, till exempel varje dag kl 2:30. Om du väljer ”Week” kan du också välja dagar i veckan, till exempel onsdag och lördag. Mer information finns i [skapa, schema, och kör återkommande uppgifter och arbetsflöden med upprepningsutlösaren](../connectors/connectors-native-recurrence.md).

* **Skjutfönster**: Kör ditt arbetsflöde med jämna tidsintervall som hanterar data i segment om kontinuerlig. Om upprepningar är missade utlösare för glidande fönster går tillbaka och bearbetar missade upprepningar. Du kan ange ett startdatum och tid, tidszon och varaktighet för att fördröja varje upprepning i arbetsflödet. Den här utlösaren har inte alternativ för att ange dagar, veckor, och månader, timmar på dagen, minuter efter timmen och dagar i veckan. Mer information finns i [skapa, schema, och kör återkommande uppgifter och arbetsflöden med utlösare för glidande fönster](../connectors/connectors-native-sliding-window.md).

## <a name="schedule-actions"></a>Schemaläggningsåtgärder

Du kan använda åtgärderna som fördröjningen och fördröjning tills att arbetsflödet vänta innan nästa åtgärd körs efter en åtgärd som logikapparbetsflödet.

* **fördröjning**: Vänta med att köra nästa åtgärd för det angivna antalet tidsenheter, till exempel sekunder, minuter, timmar, dagar, veckor eller månader. Mer information finns i [fördröjning nästa åtgärd i arbetsflöden](../connectors/connectors-native-delay.md).

* **Fördröj tills**: Vänta med att köra instruktionen nästa fram till angivet datum och tid. Mer information finns i [fördröjning nästa åtgärd i arbetsflöden](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Mönster för startdatum och starttid

<a name="start-time"></a>

Här följer några mönster som visar hur du kan styra upprepning med startdatum och starttid och hur Logic Apps-tjänsterna körs dessa upprepningar:

| Starttid | Upprepning utan schema | Upprepning med schema (endast upprepningsutlösare) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Körs arbetsbelastningen första direkt. <p>Kör framtida arbetsbelastningar baserat på senaste körningstid. | Körs arbetsbelastningen första direkt. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. |
| Starttid tidigare | **Upprepning** utlösare: Beräknar körtider baserat på den angivna starttiden och rensningar senaste kör gånger. Kör den första arbetsbelastningen på nästa framtiden körtid. <p>Kör framtida arbetsbelastningar baserat på beräkningar från senaste körningstid. <p><p>**Skjutfönster** utlösare: Beräknar körtider baserat på den angivna starttiden och Min utbildning senaste kör gånger. <p>Kör framtida arbetsbelastningar baserat på beräkningar från den angivna starttiden. <p><p>Fler förklaringar finns i exemplet som följer den här tabellen. | Kör den första arbetsbelastningen *tidigast* än starttiden, baserat på schemat som beräknas från starttiden. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. <p>**Obs:** Om du anger en upprepning med ett schema, inte men timmar eller minuter för schemat, beräknas framtida körtider med timmar eller minuter, från den första körning. |
| Starttid för närvarande eller i framtiden | Körs den första arbetsbelastningen vid den angivna starttiden. <p>Kör framtida arbetsbelastningar baserat på beräkningar från senaste körningstid. | Kör den första arbetsbelastningen *tidigast* än starttiden, baserat på schemat som beräknas från starttiden. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. <p>**Obs:** Om du anger en upprepning med ett schema, inte men timmar eller minuter för schemat, beräknas framtida körtider med timmar eller minuter, från den första körning. |
||||

*Exempel för senaste starttid och upprepning men inget schema*

Anta att aktuellt datum och tid är den 8 September 2017 klockan 13:00. Du anger startdatum och tidpunkt som den 7 September 2017 på 2:00 PM, som är tidigare och en upprepning som körs varje 2 dagar.

| Starttid | Aktuell tid | Upprepning | Schema |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09 -**07** kl 2:00) | 2017-09-**08**T13:00:00Z <br>(2017-09 -**08** klockan 13:00) | Varje 2 dagar | {none} |
|||||

För upprepningsutlösaren, Logic Apps-motor beräknar körtider baserat på starttiden, ignorerar de senaste körtider, använder nästa framtiden starttid för första kör och beräknar framtiden körs baserat på senaste körningstid.

Här är hur den här upprepningen ser ut:

| Starttid | Första körning | Framtiden körtider |
|------------|----------------|------------------|
| 2017-09 -**07** på 2:00 PM | 2017-09 -**09** på 2:00 PM | 2017-09 -**11** på 2:00 PM </br>2017-09 -**13** på 2:00 PM </br>2017-09 -**15** på 2:00 PM </br>och så vidare... |
||||

I så fall oavsett hur långt du ange vid vilken tidpunkt, till exempel tidigare 2017-09 -**05** kl 2:00 eller 2017-09 -**01** på 2:00 PM, din första Kör alltid använder nästa framtiden starttid.

För utlösare för glidande fönster, Logic Apps-motor beräknar körtider baserat på starttiden, Min utbildning senaste körtider använder starttiden för den första körningen och beräknar framtida körningar baserat på starttiden.

Här är hur den här upprepningen ser ut:

| Starttid | Första körning | Framtiden körtider |
|------------|----------------|------------------|
| 2017-09 -**07** på 2:00 PM | 2017-09 -**07** på 2:00 PM | 2017-09 -**09** på 2:00 PM </br>2017-09 -**11** på 2:00 PM </br>2017-09 -**13** på 2:00 PM </br>2017-09 -**15** på 2:00 PM </br>och så vidare... |
||||

I så fall oavsett hur långt du ange vid vilken tidpunkt, till exempel tidigare 2017-09 -**05** kl 2:00 eller 2017-09 -**01** på 2:00 PM, din första Kör alltid använder den angivna starttiden.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Exempel upprepningar

Här följer olika exempel upprepningar som du kan konfigurera för utlösare som har stöd för alternativ:

| Utlösare | Upprepning | Interval | Frekvens | Starttid | Dessa dagar | Vid dessa timmar | Vid dessa minuter | Obs! |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Upprepning <br>Skjutfönster | Körs varje kvart (inga startdatum och tidpunkt) | 15 | Minut | {none} | {unavailable} | {none} | {none} | Det här schemat startar omedelbart och beräknar framtida upprepningar baserat på senaste körningstid. |
| Upprepning <br>Skjutfönster | Kör var 15: e minut (med startdatum och tidpunkt) | 15 | Minut | *startDate*T*startTime*Z | {unavailable} | {none} | {none} | Det här schemat inte startar *alla körningstillfället* än den angivna startdatum och starttid, beräknar sedan framtida upprepningar baserat på senaste körningstid. |
| Upprepning <br>Skjutfönster | Körs varje hel timme (med startdatum och tidpunkt) | 1 | Timme | *startDate*Thh:00:00Z | {unavailable} | {none} | {none} | Det här schemat inte startar *alla körningstillfället* än den angivna starta datum och tid. Framtida upprepningar körs varje timme ”00” minut märket, som beräknas från starttiden. <p>Om frekvensen är ”Week” eller ”Month”, körs schemat respektive bara en gång i veckan eller en dag i månaden. |
| Upprepning <br>Skjutfönster | Kör varje timme, varje dag (inga startdatum och tidpunkt) | 1 | Timme | {none} | {unavailable} | {none} | {none} | Det här schemat startar omedelbart och beräknar framtida upprepningar baserat på senaste körningstid. <p>Om frekvensen är ”Week” eller ”Month”, körs schemat respektive bara en gång i veckan eller en dag i månaden. |
| Upprepning <br>Skjutfönster | Kör varje timme, varje dag (med startdatum och tidpunkt) | 1 | Timme | *startDate*T*startTime*Z | {unavailable} | {none} | {none} | Det här schemat inte startar *alla körningstillfället* än den angivna startdatum och starttid, beräknar sedan framtida upprepningar baserat på senaste körningstid. <p>Om frekvensen är ”Week” eller ”Month”, körs schemat respektive bara en gång i veckan eller en dag i månaden. |
| Upprepning <br>Skjutfönster | Kör var 15 minuter efter timmen, varje timme (med startdatum och tidpunkt) | 1 | Timme | *startDate*T00:15:00Z | {unavailable} | {none} | {none} | Det här schemat inte startar *alla körningstillfället* än den angivna starta datum och tid. Framtida upprepningar som körs på ”15” minut märket, som beräknas från början tid, så kl. 00:15, 01:15:00, 14:15:00 och så vidare. |
| Upprepning | Körs varje kvart efter timmen, varje timme (inga startdatum och tidpunkt) | 1 | Dag | {none} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Schemat som körs kl. 00:15, 01:15:00, 14:15:00 och så vidare. Dessutom är det här schemat motsvarar en frekvens på ”Hour” och en starttid med ”15” minuter. |
| Upprepning | Kör var 15: e minut på de angivna minut som hämtas (inga startdatum och tidpunkt). | 1 | Dag | {none} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Det här schemat startar inte förrän nästa angetts 15 minuters mark. |
| Upprepning | Kör kl 8:00 varje dag (inga startdatum och tidpunkt) | 1 | Dag | {none} | {unavailable} | 8 | {none} | Schemat som körs kl 8:00 varje dag, baserat på det angivna schemat. |
| Upprepning | Kör kl 8:00 varje dag (med startdatum och tidpunkt) | 1 | Dag | *startDate*T08:00:00Z | {unavailable} | {none} | {none} | Schemat körs 8:00:00 varje dag, baserat på den angivna starttiden. | 
| Upprepning | Kör kl. 8:30 varje dag (inga startdatum och tidpunkt) | 1 | Dag | {none} | {unavailable} | 8 | 30 | Schemat som körs kl. 8:30 varje dag, baserat på det angivna schemat. |
| Upprepning | Kör kl. 8:30 varje dag (med startdatum och tidpunkt) | 1 | Dag | *startDate*T08:30:00Z | {unavailable} | {none} | {none} | Det här schemat startar på det angivna startdatumet kl 8:30. |
| Upprepning | Kör kl. 8:30 och 4:30:00 varje dag | 1 | Dag | {none} | {unavailable} | 8, 16 | 30 | |
| Upprepning | Kör kl. 8:30, 8:45 AM, 4:30 PM och 4:45 varje dag | 1 | Dag | {none} | {unavailable} | 8, 16 | 30, 45 | |
| Upprepning | Kör varje lördag 17: 00 (inga startdatum och tidpunkt) | 1 | Vecka | {none} | ”Lördag” | 17 | 00 | Schemat körs varje lördag kl 5:00. |
| Upprepning | Kör varje lördag kl (med startdatum och tidpunkt) | 1 | Vecka | *startDate*T17:00:00Z | ”Lördag” | {none} | {none} | Det här schemat inte startar *alla körningstillfället* än den angivna starta datum och tid, i det här fallet den 9 September 2017 17:00:00. Framtida upprepningar kör varje lördag kl 5:00. |
| Upprepning | Körs varje tisdag, torsdag 17: 00 | 1 | Vecka | {none} | "Tuesday", "Thursday" | 17 | {none} | Schemat körs varje tisdag och torsdag 17:00:00. |
| Upprepning | Kör varje timme under arbetstid | 1 | Vecka | {none} | Välj alla dagar utom lördag och söndag. | Välj tidpunkter på dagen som du vill. | Välj några minuter på den timma som önskas. | Exempel: om din arbetstid är 8:00:00 till 5:00, välj sedan ”8, 9, 10, 11, 12, 13, 14, 15, 16, 17” som timmar på dagen. <p>Om din arbetstid kl. 8:30 till 5:30 PM, väljer du de senaste timmarna dagen plus ”30” som minuter efter timmen. |
| Upprepning | Kör en gång varje dag på helger | 1 | Vecka | {none} | ”Lördag”, ”söndag” | Välj tidpunkter på dagen som du vill. | Välj några minuter för timmen efter behov. | Det här schemat körs varje lördag och söndag vid det angivna schemat. |
| Upprepning | Körs varje kvart varannan vecka på måndagar endast | 2 | Vecka | {none} | ”Måndag” | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Det här schemat körs varannan måndag vid varje 15 minuters mark. |
| Upprepning | Kör varje månad | 1 | Månad | *startDate*T*startTime*Z | {unavailable} | {unavailable} | {unavailable} | Det här schemat inte startar *alla körningstillfället* än den angivna startdatum och starttid och beräknar framtida upprepningar på startdatum och starttid. Om du inte anger ett startdatum och starttid, använder det här schemat datum och tid. |
| Upprepning | Kör varje timme under en dag i månaden | 1 | Månad | {se anmärkning} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {se anmärkning} | Om du inte anger ett startdatum och starttid, använder det här schemat datum och tid. Ange minuters timme, en starttid för att styra minuter för upprepningsschemat, eller Använd tiden för skapandet. Till exempel om starttiden eller Skapandetid är kl. 8:25, det här schemat körs 8:25, 9:25 AM, 10:25 AM, och så vidare. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Kör bara en gång

Om du vill köra din logikapp endast på en gång i framtiden, du kan använda den **Scheduler: Kör en gång jobb** mall. När du har skapat en ny logikapp, men innan du öppnar Logic Apps Designer under den **mallar** avsnittet från den **kategori** väljer **schema**, och välj sedan den här mallen:

![Välj ”Scheduler: Kör en gång jobb ”mall](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Eller, om du kan starta din logikapp med den **när en HTTP-begäran tas emot - begäran** utlösa och skicka starttiden som en parameter för utlösaren. Den första åtgärden använder den **Fördröj tills – schemalägga** åtgärd, och ange tiden för när nästa åtgärd börjar köras.

## <a name="next-steps"></a>Nästa steg

* [Skapa, schemalägga och kör återkommande uppgifter och arbetsflöden med upprepningsutlösaren](../connectors/connectors-native-recurrence.md)
* [Skapa, schemalägga och kör återkommande uppgifter och arbetsflöden med utlösare för glidande fönster](../connectors/connectors-native-sliding-window.md)
* [Pausa arbetsflöden med fördröjning åtgärder](../connectors/connectors-native-delay.md)