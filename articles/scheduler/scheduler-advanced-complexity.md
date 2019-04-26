---
title: Skapa avancerade scheman för datalagerjobb och upprepningar – Azure Scheduler
description: Lär dig att skapa avancerade scheman och upprepningar för jobb i Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: a413261d251c8dfc1de9209168ee8137b85009f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531813"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Skapa avancerade scheman och upprepningar för jobb i Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras tillbaka. Om du vill schemalägga jobb kan du [testa Azure Logic Apps istället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Inom en [Azure Scheduler](../scheduler/scheduler-intro.md) jobb, schemat är kärnan i som bestämmer när och hur tjänsten Schemaläggaren körs jobbet. Du kan ange flera enstaka och återkommande scheman för ett jobb med Schemaläggaren. Enstaka scheman kör bara en gång vid en viss tidpunkt och är i princip återkommande scheman som bara körs en gång. Återkommande scheman körs på en angiven frekvens. Med den här flexibiliteten, kan du använda Scheduler för olika affärsscenarier, till exempel:

* **Rensa data regelbundet**: Skapa ett dagliga jobb som tar bort alla tweets som är äldre än tre månader.

* **Arkivera data**: Skapa ett månatligt jobb att push-meddelanden fakturera historiken till en tjänst för säkerhetskopiering.

* **Begär externa data**: Skapa ett jobb som körs varje kvart och tar emot en ny väderleksrapport från amerikanska NOAA.

* **Behandla bilder**: Skapa en veckodag jobb som körs vid låg belastning och använder molnbaserad databehandling för komprimering av bilder som laddats upp under dagen.

Den här artikeln beskriver exempel-jobb som du kan skapa med hjälp av Scheduler och [Azure Scheduler REST API](/rest/api/scheduler), och innehåller definitionen JavaScript Object Notation (JSON) för varje schema. 

## <a name="supported-scenarios"></a>Scenarier som stöds

De här exemplen visar vilka scenarier som har stöd för Azure Scheduler och skapa scheman för olika beteendemönster, till exempel:

* Kör en gång vid ett specifikt datum och tid.
* Kör och utför ett visst antal gånger.
* Kör omedelbart och upprepas.
* Kör och upprepas varje *n* minuter, timmar, dagar, veckor eller månader, med början vid en viss tidpunkt.
* Kör och upprepas varje vecka eller varje månad, men endast på specifika dagar i veckan eller särskilda dagar i månaden.
* Kör och upprepas mer än en gång för en viss tidsperiod. Till exempel varje månad på den sista fredagen och måndag, eller varje dag kl 5:15 och 17:15:00.

Den här artikeln beskrivs senare dessa scenarier mer utförligt.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Skapa schema med REST API

Skapa ett grundläggande schema med den [Azure Scheduler REST API](/rest/api/scheduler), Följ dessa steg:

1. Registrera din Azure-prenumeration med en resursprovider genom att använda den [registrera igen – Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/providers). Leverantörens namn för tjänsten Azure Scheduler är **Microsoft.Scheduler**. 

