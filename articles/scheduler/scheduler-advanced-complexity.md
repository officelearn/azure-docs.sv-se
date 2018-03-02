---
title: "Skapa komplexa scheman och avancerade upprepning med Azure Schemaläggaren"
description: "Lär dig hur du skapar komplexa scheman och avancerade upprepning med Azure Schemaläggaren."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Skapa komplexa scheman och avancerade upprepning med Azure Schemaläggaren

Kärnan i ett jobb i Azure Schemaläggaren är schemat. Schemat bestämmer när och hur Schemaläggaren körs jobbet. 

Du kan använda Schemaläggaren för att ange flera enstaka och återkommande scheman för ett jobb. Enstaka scheman eller en gång vid en viss tidpunkt. Enstaka schema är ett effektivt sätt återkommande scheman som kör en gång. Återkommande scheman eller på en förutbestämd frekvens.

Du kan använda Schemaläggaren med den här flexibiliteten för en mängd olika affärsscenarier:

* **Rensning av periodiska data**. Till exempel varje dag, ta bort alla tweets som är äldre än tre månader.
* **Arkivering**. Till exempel varje månad, push fakturahistoriken till en säkerhetskopieringstjänsten.
* **Begäranden för externa data**. Till exempel var 15: e minut hämta en ny ski väderleksrapport från NOAA.
* **Bild bearbetning**. Till exempel använda varje veckodag vid låg belastning cloud computing-lösningar för att komprimera avbildningar som laddades upp den dagen.

