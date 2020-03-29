---
title: Skapa avancerade jobbscheman och upprepningar
description: Lär dig hur du skapar avancerade scheman och upprepningar för jobb i Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: b85932bf0d4fd080afadef2bc28d6a218b2d627a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898593"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Skapa avancerade scheman och upprepningar för jobb i Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som [dras tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du har konfigurerat i Scheduler [migrerar](../scheduler/migrate-from-scheduler-to-logic-apps.md) du till Azure Logic Apps så snart som möjligt. 
>
> Scheduler är inte längre tillgängligt i Azure-portalen, men [REST API-](/rest/api/scheduler) och [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) förblir tillgängliga just nu så att du kan hantera dina jobb och jobbsamlingar.

Inom ett [Azure Scheduler-jobb](../scheduler/scheduler-intro.md) är schemat den kärna som avgör när och hur Scheduler-tjänsten kör jobbet. Du kan ställa in flera engångs- och återkommande scheman för ett jobb med Scheduler. Engångsscheman körs bara en gång vid en viss tidpunkt och är i princip återkommande scheman som bara körs en gång. Återkommande scheman körs med en angiven frekvens. Med den här flexibiliteten kan du använda Scheduler för olika affärsscenarier, till exempel:

* **Rensa data regelbundet**: Skapa ett dagligt jobb som tar bort alla tweets som är äldre än tre månader.

* **Arkivdata**: Skapa ett månadsjobb som skickar fakturahistorik till en säkerhetskopieringstjänst.

* **Begär externa data**: Skapa ett jobb som körs var 15:e minut och hämtar en ny väderrapport från NOAA.

* **Processbilder**: Skapa ett veckodagsjobb som körs under lågtrafik och använder molnbaserad databehandling för att komprimera bilder som laddas upp under dagen.

I den här artikeln beskrivs exempeljobb som du kan skapa med Scheduler och [AZURE Scheduler REST API](/rest/api/scheduler)och innehåller JSON-definitionen (JavaScript Object Notation) för varje schema. 

## <a name="supported-scenarios"></a>Scenarier som stöds

De här exemplen visar de olika scenarier som Azure Scheduler stöder och hur du skapar scheman för olika beteendemönster, till exempel:

* Kör en gång vid ett visst datum och en viss tid.
* Kör och upprepa ett visst antal gånger.
* Kör omedelbart och återkommer.
* Kör och återkommer var *n* minut, timmar, dagar, veckor eller månader, med början vid en viss tidpunkt.
* Kör och återkommer veckovis eller månadsvis, men endast på specifika veckodagar eller på specifika dagar i månaden.
* Kör och återkommer mer än en gång för en viss period. Till exempel varje månad den sista fredagen och måndagen, eller dagligen klockan 05:15 och 17:15.

I den här artikeln beskrivs senare dessa scenarier mer i detalj.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Skapa schema med REST API

Så här skapar du ett grundläggande schema med [AZURE Scheduler REST API:](/rest/api/scheduler)

