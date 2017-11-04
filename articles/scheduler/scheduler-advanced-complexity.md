---
title: "Hur du skapar komplexa scheman och avancerade upprepning med Azure Schemaläggaren"
description: "Hur du skapar komplexa scheman och avancerade upprepning med Azure Schemaläggaren"
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
ms.openlocfilehash: 20c3e3c1cb85308cad47054c2efa87f61cae0f22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Hur du skapar komplexa scheman och avancerade upprepning med Azure Schemaläggaren
## <a name="overview"></a>Översikt
Hjärtat i en Azure-Schemaläggaren jobbet är den *schema*. Schemat bestämmer när och hur Schemaläggaren körs jobbet.

Azure Schemaläggaren kan du ange olika enstaka och återkommande scheman för ett jobb. *Enstaka* scheman eller en gång vid en angiven tid – effektivt, de är *återkommande* scheman som kör en gång. Återkommande scheman eller på en förutbestämd frekvens.

Med den här flexibiliteten kan Azure Schemaläggaren du stöder ett stort antal affärsscenarier:

* Rensning av periodiska data – t.ex. varje dag, ta bort alla tweets som är äldre än tre månader
* Arkivering – t.ex. varje månad, push fakturahistorik till säkerhetskopieringstjänsten
* Begäranden för externa data – t.ex. Hämta var 15: e minut, nya ski Väderrapport från NOAA
* Bild bearbetning – t.ex. varje veckodag under lågtrafik använder cloud computing-lösningar för att komprimera bilder överförs den dagen