I den här artikeln går vi igenom exempel jobb som du kan skapa med hjälp av Schemaläggaren. Vi ger JSON-data som beskriver varje schema. Om du använder den [Scheduler REST API](https://msdn.microsoft.com/library/mt629143.aspx), kan du använda samma JSON till [skapar ett jobb i Schemaläggaren](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Scenarier som stöds
Exemplen i den här artikeln visar scenarier som har stöd för Schemaläggaren bredd. Exemplen visar brett så här skapar du scheman för många beteende användningsmönster, inklusive:

* Kör en gång på ett visst datum och tid.
* Kör och upprepas ett visst antal gånger.
* Kör omedelbart och upprepas.
* Kör och upprepas var  *n*  minuter, timmar, dagar, veckor eller månader med början vid en viss tid.
* Kör och upprepas för varje vecka eller månad frekvens, men endast för särskilda dagar i veckan eller särskilda dagar i månaden.
* Kör och upprepas flera gånger under en period. Till exempel den sista fredagen och föregående måndag varje månad, eller på 5:15 AM och 17:15:00 varje dag.

## <a name="date-and-date-time"></a>Datum och tid
Date-referenser i Schemaläggaren jobben följer den [ISO 8601-specifikationen](http://en.wikipedia.org/wiki/ISO_8601), och omfattar endast datum.

Datum och tid referenser i schemaläggare följer den [ISO 8601-specifikationen](http://en.wikipedia.org/wiki/ISO_8601), och inkluderar både datum och tid. En tid som inte anger en UTC-förskjutningen antas vara UTC.  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>Använda JSON och REST-API för att skapa ett schema
Skapa ett grundläggande schema med hjälp av den [Scheduler REST API](https://msdn.microsoft.com/library/mt629143), första [registrera prenumerationen med en resursleverantör](https://msdn.microsoft.com/library/azure/dn790548.aspx). Providernamn för Schemaläggaren är **Microsoft.Scheduler**. Sedan [skapa en jobbsamling](https://msdn.microsoft.com/library/mt629159.aspx). Slutligen [skapar ett jobb](https://msdn.microsoft.com/library/mt629145.aspx). 

När du skapar ett jobb kan ange du schemaläggning och upprepning med hjälp av JSON, som i den här utdrag:

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>Jobbet schemat grunderna
Följande tabell innehåller en översikt över viktiga element som används för att ange upprepning och schemaläggning i ett jobb:

| JSON-namn | Beskrivning |
|:--- |:--- |
| **startTime** |Ett datum / tid-värde. För grundläggande scheman **startTime** är den första förekomsten. För komplexa scheman jobbet startar tidigast **startTime**. |
| **recurrence** |Anger återkommande regler för jobbet och upprepning då jobbet körs. Objektet återkommande stöder elementen **frekvens**, **intervall**, **endTime**, **antal**, och **schema**. Om **återkommande** definieras **frekvens** krävs. Andra **återkommande** element är valfria. |
| **frequency** |En sträng som representerar frekvens enheten att jobbet ska återkomma. Värden som stöds är ”minuter”, ”timmar”, ”dag”, ”vecka” och ”månad”. |
| **interval** |Ett positivt heltal. **intervallet** anger intervallet för den **frekvens** värde som avgör hur ofta jobbet körs. Till exempel om **intervall** är 3 och **frekvens** ”vecka” jobbets återkomster alla tre veckor.<br /><br />Schemaläggaren stöder maximalt **intervall** 18 för månatliga frekvens, 78 för frekvensen Varje vecka och 548 för dagliga frekvens. Intervallet som stöds är 1 för timme och minut frekvens < = **intervall** < = 1000. |
| **endTime** |En sträng som anger den tid efter vilken jobbet kan inte köras. Du kan ange ett värde för **endTime** som har inträffat. Om **endTime** och **antal** inte anges körs jobbet oändligt. Du kan inte innehålla både **endTime** och **antal** i samma jobb. |
| **Antal** |Heltalet (större än noll) som anger antalet gånger som jobbet körs innan den har slutförts.<br /><br />**antal** representerar antalet gånger som jobbet körs innan bestäms slutförts. Till exempel för ett jobb som körs varje dag med en **antal** av 5 och startdatum måndag jobbet slutförs efter körning fredag. Om startdatumet har varit, beräknas första körningen från tiden för skapandet av.<br /><br />Om inget **endTime** eller **antal** anges körs jobbet oändligt. Du kan inte innehålla både **endTime** och **antal** i samma jobb. |
| **schedule** |Ett jobb med angivna intervall ändrar dess upprepning baserat på ett återkommande schema. En **schema** värdet innehåller ändringar som baseras på minuter, timmar, veckodagar, dagar i månaden och veckonumret. |

## <a name="job-schema-defaults-limits-and-examples"></a>Jobbet schemat standarder, begränsningar och exempel
Senare i artikeln diskuterar vi var och en av följande element i detalj:

| JSON-namn | Värdetyp | Krävs? | Standardvärde | Giltiga värden | Exempel |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |sträng |Nej |Ingen |ISO 8601 datum |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |objekt |Nej |Ingen |Objektet upprepning |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |sträng |Ja |Ingen |”minuter”, ”timmar”, ”dag”, ”vecka”, ”månad” |`"frequency" : "hour"` |
| **interval** |nummer |Ja |Ingen |1 och 1000 |`"interval":10` |
| **endTime** |sträng |Nej |Ingen |Datetime-värde som representerar en tidpunkt i framtiden |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **Antal** |nummer |Nej |Ingen |>= 1 |`"count": 5` |
| **schedule** |objekt |Nej |Ingen |Schema-objekt |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>Djupdykning: startTime (starttid)
I följande tabell beskrivs hur **startTime** styr hur som ett jobb körs:

| startTime-värde | Ingen upprepning | Återkommande, utan schema | Upprepning med schema |
|:--- |:--- |:--- |:--- |
| **Ingen starttid** |Kör en gång direkt. |Kör en gång direkt. Kör efterföljande körningar beräknad tid för senaste körning. |Kör en gång direkt.<br /><br />Köra efterföljande körningar baserat på ett återkommande schema. |
| **Starttiden tidigare** |Kör en gång direkt. |Beräkna första framtida körningstid efter starttiden och köras vid den tiden.<br /><br />Kör efterföljande körningar beräknad tid för senaste körning. <br /><br />Mer information finns i exemplet som följer den här tabellen. |Jobbet startar *inte sooner än* den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<br /><br />Köra efterföljande körningar baserat på ett återkommande schema. |
| **Starttid i framtiden eller den aktuella tiden** |Kör en gång vid den angivna starttiden. |Kör en gång vid den angivna starttiden.<br /><br />Kör efterföljande körningar beräknad tid för senaste körning.|Jobbet startar *inte sooner än* den angivna starttiden. Den första förekomsten är baserad på det schema som beräknats från starttiden.<br /><br />Köra efterföljande körningar baserat på ett återkommande schema. |

Nu ska vi titta på ett exempel på vad som händer när **startTime** har varit med återkommande, men utan schema.  Anta att den aktuella tiden är 2015-04-08 13:00 **startTime** är 2015-04-07 14:00 och **återkommande** är två dagar (definieras med **frekvens**: dag och **intervall**: 2.) Observera att **startTime** har varit och inträffar före aktuell tid.

Under dessa förhållanden är den första körningen på 2015-04-09 på 14:00\. Scheduler-motor beräknar körningsförekomster från starttiden. Alla tidigare instanser ignoreras. Motorn använder nästa förekomst som förekommer i framtiden. I det här fallet **startTime** är 2015-04-07 på 2:00:00 så nästa förekomst är två dagar från den tid som är 2015-04-09 på 2:00 PM.

Observera att den första körningen skulle vara samma om **startTime** är 2015-04-05 14:00 eller 2015-04-01 14:00\. Efter den första körningen beräknas efterföljande körningar med hjälp av schemat. De kommer vara på 2015-04-11 på 2:00:00 sedan 2015-04-13 på 2:00 PM sedan 2015-04-15 på 2:00 PM och så vidare.

Slutligen, när ett jobb har ett schema om timmar och minuter har ställts in i schemat värden standard timmar och minuter efter den första körningen respektive.

## <a name="deep-dive-schedule"></a>Ingående: schema
Du kan använda **schema** till *gränsen* antalet jobb körningar. Om ett jobb med till exempel en **frekvens** ”månad” har ett schema som körs bara på dag 31, körs jobbet bara i månader som har en trettio första dag.

Du kan också använda **schema** till *Expandera* antalet jobb körningar. Om ett jobb med till exempel en **frekvens** ”månad” har ett schema som körs på 1 och 2 dagar i månaden, körs jobbet första och andra dagar i månaden i stället för bara en gång i månaden.

Om du anger flera schemaelement är utvärderingsordningen från det största till minsta: vecka, månad, dag, veckodag, timmar och minut.

I följande tabell beskrivs schemaelement i detalj:

| JSON-namn | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |
| **minutes** |Minuter av timmen då jobbet körs. |En heltalsmatris. |
| **hours** |Tidpunkter på dagen då jobbet körs. |En heltalsmatris. |
| **weekDays** |Dagar i veckan jobbet körs. Kan anges endast med en frekvens på varje vecka. |En matris med någon av följande värden (maximal matrislängd storlek är 7):<br />-”Måndag”<br />-”Tisdag”<br />-”Onsdag”<br />-”Torsdag”<br />-”Fredag”<br />-”Lördag”<br />-”Söndag”<br /><br />Inte skiftlägeskänsliga. |
| **monthlyOccurrences** |Anger vilka dagar i månaden jobbet körs. Kan anges endast med en frekvens på varje månad. |En matris med **monthlyOccurrences** objekt:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dag** dag i veckan jobbet körs. Till exempel *{söndag}* är varje söndag i månaden. Krävs.<br /><br />**förekomsten** är den dag under månaden. Till exempel *{söndag, -1}* är den sista söndagen i månaden. Valfri. |
| **monthDays** |Dagen i månaden jobbet körs. Kan anges endast med en frekvens på varje månad. |En matris av följande värden:<br />– Alla värdet < = -1 och > =-31<br />– Alla värde > = 1 och < = 31|

## <a name="examples-recurrence-schedules"></a>Exempel: Återkommande scheman
I följande exempel visas olika återkommande scheman. Exemplen fokuserar på det schema-objektet och dess underelement.

Dessa scheman antar som **intervall** har angetts till 1\. I exemplen antar vi rätt **frekvens** värden för värdena i **schema**. Du kan till exempel använda en **frekvens** ”dag” och har en **monthDays** ändring i **schema**. Vi beskriver dessa begränsningar tidigare i artikeln.

| Exempel | Beskrivning |
|:--- |:--- |
| `{"hours":[5]}` |Kör kl 5 varje dag.<br /><br />Schemaläggaren matchar varje värde i ”timmar” med varje värde i ”minuter”, ett i taget, för att skapa en lista med tidpunkter då jobbet körs. |
| `{"minutes":[15], "hours":[5]}` |Kör kl. 05.15 varje dag. |
| `{"minutes":[15], "hours":[5,17]}` |Kör kl. 05.15 och 17.15 varje dag |
| `{"minutes":[15,45], "hours":[5,17]}` |Kör kl. 05.15, 5.45, 17.15 och 17.45 varje dag. |
| `{"minutes":[0,15,30,45]}` |Kör var 15:e minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Kör varje timme.<br /><br />Det här jobbet körs varje timme. Minuten styrs av värdet för **startTime**, om den har angetts. Om inget **startTime** värde har angetts, minuten styrs av tiden för skapandet av. Till exempel om starttiden eller skapelsetid (beroende på vilket) är 12:25 PM, körs jobbet 00:25, 01:25 02:25,..., 23:25.<br /><br />Schemat som motsvarar ett jobb med en **frekvens** ”timme” en **intervall** 1 och Nej **schema** värde. Skillnaden är att du kan använda det här schemat med annan **frekvens** och **intervall** värden för att skapa andra jobb. Till exempel om **frekvens** är ”månad”, schemat körs bara en gång i månaden i stället för varje dag (om **frekvens** är ”dag”). |
| `{minutes:[0]}` |Körs varje hel timme.<br /><br />Det här jobbet körs även varje timme, men Timma (kl. 12, 1 AM, 2 AM och så vidare). Detta motsvarar ett jobb med en **frekvens** ”timme” en **startTime** värdet noll minuter och ingen **schema**om frekvensen är ”dag”. Men om den **frekvens** är ”vecka” eller ”månad”, schemat körs bara en dag, en vecka eller en dag i månaden, respektive. |
| `{"minutes":[15]}` |Kör på 15 minuter efter timmen varje timme.<br /><br />Körs varje timme från kl 00:15, 1:15 AM, 2:15 AM, och så vidare. Den slutar kl 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Köras på lördag kl varje vecka. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Kör kl måndag, onsdag och fredag varje vecka. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Körs 17.15 och 17.45 varje måndag, onsdag och fredag. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Kör på 5 AM och 17: 00 på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Kör kl 5:15, 5:45 AM, 17:15:00 och 17:45:00 på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Körs var 15:e minut på vardagar. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Kör var 15: e minut på veckodagar mellan 9: 00 och 4:45 PM. |
| `{"weekDays":["sunday"]}` |Kör på söndagar vid start. |
| `{"weekDays":["tuesday", "thursday"]}` |Kör varje tisdag och torsdag vid start. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Kör 6 kl 20 åttonde dagen varje månad (förutsatt att en **frekvens** ”månad”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Kör kl 6 på den sista dagen i månaden.<br /><br />Om du vill köra ett jobb på den sista dagen i månaden, Använd -1 i stället för dag 28, 29, 30 och 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Kör kl 6 på den första och sista dagen i månaden. |
| `{monthDays":[1,-1]}` |Kör den första och sista dagen i varje månad vid start. |
| `{monthDays":[1,14]}` |Kör den första och 14 dagen varje månad vid start. |
| `{monthDays":[2]}` |Kör den andra dagen i månaden vid start. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Kör den första fredagen i varje månad kl 5. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Kör den första fredagen i månaden vid start. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Kör den tredje fredagen från slutet av månad, varje månad, vid start. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Körs den första och sista fredagen i varje månad 05.15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Kör första och sista fredagen i varje månad vid start. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Kör den femte fredagen i månaden vid start.<br /><br />Om det finns inga femte fredagen i månaden, körs jobbet inte. Kan du med -1 i stället för 5 för förekomst om du vill köra jobbet på inträffar sista fredagen i månaden. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Körs var 15:e minut den sista fredagen i månaden. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Körs 05.15, 05.45, 17.15 och 17.45 den tredje onsdagen varje månad. |

## <a name="see-also"></a>Se också

- [Vad är Scheduler?](scheduler-intro.md)
- [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
- [Komma igång med Scheduler på Azure-portalen](scheduler-get-started-portal.md)
- [Prenumerationer och fakturering i Azure Scheduler](scheduler-plans-billing.md)
- [Referens för REST-API:et för Azure Scheduler](https://msdn.microsoft.com/library/mt629143)
- [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)
- [Azure Scheduler hög tillgänglighet och tillförlitlighet](scheduler-high-availability-reliability.md)
- [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)
- [Utgående autentisering i Azure Scheduler](scheduler-outbound-authentication.md)