1. Registrera din Azure-prenumeration hos en resursprovider med hjälp av [REST-APIN För register - Resurshanteraren](https://docs.microsoft.com/rest/api/resources/providers). Providernamnet för Azure Scheduler-tjänsten är **Microsoft.Scheduler**. 

1. Skapa en jobbsamling med hjälp av [åtgärden Skapa eller uppdatera för projektsamlingar](https://docs.microsoft.com/rest/api/scheduler/jobcollections) i SCHEDULER REST API. 

1. Skapa ett jobb med hjälp av [åtgärden Skapa eller uppdatera för jobb](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Schemaelement för jobb

Den här tabellen ger en översikt på hög nivå för de större JSON-element som du kan använda när du ställer in upprepningar och scheman för jobb. 

| Element | Krävs | Beskrivning | 
|---------|----------|-------------|
| **startTime** | Inga | Ett DateTime-strängvärde i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601) som anger när jobbet startar i ett grundläggande schema. <p>För komplexa scheman startar jobbet tidigast **startTid**. | 
| **recurrence** | Inga | Upprepningsreglerna för när jobbet körs. Upprepningsobjektet stöder dessa element: **frekvens,** **intervall,** **recurrence** **schema,** **antal**och **endTime**. <p>Om du använder **upprepningselementet** måste du också använda **frekvenselementet,** medan andra **återkommande** element är valfria. |
| **Frekvens** | Ja, när du använder **återkommande** | Tidsenheten mellan förekomster och stöder dessa värden: "Minut", "Timme", "Dag", "Vecka", "Månad" och "År" | 
| **Intervall** | Inga | Ett positivt heltal som bestämmer antalet tidsenheter mellan förekomster baserat på **frekvens**. <p>Om till exempel **intervallet** är 10 och **frekvensen** är "Vecka" återkommer jobbet var 10:e vecka. <p>Här är det mesta antalet intervall för varje frekvens: <p>- 18 månader <br>- 78 veckor <br>- 548 dagar <br>- I timmar och minuter är intervallet 1 <= <*intervall*> <= 1000. | 
| **Schema** | Inga | Definierar ändringar i upprepningen baserat på angivna minutmärken, timmarkeringar, veckodagar och dagar i månaden | 
| **antal** | Inga | Ett positivt heltal som anger hur många gånger jobbet körs innan det avslutas. <p>När ett dagligt jobb till exempel har **angetts** till 7 och startdatumet är måndag, avslutas jobbet på söndag. Om startdatumet redan har passerat beräknas det första körninget från skapandet. <p>Utan **endTime** eller **count**körs jobbet oändligt. Du kan inte använda både **antal** och **endTime** i samma jobb, men regeln som avslutas först respekteras. | 
| **endTime** | Inga | Ett datum- eller DateTime-strängvärde i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601) som anger när jobbet slutar köras. Du kan ange ett värde för **endTime** som finns i det förflutna. <p>Utan **endTime** eller **count**körs jobbet oändligt. Du kan inte använda både **antal** och **endTime** i samma jobb, men regeln som avslutas först respekteras. |
|||| 

I det här JSON-schemat beskrivs till exempel ett grundläggande schema och återkommande för ett jobb: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Datum och DateTime-värden*

* Datum i Scheduler-jobb inkluderar endast datumet och följer [ISO 8601-specifikationen](https://en.wikipedia.org/wiki/ISO_8601).

* Datumtider i Scheduler-jobb inkluderar både datum och tid, följer [ISO 8601-specifikationen](https://en.wikipedia.org/wiki/ISO_8601)och antas vara UTC när ingen UTC-förskjutning har angetts. 

Mer information finns i [Begrepp, terminologi och entiteter](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Detaljer: startTime

I den här tabellen beskrivs hur **startTime** styr hur ett jobb körs:

| startTime | Ingen upprepning | Återkommande, inget schema | Upprepning med schema |
|-----------|---------------|-------------------------|--------------------------|
| **Ingen starttid** | Spring en gång omedelbart. | Spring en gång omedelbart. Kör senare körningar som beräknats från den senaste körningstiden. | Spring en gång omedelbart. Kör senare körningar baserat på ett återkommande schema. | 
| **Starttid tidigare** | Spring en gång omedelbart. | Beräkna den första framtida körningstiden efter starttid och kör då. <p>Kör senare körningar som beräknats från den senaste körningstiden. <p>Se exemplet efter den här tabellen. | Starta jobbet *tidigast* den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden. <p>Kör senare körningar baserat på ett återkommande schema. | 
| **Starttid i framtiden eller aktuell tid** | Kör en gång vid den angivna starttiden. | Kör en gång vid den angivna starttiden. <p>Kör senare körningar som beräknats från den senaste körningstiden. | Starta jobbet *tidigast* den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden. <p>Kör senare körningar baserat på ett återkommande schema. |
||||| 

Anta att du har det här exemplet med följande villkor: en starttid tidigare med en upprepning, men inget schema.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Aktuellt datum och tid är den 8 april 2015 kl.

* Startdatum och starttid är den 7 april 2015 klockan 14:00, vilket är före aktuellt datum och tid.

* Upprepningen sker varannan dag.

1. Under dessa förhållanden är den första avrättningen den 9 april 2015 kl 14:00. 

   Scheduler beräknar körningsförekomsterna baserat på starttiden, ignorerar alla instanser tidigare och använder nästa instans i framtiden. 
   I det här fallet är **startTime** den 7 april 2015 klockan 14:00, så nästa instans är två dagar från den tiden, vilket är den 9 april 2015 klockan 14:00.

   Den första körningen är densamma oavsett om **startTime** är 2015-04-05 14:00 eller 2015-04-01 14:00. Efter den första körningen beräknas senare körningar baserat på schemat. 
   
1. Avrättningarna följer sedan i denna ordning: 
   
   1. 2015-04-11 kl 14:00
   1. 2015-04-13 kl 14:00 
   1. 2015-04-15 kl 14:00
   1. Och så vidare...

1. Slutligen, när ett jobb har ett schema men inga angivna timmar och minuter, dessa värden standard till timmar och minuter i den första körningen, respektive.

<a name="schedule"></a>

## <a name="details-schedule"></a>Detaljer: schema

Du kan använda **schemat** för att *begränsa* antalet jobbkörningar. Om till exempel ett jobb med **en frekvens** av "månad" har ett schema som körs endast dag 31, körs jobbet bara i månader som har en 31:a dag.

Du kan också använda **schemat** för att *utöka* antalet jobbkörningar. Om till exempel ett jobb med **en frekvens** av "månad" har ett schema som körs på månad dag 1 och 2, körs jobbet på första och andra dagarna i månaden i stället för bara en gång i månaden.

Om du anger mer än ett schemaelement är utvärderingsordningen från den största till den minsta: veckonummer, månadsdag, veckodag, timme och minut.

I följande tabell beskrivs schemaelement i detalj:

| JSON-namn | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |
| **minutes** |Minuter i timmen då jobbet körs. |En matris med heltal. |
| **hours** |Timmar på dagen då jobbet körs. |En matris med heltal. |
| **weekDays** |Dagar i veckan jobbet körs. Kan endast anges med en veckofrekvens. |En matris med något av följande värden (maximal matrisstorlek är 7):<br />- "Måndag"<br />- "Tisdag"<br />- "Onsdag"<br />- "Torsdag"<br />- "Fredag"<br />- "Lördag"<br />- "Söndag"<br /><br />Inte skiftlägeskänsligt. |
| **monthlyOccurrences** |Bestämmer vilka dagar i månaden jobbet körs. Kan endast anges med en månatlig frekvens. |En matris med **månatligaoccurrences-objekt:**<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dagen** är den dag i veckan som jobbet körs. *{Söndag}* är till exempel varje söndag i månaden. Krävs.<br /><br />**förekomst** av dagen under månaden. *{Söndag, -1}* är till exempel den sista söndagen i månaden. Valfri. |
| **monthDays** |Dag i månaden jobbet körs. Kan endast anges med en månatlig frekvens. |En matris med följande värden:<br />– Ett värde <= -1 och >= -31<br />– Ett värde > = 1 och < = 31|

## <a name="examples-recurrence-schedules"></a>Exempel: Återkommande scheman

Följande exempel visar olika upprepningsscheman. Exemplen fokuserar på schemaobjektet och dess underelement.

Dessa scheman förutsätter att **intervallet** är inställt på 1\. Exemplen förutsätter också rätt **frekvensvärden** för värdena i **schemat**. Du kan till exempel inte använda en **frekvens** av "dag" och har en **monthDays-ändring** i **schemat**. Vi beskriver dessa begränsningar tidigare i artikeln.

| Exempel | Beskrivning |
|:--- |:--- |
| `{"hours":[5]}` |Kör klockan 5 varje dag.<br /><br />Scheduler matchar varje värde i "timmar" med varje värde i "minuter", en efter en, för att skapa en lista över alla de tider då jobbet körs. |
| `{"minutes":[15], "hours":[5]}` |Kör kl. 05.15 varje dag. |
| `{"minutes":[15], "hours":[5,17]}` |Kör kl. 05.15 och 17.15 varje dag |
| `{"minutes":[15,45], "hours":[5,17]}` |Kör kl. 05.15, 5.45, 17.15 och 17.45 varje dag. |
| `{"minutes":[0,15,30,45]}` |Kör var 15:e minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Kör varje timme.<br /><br />Det här jobbet körs varje timme. Minuten styrs av värdet för **startTime**, om det anges. Om inget **startTime-värde** anges styrs minuten av skapandetiden. Om starttiden eller skapandetiden (beroende på vilket som gäller) till exempel är 12:25 körs jobbet 00:25, 01:25, 02:25, ..., 23:25.<br /><br />Schemat är detsamma som ett jobb med en **frekvens** på "timme", ett **intervall** på 1 och inget **schemavärde.** Skillnaden är att du kan använda det här schemat med olika **frekvens-** och **intervallvärden** för att skapa andra jobb. Om **frekvensen** till exempel är "månad" körs schemat bara en gång i månaden i stället för varje dag (om **frekvensen** är "dag"). |
| `{minutes:[0]}` |Körs varje hel timme.<br /><br />Detta jobb körs också varje timme, men på timmen (12 AM, 01:00, 02:00, och så vidare). Det här schemat är samma som ett jobb med en **frekvens** av "timme", ett **startTime-värde** på noll minuter och inget **schema**, om frekvensen är "dag". Men om **frekvensen** är "vecka" eller "månad", körs schemat bara en dag i veckan eller en dag i månaden. |
| `{"minutes":[15]}` |Kör på 15 minuter över timmen varje timme.<br /><br />Körs varje timme, med början 00:15, 01:15, 02:15 och så vidare. Det slutar 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Kör klockan 17.00 på lördag varje vecka. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Kör klockan 17.00 på måndagar, onsdagar och fredagar varje vecka. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Körs 17.15 och 17.45 varje måndag, onsdag och fredag. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Kör klockan 05.00 och 17.00 på måndagar, onsdagar och fredagar varje vecka. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Kör på 5:15, 05:45, 5:15, och 5:45 på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Körs var 15:e minut på vardagar. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Kör var 15:e minut på vardagar, mellan 09.00 och 16.45. |
| `{"weekDays":["sunday"]}` |Kör på söndagar vid starttid. |
| `{"weekDays":["tuesday", "thursday"]}` |Kör på tisdagar och torsdagar vid starttid. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Kör klockan 06.00 den 28:e dagen i varje månad (förutsatt att en **frekvens** av "månad"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Kör klockan 06.00 den sista dagen i månaden.<br /><br />Om du vill köra ett jobb den sista dagen i en månad använder du -1 i stället för dag 28, 29, 30 eller 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Kör klockan 06.00 den första och sista dagen i varje månad. |
| `{monthDays":[1,-1]}` |Kör den första och sista dagen i varje månad vid starttid. |
| `{monthDays":[1,14]}` |Kör den första och 14: e dagen i varje månad vid starttid. |
| `{monthDays":[2]}` |Kör den andra dagen i månaden vid starttid. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Kör den första fredagen i varje månad kl 05:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Kör den första fredagen i varje månad vid starttid. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Kör den tredje fredagen från slutet av månaden, varje månad, vid starttid. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Körs den första och sista fredagen i varje månad 05.15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Kör den första och sista fredagen i varje månad vid starttid. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Kör den femte fredagen i varje månad vid starttid.<br /><br />Om det inte blir femte fredagen på en månad, går inte jobbet. Du kan överväga att använda -1 i stället för 5 för förekomsten om du vill köra jobbet på den sista som inträffar fredag i månaden. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Körs var 15:e minut den sista fredagen i månaden. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Körs 05.15, 05.45, 17.15 och 17.45 den tredje onsdagen varje månad. |

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)