1. Skapa en jobbsamling med hjälp av den [skapa eller uppdatera åtgärden för jobbsamlingar](https://docs.microsoft.com/rest/api/scheduler/jobcollections) i Scheduler REST API. 

1. Skapa ett jobb med hjälp av den [skapa eller uppdatera åtgärden för jobb](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Schemaelement för jobbet

Den här tabellen innehåller en översikt för större JSON-element som du kan använda när du konfigurerar upprepningar och scheman för jobb. 

| Element | Obligatoriskt | Beskrivning | 
|---------|----------|-------------|
| **startTime** | Nej | Ett DateTime-strängvärde i [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601) som anger när jobbet startas första gången i ett grundläggande schema. <p>För komplexa scheman startar jobbet tidigast **startTime**. | 
| **recurrence** | Nej | Upprepningen regler för när jobbet körs. Den **upprepning** objektet stöder dessa element: **frekvens**, **intervall**, **schema**, **antal**, och **endTime**. <p>Om du använder den **upprepning** element, måste du också använda den **frekvens** element, medan andra **upprepning** element är valfria. |
| **frequency** | Ja, när du använder **upprepning** | Tidsenhet mellan förekomster och har stöd för dessa värden: ”Minute”, ”Hour”, ”Day”, ”Week”, ”Month” och ”år” | 
| **interval** | Nej | Ett positivt heltal som anger antalet tidsenheter mellan förekomster utifrån **frekvens**. <p>Till exempel om **intervall** är 10 och **frekvens** är ”Week” jobbet återkommer var 10 vecka. <p>Här är de mest antalet intervall för varje frekvens: <p>– 18 månader <br>-78 veckor <br>-548 dagar <br>– Intervallet är 1 för många timmar och minuter, < = <*intervall*>< = 1000. | 
| **schedule** | Nej | Definierar ändringar för upprepningen som baseras på de angivna minut-märkena, timme-märken, dagar i veckan och dagar i månaden | 
| **antal** | Nej | Ett positivt heltal som anger antalet gånger som jobbet körs innan du avslutar. <p>Till exempel när dagliga jobb har **antal** inställd på 7 och startdatum är måndag, jobbet har slutförts körs på söndag. Om startdatum har redan passerat, beräknas den första körningen från tiden för skapandet. <p>Utan **endTime** eller **antal**, körs jobbet oändligt. Du kan inte använda båda **antal** och **endTime** i samma jobb, men regeln att slutförs först är hanteras. | 
| **endTime** | Nej | Ett datum eller datum/tid-strängvärde i [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601) som anger när jobbet avslutades som körs. Du kan ange ett värde för **endTime** som är i förflutna. <p>Utan **endTime** eller **antal**, körs jobbet oändligt. Du kan inte använda båda **antal** och **endTime** i samma jobb, men regeln att slutförs först är hanteras. |
|||| 

Exempelvis beskriver det här JSON-schemat en grundläggande schema- och upprepningsinformation för ett jobb: 

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

*Datum- och DateTime-värden*

* Datum i Scheduler-jobb omfattar bara datumet och följ de [ISO 8601-specifikationen](https://en.wikipedia.org/wiki/ISO_8601).

* Datum / tid i Scheduler-jobb som innehåller både datum och tid, följ den [ISO 8601-specifikationen](https://en.wikipedia.org/wiki/ISO_8601), och antas vara UTC när ingen UTC-förskjutning har angetts. 

Mer information finns i [begrepp, terminologi och entiteter](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Information: startTime

Den här tabellen beskriver hur **startTime** styr hur ett jobb körs:

| startTime | Ingen upprepning | Upprepning utan schema | Upprepning med schema |
|-----------|---------------|-------------------------|--------------------------|
| **Inga starttid** | Kör en gång direkt. | Kör en gång direkt. Kör senare körningar som beräknas från senaste körningstid. | Kör en gång direkt. Kör senare körningar baserat på ett upprepningsschema. | 
| **Starttid tidigare** | Kör en gång direkt. | Beräkna första framtida körningstid efter starttiden och köra vid den tiden. <p>Kör senare körningar som beräknas från senaste körningstid. <p>Se exemplet efter den här tabellen. | Starta jobbet *tidigast* den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden. <p>Kör senare körningar baserat på ett upprepningsschema. | 
| **Starttid i framtiden eller aktuell tid** | Kör en gång vid den angivna starttiden. | Kör en gång vid den angivna starttiden. <p>Kör senare körningar som beräknas från senaste körningstid. | Starta jobbet *tidigast* den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden. <p>Kör senare körningar baserat på ett upprepningsschema. |
||||| 

Anta att du det här exemplet med dessa villkor: en starttid tidigare med upprepning men utan schema.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Aktuellt datum och tid är 08 April 2015 klockan 13:00.

* Startdatum och starttid är 07 April 2015 kl 2:00, som är före aktuellt datum och tid.

* Upprepningen är varannan dag.

1. Under dessa villkor är den första körningen 09 April 2015 kl 2:00. 

   Scheduler beräknar körningsförekomster baserat på starttid och ignorerar alla instanser i förflutna och använder nästa förekomst i framtiden. 
   I det här fallet **startTime** var 07 April 2015 2:00 PM, så nästa instans är två dagar från den tiden, vilket är 09 April 2015 kl 2:00.

   Den första körningen är samma oavsett om **startTime** är 2015-04-05 14:00 eller 2015-04-01 14:00. Efter den första körningen beräknas senare körningar baserat på schemat. 
   
1. Körningar följer sedan i den här ordningen: 
   
   1. 2015-04-11 kl 14:00:00
   1. 2015-04-13 kl 14:00:00 
   1. 2015-04-15 kl 14:00:00
   1. och så vidare...

1. Slutligen, när ett jobb har ett schema, men inga angivna timmar och minuter, dessa värden som standard timmar och minuter i den första körningen respektive.

<a name="schedule"></a>

## <a name="details-schedule"></a>Information: schema

Du kan använda **schema** till *gränsen* antalet jobbkörningar. Exempel: om ett jobb med en **frekvens** ”Month” har ett schema som bara körs på dag 31, jobbet körs bara i månader som har en 31: a dag.

Du kan också använda **schema** till *Expandera* antalet jobbkörningar. Exempel: om ett jobb med en **frekvens** ”Month” har ett schema som körs på dag 1 och 2, jobbet körs på första och andra dagen i månaden istället för bara en gång i månaden.

Om du anger fler än ett schema-element, den utvärderas de från största till minsta: veckonummer, dag i månaden, veckodag, timme och minut.

I följande tabell beskrivs schemaelement i detalj:

| JSON-namn | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |
| **minutes** |Minuter för den timme då jobbet körs. |En matris med heltal. |
| **hours** |Timmar på dagen då jobbet körs. |En matris med heltal. |
| **weekDays** |Dagar i veckan jobbet körs. Kan anges endast med en veckofrekvens. |En matris med något av följande värden (maximal matrisstorlek är 7):<br />-”Måndag”<br />-”Tisdag”<br />-”Onsdag”<br />-”Torsdag”<br />-”Fredag”<br />-”Lördag”<br />-”Sunday”<br /><br />Inte skiftlägeskänslig. |
| **monthlyOccurrences** |Anger vilka dagar i månaden jobbet körs. Kan anges endast med månatlig frekvens. |En matris med **monthlyOccurrences** objekt:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dag** är den veckodag som jobbet körs. Till exempel *{söndag}* varje söndag i månaden. Krävs.<br /><br />**förekomst** är förekomsten av dag under månaden. Till exempel *{söndag, -1}* är den sista söndagen i månaden. Valfri. |
| **monthDays** |Dag i månaden jobbet körs. Kan anges endast med månatlig frekvens. |En matris med följande värden:<br />– Ett värde <= -1 och >= -31<br />– Ett värde > = 1 och < = 31|

## <a name="examples-recurrence-schedules"></a>Exempel: Återkommande scheman

I följande exempel visas olika återkommande scheman. Exempel fokus på schemaobjektet och dess underelement.

Dessa scheman förutsätts att **intervall** har angetts till 1\. I exemplen antar vi rätt **frekvens** värden för värdena i **schema**. Exempel: du kan inte använda en **frekvens** ”Day” och har en **monthDays** ändringen i **schema**. Beskriver vi dessa begränsningar tidigare i artikeln.

| Exempel | Beskrivning |
|:--- |:--- |
| `{"hours":[5]}` |Körs 05: 00 varje dag.<br /><br />Scheduler matchar upp varje värde i ”timmar” med varje värde i ”minuter”, ett i taget, att skapa en lista med tidpunkter då jobbet körs. |
| `{"minutes":[15], "hours":[5]}` |Kör kl. 05.15 varje dag. |
| `{"minutes":[15], "hours":[5,17]}` |Kör kl. 05.15 och 17.15 varje dag |
| `{"minutes":[15,45], "hours":[5,17]}` |Kör kl. 05.15, 5.45, 17.15 och 17.45 varje dag. |
| `{"minutes":[0,15,30,45]}` |Kör var 15:e minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Kör varje timme.<br /><br />Det här jobbet körs varje timme. Minut styrs av värdet för **startTime**, om det har angetts. Om ingen **startTime** värde har angetts, minut styrs av tiden för skapandet. Exempel: om starttiden eller skapandetiden (beroende på vilken som gäller) är 12:25 PM, körs jobbet 00.25, 01.25, 02.25, …, 23:25.<br /><br />Schemat är samma som ett jobb med en **frekvens** ”HOUR”, en **intervall** 1 och inget **schema** värde. Skillnaden är att du kan använda det här schemat med annan **frekvens** och **intervall** värden för att skapa andra jobb. Till exempel om **frekvens** är ”month” körs schemat bara en gång i månaden istället för varje dag (om **frekvens** är ”day”). |
| `{minutes:[0]}` |Körs varje hel timme.<br /><br />Det här jobbet körs även varje timme, men på timmen (kl. 12, 1 AM, 2 AM och så vidare). Det här schemat är samma som ett jobb med en **frekvens** ”HOUR”, en **startTime** värdet noll minuter och inget **schema**om frekvensen är ”day”. Men om den **frekvens** är ”week” eller ”month” körs schemat bara en gång i veckan eller en gång i månaden respektive. |
| `{"minutes":[15]}` |Körs 15 minuter efter timmen varje timme.<br /><br />Körs varje timme, kl. 00:15:00, 13:15:00, 14:15:00 och så vidare. Det slutar kl 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Körs 17: 00 på lördagar varje vecka. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Körs 17: 00 på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Körs 17.15 och 17.45 varje måndag, onsdag och fredag. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Körs 05: 00 och 17: 00 på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Kör kl 5:15, 05:45:00, 17:15:00 och 17:45 på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Körs var 15:e minut på vardagar. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Kör var 15: e minut på vardagar mellan 09: 00 och 4:45 PM. |
| `{"weekDays":["sunday"]}` |Kör på söndagar vid start. |
| `{"weekDays":["tuesday", "thursday"]}` |Körs varje tisdag och torsdag på starttiden. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Körs 06: 00 den 28: e dagen varje månad (förutsatt att en **frekvens** ”Month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Körs 06: 00 den sista dagen i månaden.<br /><br />Om du vill köra ett jobb på den sista dagen i månaden, kan du använda -1 istället för dag 28, 29, 30 eller 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Körs 06: 00 på första och sista dagen i varje månad. |
| `{monthDays":[1,-1]}` |Kör den första och sista dagen i varje månad på starttiden. |
| `{monthDays":[1,14]}` |Kör den första och 14 dagen i varje månad på starttiden. |
| `{monthDays":[2]}` |Kör den andra dagen i månaden på starttiden. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Kör den första fredagen i varje månad 05: 00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Kör den första fredagen i varje månad på starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Kör den tredje fredagen från slutet av månaden, varje månad på starttiden. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Körs den första och sista fredagen i varje månad 05.15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Kör först och sista fredagen i varje månad på starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Kör den femte fredagen i varje månad på starttiden.<br /><br />Om det finns någon femte fredag i en månad körs inte jobbet. Du kan överväga att använda -1 istället för 5 som förekomst om du vill köra jobbet på den senaste inträffar fredagen i månaden. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Körs var 15:e minut den sista fredagen i månaden. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Körs 05.15, 05.45, 17.15 och 17.45 den tredje onsdagen varje månad. |

## <a name="see-also"></a>Se också

* [Vad är Azure Scheduler?](scheduler-intro.md)
* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)