I den här artikeln vi att gå igenom exempel jobb som du kan skapa med Azure Schemaläggaren. Vi ger JSON-data som beskriver varje schema. Om du använder den [Scheduler REST API](https://msdn.microsoft.com/library/mt629143.aspx), kan du använda samma JSON för [skapar ett jobb i Azure Schemaläggaren](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Scenarier som stöds
Många exemplen i det här avsnittet visar scenarier som har stöd för Azure Scheduler bredd. Allmänna ordalag visar de här exemplen hur du skapar scheman för många beteende användningsmönster, inklusive som nedan:

* Kör en gång på ett visst datum och tid
* Kör och upprepas flera gånger explicit
* Kör omedelbart och upprepas
* Kör och upprepas var  *n*  minuter, timmar, dagar, veckor eller månader med början vid en viss tidpunkt
* Kör och upprepas för varje vecka eller månad frekvens men endast på specifika dagar, särskilda dagar i veckan eller särskilda dagar i månaden
* Kör och återkomma med flera gånger under en period – t.ex. sista fredagen och måndag varje månad, eller vid 5:15 am och 17:15:00 varje dag

## <a name="dates-and-datetimes"></a>Datum och datum och tid
Datum i Azure-schemaläggare följer den [ISO 8601-specifikationen](http://en.wikipedia.org/wiki/ISO_8601) och inkludera endast datum.

Datum och tid referenser i Azure-schemaläggare följer den [ISO 8601-specifikationen](http://en.wikipedia.org/wiki/ISO_8601) och inkluderar både datum och tid delar. En tid som inte anger en UTC-förskjutningen antas vara UTC.  

## <a name="how-to-use-json-and-rest-api-for-creating-schedules"></a>Så här: Använda JSON- och REST-API för att skapa scheman
Så här skapar du ett enkelt schema med den [Azure Scheduler REST API](https://msdn.microsoft.com/library/mt629143), första [registrera prenumerationen med en resursleverantör](https://msdn.microsoft.com/library/azure/dn790548.aspx) (providernamn för Schemaläggaren är *Microsoft.Scheduler*), sedan [skapa en jobbsamling](https://msdn.microsoft.com/library/mt629159.aspx), och slutligen [skapar ett jobb](https://msdn.microsoft.com/library/mt629145.aspx). När du skapar ett jobb kan ange du schemaläggning och upprepning med JSON som den utdrag nedan:

    {
        "startTime": "2012-08-04T00:00Z", // optional
         …
        "recurrence":                     // optional
        {
            "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
            "interval": 1,                // optional, how often to fire (default to 1)
            "schedule":                   // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // optional (default to recur infinitely)
            "endTime": "2012-11-04",      // optional (default to recur infinitely)
        },
        …
    }

## <a name="overview-job-schema-basics"></a>Översikt: Jobbet schemat grunderna
Följande tabell innehåller en översikt över viktiga element relaterade till upprepning och schemaläggning i ett jobb:

| **JSON-namn** | **Beskrivning** |
|:--- |:--- |
| ***startTime*** |*startTime* är datum och tid. För enkel scheman *startTime* är den första förekomsten och för komplexa scheman kommer jobbet att starta tidigast *startTime*. |
| ***upprepning*** |Den *återkommande* objektet anger återkommande regler för jobbet och återkommande jobbet körs med. Objektet återkommande stöder elementen *frekvens, intervall, endTime, count,* och *schema*. Om *återkommande* definieras *frekvens* krävs; andra element för *återkommande* är valfria. |
| ***frekvens*** |Den *frekvens* sträng som representerar frekvens enheten att jobbet ska återkomma. Värden som stöds är *”minuter”, ”timmar”, ”dag”, ”vecka”* eller *”månad”.* |
| ***intervall*** |Den *intervall* är ett positivt heltal och anger intervallet för den *frekvens* som avgör hur ofta jobbet ska köras. Till exempel om *intervall* är 3 och *frekvens* ”vecka” jobbets återkomster var 3 veckor. Azure Scheduler stöder maximalt *intervall* 18 månader för månatliga frekvens 78 veckor för frekvensen Varje vecka eller 548 dagar för daglig frekvens. Intervallet som stöds är 1 för timme och minut frekvens < = *intervall* < = 1000. |
| ***Sluttid*** |Den *endTime* sträng Anger tidigare datum och tid som jobbet inte ska köras. Det går inte att ha en *endTime* tidigare. Om inget *endTime* eller antal anges, körs jobbet oändligt. Båda *endTime* och *antal* kan inte ingå i samma jobb. |
| ***Antal*** |<p>Den *antal* är heltalet (större än noll) som anger antalet gånger som det här jobbet ska köras innan du Slutför.</p><p>Den *antal* representerar antalet gånger som jobbet körs innan bestäms som slutförd. Till exempel för ett jobb som körs varje dag med *antal* 5 och startdatum för måndag, jobbet slutförs efter körning fredag. Om startdatumet har varit, beräknas första körningen från tiden för skapandet av.</p><p>Om inget *endTime* eller *antal* anges körs jobbet oändligt. Båda *endTime* och *antal* kan inte ingå i samma jobb.</p> |
| ***schema*** |Ett jobb med angivna intervall ändrar dess upprepning baserat på ett återkommande schema. En *schema* innehåller ändringar som baseras på minuter, timmar, veckodagar, dagar i månaden och veckonumret. |

## <a name="overview-job-schema-defaults-limits-and-examples"></a>Översikt: Jobbet schemat standarder, begränsningar och exempel
Efter den här översikten vi beskrivs de här elementen i detalj.

| **JSON-namn** | **Värdetyp** | **Krävs?** | **Standardvärde** | **Giltiga värden** | **Exempel** |
|:--- |:--- |:--- |:--- |:--- |:--- |
| ***startTime*** |Sträng |Nej |Ingen |ISO 8601-datum/tid |<code>"startTime" : "2013-01-09T09:30:00-08:00"</code> |
| ***upprepning*** |Objekt |Nej |Ingen |Upprepningsobjekt |<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code> |
| ***frekvens*** |Sträng |Ja |Ingen |”minuter”, ”timmar”, ”dag”, ”vecka”, ”månad” |<code>"frequency" : "hour"</code> |
| ***intervall*** |Tal |Nej |1 |1 till 1000. |<code>"interval":10</code> |
| ***Sluttid*** |Sträng |Nej |Ingen |Datum/tid-värde som representerar en tidpunkt i framtiden |<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
| ***Antal*** |Tal |Nej |Ingen |>= 1 |<code>"count": 5</code> |
| ***schema*** |Objekt |Nej |Ingen |Schemaobjekt |<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code> |

## <a name="deep-dive-starttime"></a>Ingående: *startTime*
I följande tabell insamlingar hur *startTime* styr hur ett jobb körs.

| **startTime-värdet** | **Ingen upprepning** | **Upprepning. Inget schema** | **Återkommande schema** |
|:--- |:--- |:--- |:--- |
| **Ingen starttid** |Kör en gång direkt |Kör en gång direkt. Köra efterföljande körningar baserat på Beräkna från tid för senaste körning |<p>Kör en gång direkt</p><p>Kör efterföljande körningar baserat på upprepningsschemat</p> |
| **Starttid i förfluten tid** |Kör en gång direkt |<p>Beräkna första framtida körningstid efter starttiden och köras vid den tiden</p><p>Köra efterföljande körningar baserat oncalculating från tid för senaste körning</p><p>Se exempel efter den här tabellen för en ytterligare förklaring</p> |<p>Jobbet startar *inte sooner än* den angivna starttiden. Den första förekomsten är baserad på det schema som beräknats från starttiden</p><p>Kör efterföljande körningar baserat på upprepningsschemat</p> |
| **Starttid i framtiden eller för närvarande** |Kör en gång vid angivna start |<p>Kör en gång vid angivna start</p><p>Köra efterföljande körningar baserat på Beräkna från tid för senaste körning</p> |<p>Jobbet startar *inte sooner än* den angivna starttiden. Den första förekomsten är baserad på det schema som beräknats från starttiden</p><p>Kör efterföljande körningar baserat på upprepningsschemat</p> |

Låt oss se ett exempel på vad som händer där *startTime* är tidigare med *återkommande* men ingen *schema*.  Anta att den aktuella tiden är 2015-04-08 13:00 *startTime* är 2015-04-07 14:00 och *återkommande* är 2 dagar (definieras med *frekvens*: dag och *intervall*: 2.) Observera att den *startTime* har varit och inträffar före aktuell tid

Under dessa förhållanden visas den *första körningen* kommer att vara 2015-04-09 14:00\. Scheduler-motor beräknar körningsförekomster från starttiden.  Alla tidigare instanser ignoreras. Motorn använder nästa förekomst som förekommer i framtiden.  Så i detta fall *startTime* är 2015-04-07 på 2:00:00 så nästa förekomst är 2 dagar från den tid som är 2015-04-09 på 2:00 pm.

Observera att den första körningen skulle vara samma även om startTime 2015-04-05 14:00 eller 2015-04-01 14:00\. Efter den första körningen efterföljande körningar beräknas med hjälp av den planerade – så att de är utsatta för 2015-04-11 på 2:00:00 sedan 2015-04-13 på 2:00:00 sedan 2015-04-15 på 2:00:00 osv.

Slutligen, när ett jobb har ett schema om timmar eller minuter har ställts in i schemat är de som standard i timmar och minuter efter den första körningen respektive.

## <a name="deep-dive-schedule"></a>Ingående: *schema*
Dels måste en *schema* kan *gränsen* antalet jobb körningar.  Till exempel, om ett jobb med en frekvens som ”månad” har en *schema* att körs endast dag 31 jobbet körs i de månader som har en 31<sup>st</sup> dag.

Å andra sidan en *schema* kan också *Expandera* antalet jobb körningar. Till exempel, om ett jobb med en frekvens som ”månad” har en *schema* som körs på 1 och 2 dagar i månaden jobbet körs på 1<sup>st</sup> och 2<sup>nd</sup> dagar i månaden i stället för bara en gång i månaden.

Om flera schemaelement har angetts utvärderingsordningen är från den högsta till lägsta – number vecka, månad, dag, veckodag, timmar och minuter.

I följande tabell beskrivs *schema* element i detalj.

| **JSON-namn** | **Beskrivning** | **Giltiga värden** |
|:--- |:--- |:--- |
| **minuter** |Minuter efter den timme då jobbet ska köras |<ul><li>Heltal, eller</li><li>Heltalsmatris</li></ul> |
| **timmar** |Tidpunkter på dagen då jobbet ska köras |<ul><li>Heltal, eller</li><li>Heltalsmatris</li></ul> |
| **Veckodagar** |Dagar i veckan jobbet körs. Kan bara anges med veckofrekvens. |<ul><li>”Måndag”, ”tisdag”, ”onsdag”, ”torsdag”, ”fredag”, ”lördag” eller ”söndag”</li><li>Matris med någon av ovanstående värden (max. matrisstorlek 7)</li></ul>*Inte* skiftlägeskänsligt |
| **monthlyOccurrences** |Fastställer vilka dagar i månaden som jobbet ska köras. Kan bara anges med månadsfrekvens. |<ul><li>Matris med monthlyOccurrence objekt:</li></ul> <pre>{ "day": *day*,<br />  "occurrence": *occurrence*<br />}</pre><p> *dag* är dag i veckan jobbet körs, till exempel {söndag} är varje söndag i månaden. Krävs.</p><p>Förekomst är *förekomsten* dag under månaden, t.ex. {söndag, -1} är den sista söndagen i månaden. Valfri.</p> |
| **monthDays** |Dagen i månaden jobbet körs. Kan bara anges med månadsfrekvens. |<ul><li>Inget värde < = -1 och > =-31.</li><li>Ett värde > = 1 och < = 31.</li><li>En matris med ovanstående värden</li></ul> |

## <a name="examples-recurrence-schedules"></a>Exempel: Återkommande scheman
Följande är olika exempel på återkommande scheman – fokus på schema-objektet och dess underordnade element.

Scheman under alla anta att den *intervall* har angetts till 1\. Dessutom måste en förutsätter rätt frekvensen i enlighet med vad som finns i den *schema* – t.ex. en kan inte använda frekvens ”dag” och har en ändring av ”monthDays” i schemat. Dessa begränsningar som beskrivs ovan.

| **Exempel** | **Beskrivning** |
|:--- |:--- |
| <code>{"hours":[5]}</code> |Kör kl 5 varje dag. Azure Scheduler matchar varje värde i ”timmar” med varje värde i ”minuter”, ett i taget, för att skapa en lista med tidpunkter då jobbet som ska köras. |
| <code>{"minutes":[15], "hours":[5]}</code> |Kör kl 5:15 varje dag |
| <code>{"minutes":[15], "hours":[5,17]}</code> |Kör på 5:15 AM och 17:15:00 varje dag |
| <code>{"minutes":[15,45], "hours":[5,17]}</code> |Kör kl 5:15, 5:45 AM, 17:15:00 och 17:45:00 varje dag |
| <code>{"minutes":[0,15,30,45]}</code> |Kör var 15: e minut |
| <code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code> |Kör varje timme. Det här jobbet körs varje timme. Minuten styrs av den *startTime*, om någon sådan har angetts, eller om inget har angetts av tiden för skapandet av. Till exempel om starttiden eller skapelsetid (beroende på vilket) är 12:25 PM jobbet ska köras vid 00:25, 01:25 02:25,..., 23:25. Schemat är likvärdiga med ett jobb med *frekvens* ”timme” en *intervall* 1 och Nej *schema*. Skillnaden är att det här schemat kan användas med olika *frekvens* och *intervall* att skapa andra jobb för. Till exempel om den *frekvens* ”månad”, schemat skulle körs bara en gång i månaden i stället för varje dag om *frekvens* har ”dag” |
| <code>{minutes:[0]}</code> |Kör varje timme timma. Det här jobbet körs även varje timme, men Timma (t.ex. 12: 00, 1 kl 02: 00, etc.) Detta motsvarar ett jobb med frekvensen för ”timme”, startTime med noll minuter och inget schema om frekvensen ”dag”, men om frekvensen ”vecka” eller ”månad”, schemat skulle köra endast en dag, en vecka eller en dag i månaden, respektive. |
| <code>{"minutes":[15]}</code> |Kör i 15 minuter efter timme varje timme. Körs varje timme, med start 00:15, 01:15, 02:15 osv och slutar 22:15 och 23:15. |
| <code>{"hours":[17], "weekDays":["saturday"]}</code> |Kör kl på lördagar varje vecka |
| <code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Kör kl måndag, onsdag och fredag varje vecka |
| <code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Kör på 17:15:00 och 17:45:00 på måndag, onsdag och fredag varje vecka |
| <code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Kör på 5 AM och 17: 00 på måndag, onsdag och fredag varje vecka |
| <code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Kör kl 5:15, 5:45 AM, 17:15:00 och 17:45:00 på måndag, onsdag och fredag varje vecka |
| <code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Körs var 15: e minut på vardagar |
| <code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Kör var 15: e minut på vardagar mellan 9: 00 och 4:45 PM |
| <code>{"weekDays":["sunday"]}</code> |Kör på söndagar vid Start |
| <code>{"weekDays":["tuesday", "thursday"]}</code> |Kör varje tisdag och torsdag vid Start |
| <code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code> |Kör kl 6 på 28 dag av var månaden (förutsatt att frekvensen för månad) |
| <code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code> |Kör kl 6 på den sista dagen i månaden. Om du vill köra ett jobb på den sista dagen i månaden, Använd -1 i stället för dag 28, 29, 30 och 31. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code> |Kör kl 6 på den första och sista dagen i månaden |
| <code>{monthDays":[1,-1]}</code> |Kör den första och sista dagen i varje månad vid Start |
| <code>{monthDays":[1,14]}</code> |Kör den första och 14 dagen varje månad vid Start |
| <code>{monthDays":[2]}</code> |Kör den andra dagen i månaden vid Start |
| <code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |Kör på första fredagen i varje månad kl 5 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |: Köra första fredagen i varje månad vid Start |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code> |Kör tredje fredag från slutet av månad, varje månad, vid Start |
| <code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Kör på första och sista fredagen i varje månad kl 5:15 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Kör på första och sista fredagen i varje månad vid Start |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code> |Kör på femte fredagen i varje månad vid Start. Om det finns inga femte fredagen i månaden, körs detta inte, eftersom den har schemalagts för att köras på endast femte fredagar. Kan du med -1 i stället för 5 för förekomst om du vill köra jobbet på inträffar sista fredagen i månaden. |
| <code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code> |Kör var 15: e minut sista fredagen i månaden |
| <code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code> |Kör kl 5:15, 5:45 AM, 17:15:00 och 17:45:00 3 onsdagen varje månad |

## <a name="see-also"></a>Se även
 [Vad är Scheduler?](scheduler-intro.md)

 [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)

 [Komma igång med Scheduler på Azure-portalen](scheduler-get-started-portal.md)

 [Prenumerationer och fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Referens för REST-API:et för Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)

 [Hög tillgänglighet och tillförlitlighet i Azure Scheduler](scheduler-high-availability-reliability.md)

 [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Utgående autentisering i Azure Scheduler](scheduler-outbound-authentication.md)

