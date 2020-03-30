---
title: Schemalägga återkommande uppgifter och arbetsflöden i Azure Logic Apps
description: En översikt över hur du schemalägger återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270568"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Schemalägg och kör återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps

Logic Apps hjälper dig att skapa och köra automatiserade återkommande uppgifter och processer enligt ett schema. Genom att skapa ett arbetsflöde för logikappar som börjar med en inbyggd återkommande utlösare eller utlösare av skjutfönster, som är triggers av schematyp, kan du köra aktiviteter direkt, vid ett senare tillfälle eller med ett återkommande intervall. Du kan ringa tjänster i och utanför Azure, till exempel HTTP- eller HTTPS-slutpunkter, skicka meddelanden till Azure-tjänster som Azure Storage och Azure Service Bus, eller hämta filer till en filresurs. Med utlösaren Återkommande kan du också ställa in komplexa scheman och avancerade upprepningar för att köra aktiviteter. Mer information om de inbyggda utlösande faktorerna och åtgärderna schemalägger finns i [Schemalägga utlösare](#schedule-triggers) och [schemalägg åtgärder](#schedule-actions). 

> [!TIP]
> Du kan schemalägga och köra återkommande arbetsbelastningar utan att skapa en separat logikapp för varje schemalagt jobb och köra in i [gränsen för arbetsflöden per region och prenumeration](../logic-apps/logic-apps-limits-and-config.md#definition-limits). I stället kan du använda logikappmönstret som skapas av [Azure QuickStart-mallen: Logic Apps jobbschemaläggare](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> I jobbschematarmallen Logic Apps skapas en Logikapp för CreateTimerJob som anropar en TimerJob-logikapp. Du kan sedan anropa Logikappen CreateTimerJob som ett API genom att göra en HTTP-begäran och skicka ett schema som indata för begäran. Varje anrop till Logic App CreateTimerJob anropar också logic-appen TimerJob, som skapar en ny TimerJob-instans som körs kontinuerligt baserat på det angivna schemat eller tills en angiven gräns möts. På så sätt kan du köra så många TimerJob-instanser som du vill utan att behöva oroa dig för arbetsflödesbegränsningar eftersom instanser inte är enskilda logikapparbetsflödesdefinitioner eller resurser.

I den här listan visas några exempel på aktiviteter som du kan köra med inbyggda schemautlösare:

* Hämta interna data, till exempel köra en SQL-lagrad procedur varje dag.

* Hämta externa data, till exempel hämta väderrapporter från NOAA var 15:e minut.

* Skicka rapportdata, till exempel skicka en sammanfattning via e-post för alla order som är större än ett visst belopp under den senaste veckan.

* Bearbeta data, till exempel komprimera dagens uppladdade bilder varje vardag under lågtrafik.

* Rensa data, till exempel ta bort alla tweets som är äldre än tre månader.

* Arkivera data, till exempel skicka fakturor till en säkerhetskopieringstjänst klockan 01:00 varje dag under de kommande nio månaderna.

Du kan också använda de inbyggda åtgärderna Schema för att pausa arbetsflödet innan nästa åtgärd körs, till exempel:

* Vänta tills en veckodag för att skicka en statusuppdatering via e-post.

* Fördröja arbetsflödet tills ett HTTP-anrop har tid att slutföra innan du återupptar och hämtar resultatet.

I den här artikeln beskrivs funktionerna för inbyggda utlösare och åtgärder i Schema.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Schemautlösare

Du kan starta logikapparbetsflödet med utlösaren Återkommande eller Sliding Window, som inte är associerad med någon specifik tjänst eller något specifikt system, till exempel Office 365 Outlook eller SQL Server. Dessa utlösare startar och kör arbetsflödet baserat på din angivna upprepning där du väljer intervall och frekvens, till exempel antalet sekunder, minuter och timmar för båda utlösare, eller antalet dagar, veckor eller månader för upprepningsutlösaren. Du kan också ange startdatum och starttid samt tidszonen. Varje gång en utlösare utlöses skapar och kör Logic Apps en ny arbetsflödesinstans för logikappen.

Här är skillnaderna mellan dessa utlösare:

* **Återkommande**: Kör arbetsflödet med jämna mellanrum baserat på det angivna schemat. Om upprepningar missas bearbetas inte upprepningsutlösaren de missade upprepningarna utan startar om upprepningar med nästa schemalagda intervall. Du kan ange ett startdatum och en starttid samt tidszonen. Om du väljer "Dag" kan du ange timmar på dagen och minuter per timme, till exempel varje dag klockan 2:30. Om du väljer "Vecka" kan du också välja veckodagar, till exempel onsdag och lördag. Mer information finns i [Skapa, schemalägga och köra återkommande uppgifter och arbetsflöden med utlösaren Återkommande](../connectors/connectors-native-recurrence.md).

* **Skjutfönster:** Kör arbetsflödet med jämna mellanrum som hanterar data i kontinuerliga segment. Om upprepningar missas går utlösaren glidande fönster tillbaka och bearbetar de missade upprepningarna. Du kan ange ett startdatum och en starttid, en tidszon och en varaktighet som ska försena varje upprepning i arbetsflödet. Den här utlösaren har inga alternativ för att ange dagar, veckor och månader, timmar på dagen, minuter per timme och veckodagar. Mer information finns i [Skapa, schemalägga och köra återkommande uppgifter och arbetsflöden med utlösaren Skjutfönster](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Schemalägga åtgärder

När en åtgärd har åtgärd i logikappens arbetsflöde kan du använda åtgärderna Fördröjning och Fördröjning tills du gör arbetsflödet väntar innan nästa åtgärd körs.

* **Fördröjning**: Vänta med att köra nästa åtgärd för det angivna antalet tidsenheter, till exempel sekunder, minuter, timmar, dagar, veckor eller månader. Mer information finns [i Fördröja nästa åtgärd i arbetsflöden](../connectors/connectors-native-delay.md).

* **Fördröjning till**: Vänta med att köra nästa åtgärd tills det angivna datumet och tiden. Mer information finns [i Fördröja nästa åtgärd i arbetsflöden](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Mönster för startdatum och starttid

<a name="start-time"></a>

Här är några mönster som visar hur du kan styra upprepning med startdatum och starttid och hur Logic Apps-tjänsten kör dessa upprepningar:

| Starttid | Upprepning utan schema | Återkommande med schema (endast återkommande utlösare) |
|------------|-----------------------------|----------------------------------------------------|
| {ingen} | Kör den första arbetsbelastningen direkt. <p>Kör framtida arbetsbelastningar baserat på den senaste körningstiden. | Kör den första arbetsbelastningen direkt. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. |
| Starttid tidigare | **Återkommande** utlösare: Beräknar körtider baserat på den angivna starttiden och ignorerar tidigare körningstider. Kör den första arbetsbelastningen vid nästa framtida körning. <p>Kör framtida arbetsbelastningar baserat på beräkningar från den senaste körningstiden. <p><p>**Skjutfönsterutlösaren:** Beräknar körtider baserat på den angivna starttiden och hedrar tidigare körningstider. <p>Kör framtida arbetsbelastningar baserat på beräkningar från den angivna starttiden. <p><p>Mer förklaring finns i exemplet efter den här tabellen. | Kör den första arbetsbelastningen *tidigast* starttiden, baserat på schemat som beräknas från starttiden. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. <p>**Anm.:** Om du anger en upprepning med ett schema, men inte anger timmar eller minuter för schemat, beräknas framtida körningstider med timmar respektive minuter från den första körningstiden. |
| Starttid för närvarande eller i framtiden | Kör den första arbetsbelastningen vid den angivna starttiden. <p>Kör framtida arbetsbelastningar baserat på beräkningar från den senaste körningstiden. | Kör den första arbetsbelastningen *tidigast* starttiden, baserat på schemat som beräknas från starttiden. <p>Kör framtida arbetsbelastningar baserat på det angivna schemat. <p>**Anm.:** Om du anger en upprepning med ett schema, men inte anger timmar eller minuter för schemat, beräknas framtida körningstider med timmar respektive minuter från den första körningstiden. |
||||

> [!IMPORTANT]
> När upprepningar inte anger avancerade schemaläggningsalternativ baseras framtida upprepningar på den senaste körningstiden.
> Starttiderna för dessa upprepningar kan drivas på grund av faktorer som svarstid under lagringsanrop. Om du vill vara säker på att logikappen inte missar en upprepning, särskilt när frekvensen är i dagar eller längre, använder du något av följande alternativ:
> 
> * Ange en starttid för upprepningen.
> 
> * Ange timmar och minuter för när upprepningen ska köras med hjälp av egenskaperna **Vid dessa timmar** och vid dessa **minuter.**
> 
> * Använd [utlösaren skjutfönster](../connectors/connectors-native-sliding-window.md)i stället för utlösaren För återkommande.

*Exempel för tidigare starttid och upprepning men inget schema*

Anta att aktuellt datum och tid är den 8 september 2017 klockan 13:00. Du anger startdatum och starttid som 7 september 2017 klockan 14:00, vilket är tidigare, och en upprepning som körs varannan dag.

| Starttid | Aktuell tid | Upprepning | Schema |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-**07** kl 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09-**08** kl 13:00) | Varannan dag | {ingen} |
|||||

För upprepningsutlösaren beräknar Logic Apps-motorn körtider baserat på starttiden, ignorerar tidigare körtider, använder nästa framtida starttid för den första körningen och beräknar framtida körningar baserat på den senaste körningstiden.

Så här ser upprepningen ut:

| Starttid | Första körtiden | Framtida körtider |
|------------|----------------|------------------|
| 2017-09-**07** kl 14:00 | 2017-09-**09** kl 14:00 | 2017-09-**11** kl 14:00 </br>2017-09-**13** kl 14:00 </br>2017-09-**15** kl 14:00 </br>och så vidare ... |
||||

Så, oavsett hur långt tidigare du anger starttiden, till exempel 2017-09-**05** på 2:00 PM eller 2017-09-**01** kl 14:00, använder din första körning alltid nästa framtida starttid.

För utlösaren Skjutfönster beräknar Logic Apps-motorn körtider baserat på starttiden, hedrar tidigare körtider, använder starttiden för den första körningen och beräknar framtida körningar baserat på starttiden.

Så här ser upprepningen ut:

| Starttid | Första körtiden | Framtida körtider |
|------------|----------------|------------------|
| 2017-09-**07** kl 14:00 | 2017-09-**07** kl 14:00 | 2017-09-**09** kl 14:00 </br>2017-09-**11** kl 14:00 </br>2017-09-**13** kl 14:00 </br>2017-09-**15** kl 14:00 </br>och så vidare ... |
||||

Så, oavsett hur långt tidigare du anger starttiden, till exempel 2017-09-**05** på 2:00 PM eller 2017-09-**01** kl 14:00, använder din första körning alltid den angivna starttiden.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Exempel på återkommande

Här är olika exempel på upprepningar som du kan ställa in för de utlösare som stöder alternativen:

| Utlösare | Upprepning | Intervall | Frequency | Starttid | Dessa dagar | Vid dessa timmar | Vid dessa minuter | Obs! |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Återkommande <br>Skjutfönster | Kör var 15:e minut (inget startdatum och starttid) | 15 | Minut | {ingen} | {ej tillgänglig} | {ingen} | {ingen} | Det här schemat startar omedelbart och beräknar sedan framtida upprepningar baserat på den senaste körningstiden. |
| Återkommande <br>Skjutfönster | Kör var 15:e minut (med startdatum och starttid) | 15 | Minut | *startDate* T*startTime*Z | {ej tillgänglig} | {ingen} | {ingen} | Det här schemat startar inte *tidigare* än det angivna startdatumet och starttiden och beräknar sedan framtida upprepningar baserat på den senaste körningstiden. |
| Återkommande <br>Skjutfönster | Kör varje timme, på timmen (med startdatum och starttid) | 1 | Timme | *startDate* Thh:00:00Z Thh:00:00Z Thh:00:00Z Thh | {ej tillgänglig} | {ingen} | {ingen} | Det här schemat startar inte *tidigare* än det angivna startdatum och den angivna starttiden. Framtida upprepningar körs varje timme vid minutmarkeringen "00", som beräknas från starttiden. <p>Om frekvensen är "Vecka" eller "Månad" körs det här schemat bara en dag per vecka eller en dag per månad. |
| Återkommande <br>Skjutfönster | Kör varje timme, varje dag (inget startdatum och -tid) | 1 | Timme | {ingen} | {ej tillgänglig} | {ingen} | {ingen} | Det här schemat startar omedelbart och beräknar framtida upprepningar baserat på den senaste körningstiden. <p>Om frekvensen är "Vecka" eller "Månad" körs det här schemat bara en dag per vecka eller en dag per månad. |
| Återkommande <br>Skjutfönster | Kör varje timme, varje dag (med startdatum och tid) | 1 | Timme | *startDate* T*startTime*Z | {ej tillgänglig} | {ingen} | {ingen} | Det här schemat startar inte *tidigare* än det angivna startdatumet och starttiden och beräknar sedan framtida upprepningar baserat på den senaste körningstiden. <p>Om frekvensen är "Vecka" eller "Månad" körs det här schemat bara en dag per vecka eller en dag per månad. |
| Återkommande <br>Skjutfönster | Kör var 15:e minut efter timmen, varje timme (med startdatum och starttid) | 1 | Timme | *startDate* T00:15:00Z | {ej tillgänglig} | {ingen} | {ingen} | Det här schemat startar inte *tidigare* än det angivna startdatum och den angivna starttiden. Framtida repetitioner körs på "15" minut märke, som beräknas från starttiden, så vid 00:15, 01:15, 02:15 och så vidare. |
| Upprepning | Kör var 15:e minut efter timmen, varje timme (inget startdatum och starttid) | 1 | Day | {ingen} | {ej tillgänglig} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Det här schemat pågår 00:15, 01:15, 02:15 och så vidare. Dessutom motsvarar det här schemat en frekvens av "Timme" och en starttid med "15" minuter. |
| Upprepning | Kör var 15:e minut vid de angivna minutmarkeringarna (inget startdatum och starttid). | 1 | Day | {ingen} | {ej tillgänglig} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Det här schemat startar inte förrän nästa angivna 15-minutersmarkering. |
| Upprepning | Kör dagligen klockan 8 *plus* minutmarkeringen från när du sparar logikappen | 1 | Day | {ingen} | {ej tillgänglig} | 8 | {ingen} | Utan startdatum och starttid körs det här schemat baserat på den tid då du sparar logikappen (PUT-åtgärden). |
| Upprepning | Kör dagligen klockan 8:00 (med startdatum och starttid) | 1 | Day | *startDate* T08:00:00Z | {ej tillgänglig} | {ingen} | {ingen} | Det här schemat startar inte *tidigare* än det angivna startdatum och den angivna starttiden. Framtida händelser körs dagligen klockan 8:00. | 
| Upprepning | Kör dagligen klockan 8:30 (inget startdatum och -tid) | 1 | Day | {ingen} | {ej tillgänglig} | 8 | 30 | Detta schema körs klockan 8:30 varje dag. |
| Upprepning | Kör dagligen på 8:30 och 4:30 | 1 | Day | {ingen} | {ej tillgänglig} | 8, 16 | 30 | |
| Upprepning | Kör dagligen på 8:30, 8:45, 4:30, och 4:45 | 1 | Day | {ingen} | {ej tillgänglig} | 8, 16 | 30, 45 | |
| Upprepning | Kör varje lördag klockan 17.00 (inget startdatum och starttid) | 1 | Vecka | {ingen} | "Lördag" | 17 | 00 | Detta schema körs varje lördag kl 17:00. |
| Upprepning | Kör varje lördag kl 17:00 (med startdatum och tid) | 1 | Vecka | *startDate* T17:00:00Z | "Lördag" | {ingen} | {ingen} | Det här schemat startar inte *tidigare* än det angivna startdatumet och starttiden, i det här fallet den 9 september 2017 klockan 17:00. Framtida repetitioner körs varje lördag kl 17:00. |
| Upprepning | Kör varje tisdag, torsdag klockan 17.00 *plus* minutmarkeringen från när du sparar logikappen| 1 | Vecka | {ingen} | "Tisdag", "Torsdag" | 17 | {ingen} | |
| Upprepning | Kör varje timme under arbetstid | 1 | Vecka | {ingen} | Välj alla dagar utom lördag och söndag. | Välj de timmar på dagen som du vill använda. | Välj några minuter i timmen som du vill. | Om arbetstiden till exempel är 8:00 till 17:00 väljer du "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" som timmar på dagen. <p>Om din arbetstid är 8:30 till 17:30 väljer du de föregående timmarna på dagen plus "30" som minuter i timmen. |
| Upprepning | Kör en gång varje dag på helgerna | 1 | Vecka | {ingen} | "Lördag", "Söndag" | Välj de timmar på dagen som du vill använda. | Välj några minuter av timmen efter behov. | Det här schemat körs varje lördag och söndag enligt det angivna schemat. |
| Upprepning | Kör var 15:e minut varannan vecka endast på måndagar | 2 | Vecka | {ingen} | "Måndag" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Detta schema körs varannan måndag vid varje 15-minuters märke. |
| Upprepning | Kör varje månad | 1 | Month | *startDate* T*startTime*Z | {ej tillgänglig} | {ej tillgänglig} | {ej tillgänglig} | Det här schemat startar inte *tidigare* än det angivna startdatum och den angivna starttiden och beräknar framtida upprepningar på startdatum och starttid. Om du inte anger ett startdatum och en starttid används datum och tid för att skapa det här schemat. |
| Upprepning | Kör varje timme under en dag i månaden | 1 | Month | {se anmärkning} | {ej tillgänglig} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {se anmärkning} | Om du inte anger ett startdatum och en starttid används datum och tid för att skapa det här schemat. Om du vill styra minuterna för upprepningsschemat anger du minuterna för timmen, en starttid eller använder skapandetiden. Om starttiden eller skapandetiden till exempel är 08:25 körs det här schemat 08:25, 09:25, 10:25 och så vidare. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Kör bara en gång

Om du bara vill köra logikappen samtidigt i framtiden kan du använda mallen **Scheduler: Kör en gång** jobb. När du har skapat en ny logikapp men innan du öppnar Logic Apps Designer väljer du **Schema**i listan Kategori i listan **Mallar** och väljer sedan den här mallen: **Category**

![Välj mallen "Schemaläggaren: Kör en gång jobb"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Eller, om du kan starta logikappen med **när en HTTP-begäran tas emot - Begäran utlösare,** och skicka starttiden som en parameter för utlösaren. Använd åtgärden **Fördröjning till - Schemalägg** och ange tid för när nästa åtgärd börjar köras.

## <a name="next-steps"></a>Nästa steg

* [Skapa, schemalägga och köra återkommande uppgifter och arbetsflöden med utlösaren Återkommande](../connectors/connectors-native-recurrence.md)
* [Skapa, schemalägga och köra återkommande uppgifter och arbetsflöden med utlösaren Skjutfönster](../connectors/connectors-native-sliding-window.md)
* [Pausa arbetsflöden med fördröjningsåtgärder](../connectors/connectors-native-delay.md)
