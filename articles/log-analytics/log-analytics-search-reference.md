---
title: "Azure logganalys söka referens | Microsoft Docs"
description: "Logganalys Sök referens beskriver sökspråk och innehåller allmänna frågesyntaxen alternativ du kan använda när du söker efter data och filtrera uttryck för att begränsa din sökning."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 402615a2-bed0-4831-ba69-53be49059718
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 264ea071dc0b15964af07c68cbf0dee896b07a3e
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2017
---
# <a name="log-analytics-search-reference"></a>Logganalys söka referens

>[!NOTE]
> Den här artikeln beskriver loggen sökningar med äldre frågespråket i logganalys.  Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), läser du bör [Språkreferens för det nya språket](https://go.microsoft.com/fwlink/?linkid=856079).

I följande referensavsnitt om sökningen language beskrivs allmänna syntax frågealternativen du kan använda när du söker efter data och filtrera uttryck för att begränsa din sökning. Här beskrivs också kommandon som du kan använda för att vidta åtgärder för data som hämtats.

Du kan läsa om de fält som returneras i sökningar och fasetterna som hjälper dig identifiera information om liknande datakategorier, i den [sökfält och aspekten refererar till avsnittet](#search-field-and-facet-reference).

## <a name="general-query-syntax"></a>Allmän frågesyntaxen
Syntaxen för allmänna frågor är följande:

```
filterExpression | command1 | command2 …
```

Filteruttrycket (`filterExpression`) definierar ”where” villkor för frågan. Kommandon som gäller för de resultat som returnerats av frågan. Flera kommandon måste avgränsas med vertikalstreck (|).

### <a name="general-syntax-examples"></a>Allmän syntaxexemplen
Exempel:

```
system
```

Den här frågan returnerar resultat som innehåller ordet *system* i alla fält som har indexerats för fulltext eller villkor sökning.

> [!NOTE]
> Inte alla fält som indexeras sätt, men de vanligaste textrepresentation fält (till exempel beskrivningar och namn) vanligtvis är.
>
>

```
system error
```

Den här frågan returnerar resultat som innehåller ord *system* och *fel*.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

Den här frågan returnerar resultat som innehåller ord *system* och *fel*. Sedan sorteras resultaten av den *ManagementGroupName* (i stigande ordning), och sedan efter den *TimeGenerated* (i fallande ordning). Det tar endast de första 10 resultat.

> [!IMPORTANT]
> Alla fältnamn och värden för fälten sträng och texten är skiftlägeskänslig.
>
>

## <a name="filter-expressions"></a>Filtrera uttryck
I följande underavsnitt beskrivs filteruttryck.

### <a name="string-literals"></a>Stränglitteraler
En stränglitteral är valfri sträng som inte kan identifieras av parsern som ett nyckelord eller en fördefinierad datatyp (till exempel ett tal eller datum).

Exempel:

```
These all are string literals
```

Den här frågan söker efter förekomster av alla fem ord. Om du vill söka komplexa sträng, omger du teckensträng inom citattecken. Exempel:

```
"Windows Server"
```

Detta returnerar endast resultat med exakt matchar för *Windows Server*.

### <a name="numbers"></a>Siffror
Parsern stöder heltal och flyttal nummer syntax för numeriska fält.

Exempel:

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="dates-and-times"></a>Datum och tid
Alla delar av informationen i systemet har en *TimeGenerated* -egenskap som representerar det ursprungliga datum och tid för posten. Vissa typer av data kan ha ytterligare datum- och tidsfälten (till exempel *LastModified*).

Tidslinjen **diagram tidsvärdet** Väljaren i Azure Log Analytics visar en fördelning av resultaten över tid (enligt den aktuella frågan körs). Detta baseras på den *TimeGenerated* fältet. Datum och tid har en specifik sträng-format som kan användas i frågor för att begränsa frågan till en viss tidsram. Du kan också använda syntax för att referera till relativa tidsintervall (till exempel ”mellan tre dagar sedan och 2 timmar sedan”).

Följande är giltiga typer av syntaxen för datum och tid:

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


Exempel:

```
TimeGenerated:2013-10-01T12:20
```

Föregående kommando returnerar bara poster med en *TimeGenerated* värdet exakt 12:20 på 1 oktober 2013.

Parsern stöder också mnemoteknisk datum/tid-värde, nu. (Det är troligt att detta kommer att ge något resultat, eftersom data inte gör det genom att snabb systemet.)

Exemplen är byggstenarna för relativa och absoluta datum. I följande tre avsnitt visas hur du använder dem i mer avancerade filter med exempel på relativa datumintervall.

### <a name="datetime-math"></a>Tidsvärdet math
Använd tidsvärdet matematiska operatorer offset eller avrunda datum/tid-värden med hjälp av enkla beräkningar för datum/tid.

Syntax:

```
datetime/unit
```

```
datetime[+|-]count unit
```

| Operatorn | Beskrivning |
| --- | --- |
| / |Avrundar datum/tid till den angivna enheten. Till exempel nu / dag Avrundar aktuellt datum och tid till midnatt på den aktuella dagen. |
| + eller - |Förskjuts tidsvärdet med det angivna antalet enheter. Till exempel nu + 1 timme förskjuts aktuellt datum och tid med en timme framåt. 2013-10-01T12:00-10 dagar förskjuts datumvärdet tillbaka med 10 dagar. |

Du kan länka tidsvärdet matematiska operatorer tillsammans. Exempel:

```
NOW+1HOUR-10MONTHS/MINUTE
```

I följande tabell visas de datum/tid-enheterna som stöds.

| Datum/tid-enhet | Beskrivning |
| --- | --- |
| ÅR, ÅR |Avrundar till innevarande eller förskjuts med det angivna antalet år. |
| MÅNADEN, MÅNADER |Avrundar till aktuell månad eller förskjuts med angivet antal månader. |
| DAGAR, DATUM |Avrundar till aktuell dag i månaden eller förskjuts med det angivna antalet dagar. |
| TIMME, TIMMAR |Avrundar till aktuell timme eller förskjuts med det angivna antalet timmar. |
| MINUT, MINUTER |Avrundar till aktuell minut eller förskjuts med det angivna antalet minuter. |
| ANDRA, I SEKUNDER |Avrundar till aktuella andra eller förskjuts med det angivna antalet sekunder. |
| MILLISEKUND MILLISEKUNDER MILLIEKVIVALENTER, MILLIS |Avrundar till aktuella millisekunder eller förskjuts med det angivna antalet millisekunder. |

### <a name="field-facets"></a>Fältet facets
Du kan ange sökvillkor för specifika fält och deras exakta värden genom att använda fältet facets. Detta skiljer sig från att skriva ”fritext” frågor för olika villkor i indexet. Du har redan sett den här tekniken i flera föregående exempel. Följande är mer komplexa exempel.

**Syntax**

```
field:value
```

```
field=value
```

**Beskrivning**

Söker i fältet för det specifika värdet. Värdet kan vara en stränglitteral, tal eller datum och tid.

Exempel:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**Syntax**

*fältet > värde*

*fältet < värde*

*fältet > = värde*

*fältet < = värde*

*fältet! = värde*

**Beskrivning**

Innehåller jämförelser.

Exempel:

```
TimeGenerated>NOW+2HOURS
```

**Syntax**

```
field:[from..to]
```

**Beskrivning**

Innehåller intervallet faceting.

Exempel:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="in"></a>I
Den **IN** nyckelord kan du välja från en lista med värden. Beroende på den syntax som du använder, kan det vara en enkel lista med värden som du anger, eller en lista med värden från en aggregering.

Syntax 1:

```
field IN {value1,value2,value3,...}
```

Den här syntaxen kan du inkludera alla värden i listan.



Exempel:

```
EventID IN {1201,1204,1210}
```

```
Computer IN {"srv01.contoso.com","srv02.contoso.com"}
```

Syntax 2:

```
(Outer query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

Den här syntaxen kan du skapa en samling. Du kan sedan feed i listan med värden från den sammansättning i en annan yttre (primära) sökning som söker efter händelser med dessa värde. Du kan göra detta genom att omsluta den inre sökningen med klammerparenteser och mata resultaten som möjliga värden för ett fält i den yttersta sökningen genom att använda operatorn i.

Inre frågan exempel: *datorer som för närvarande saknar säkerhetsuppdateringar* med följande aggregering fråga:

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

Den slutliga fråga som söker efter *alla Windows-händelser för datorer som för närvarande saknar säkerhetsuppdateringar* liknar följande:

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="contains"></a>Contains
Den **innehåller** nyckelord kan du filtrera efter poster med ett fält som innehåller en angiven sträng. Detta är skiftlägeskänslig, fungerar bara med strängfält och får inte innehålla några escape-tecken.

Syntax:

```
field:contains("string")
```

Exempel:

```
Type:contains("Event")
```

Detta returnerar poster med en typ som innehåller strängen ”Event”. Exempel inkluderar **händelse**, **SecurityEvent**, och **ServiceFabricOperationEvent**.



### <a name="regular-expressions"></a>Reguljära uttryck
Du kan ange ett sökvillkor för ett fält med ett reguljärt uttryck med hjälp av den **Regex** nyckelord. En fullständig beskrivning av den syntax som du kan använda i reguljära uttryck, se [med reguljära uttryck för att filtrera loggen söker i logganalys](log-analytics-log-searches-regex.md).

Syntax:

```
field:Regex("Regular Expression")
```

Exempel:

```
Computer:Regex("^C.*")
```

### <a name="logical-operators"></a>Logiska operatorer
Frågespråk stöd för logiska operatorer (*och*, *eller*, och *inte*) och deras alias C-format (*&&*,  *||* , och *!*respektive). Du kan använda parenteser för att gruppera de här operatorerna.

Exempel:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

Du kan hoppa över den logiska operatorn för översta filter-argument. I det här fallet antas operatorn och.

| Filteruttrycket | Motsvarighet till |
| --- | --- |
| systemfel |system och fel |
| System ”Windows Server” eller allvarlighetsgrad: 1 |system- och (”Windows Server” eller allvarlighetsgrad: 1) |

### <a name="wildcarding"></a>Jokertecken
Frågespråket stöder användning av den ( \* ) tecken för att representera ett eller flera tecken för ett värde i en fråga.

Exempel:

 Hitta alla datorer med ”SQL” i namnet, till exempel ”Redmond SQL”.

```
Type=Event Computer=*SQL*
```

> [!NOTE]
> Jokertecken kan inte användas i offerterna för tillfället. Till exempel meddelandet `"*This text*"` anser den (\*) används som en literal (\*) tecken.


## <a name="commands"></a>Kommandon


Kommandon som gäller för de resultat som returneras av frågan. Använd vertikalstreck (|) om du vill använda ett kommando hämtade resultaten. Flera kommandon måste avgränsas med pipe-tecken.

> [!NOTE]
> Kommandonamn kan skrivas i versaler eller gemener, till skillnad från fältnamn och data.
>
>

### <a name="sort"></a>Sortera
Syntax:

    sort field1 asc|desc, field2 asc|desc, …

Sorterar resultaten efter specifika fält. Asc/desc-suffix för att sortera resultaten i stigande eller fallande ordning är valfritt. Om det utelämnas den *asc* sorteringsordning antas. För den **TimeGenerated** fältet *desc* sorteringsordning antas så att den returnerar först senaste resultat som standard.

### <a name="toplimit"></a>Upp/gränsen
Syntax:

    top number


    limit number
Begränsar svar på de främsta resultat.

Exempel:

    Type:Alert errors detected | top 10

Returnerar de översta 10 matchande resultat.

### <a name="skip"></a>Hoppa över
Syntax:

    skip number

Hoppar över antalet resultat som visas.

Exempel:

    Type:Alert errors detected | top 10 | skip 200

Returnerar de översta 10 matchande resultat från resultatet 200.

### <a name="select"></a>Välj
Syntax:

    select field1, field2, ...

Begränsar resultat till de fält som du väljer.

Exempel:

    Type:Alert errors detected | select Name, Severity

Begränsar fälten returnerade resultat att *namn* och *allvarlighetsgrad*.

### <a name="measure"></a>Mått
Den *mått* kommando för att tillämpa statistikfunktioner rådata sökresultatet. Detta är användbart för att hämta *Gruppera efter* vyer över data. När du använder kommandot mått innehåller logganalys sökresultatet en tabell med sammanlagda resultat.

**Syntax:**

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



Sammanställer resultatet av *grupperingsfält*, och beräknar aggregerade måttvärden med hjälp av *aggregatedField*.

| Måttet statistisk funktion | Beskrivning |
| --- | --- |
| *aggregateFunction* |Namnet på funktionen aggregate (skiftlägesokänslig). Mängdfunktionerna stöds: antal, MAX, MIN, SUM, AVG, STDDEV, COUNTDISTINCT, percentil ## eller PCT ## (## är ett tal mellan 1 och 99). |
| *aggregatedField* |Det fält som är att aggregeras. Det här fältet är valfritt för antal mängdfunktionen men måste vara ett befintligt numeriska fält för SUMMAN, MAX, MIN, AVG, STDDEV, percentil ## eller PCT ## (## är ett tal mellan 1 och 99). AggregatedField kan också vara någon av de **utöka** funktioner som stöds. |
| *fieldAlias* |(Valfritt) alias för det beräknade aggregerade värdet. Om inget anges fältnamnet är **AggregatedValue**. |
| *Grupperingsfält* |Namnet på fältet resultatuppsättningen grupperas efter. |
| *Intervall* |Tidsintervall i formatet:**nnnNAME**. **nnn**är positivt heltal. **NAMNET** heter intervall. Stöds intervall namn är skiftlägeskänsliga och innehålla: MILLISEKUNDER [S] sekund [S] minut [S] timme [S] dag [S] [S] för MÅNADEN och ÅRET [S]. |

Alternativet intervallet kan endast användas i fält för datum/tid-grupp (exempelvis *TimeGenerated* och *TimeCreated*). För närvarande är detta verkställs inte av tjänsten, men ett fält utan datum/tid som skickas till serverdelen orsakar ett fel under körning. När schemavalideringen implementeras avvisar API för tjänsten som använder fält utan datum/tid för intervall aggregering. Aktuellt *mått* implementeringen stöder intervall gruppering för alla mängdfunktioner.

Om BY-satsen utelämnas, men ett intervall har angetts (som en andra syntax), den *TimeGenerated* fältet antas som standard.

Exempel:

**Exempel 1**

    Type:Alert | measure count() as Count by ObjectId

Grupperar aviseringar efter *ObjectID*, och beräknar antalet aviseringar för varje grupp. Sammanställda värdet som returneras av *antal* fält (alias).

**Exempel 2**

    Type:Alert | measure count() interval 1HOUR

Grupperar aviseringar efter 1 timme intervall genom att använda den *TimeGenerated* fältet och returnerar antalet aviseringar i varje intervall.

**Exempel 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

Samma som i föregående exempel, men med ett sammansatt fältalias (*AlertsPerHour*).

**Exempel 4**

    * | måttet count() av TimeCreated intervall 5DAYS

Grupperar resultaten efter 5 dagars intervall genom att använda den *TimeCreated* fältet och returnerar antalet resultat i varje intervall.

**Exempel 5**

    Type:Alert | measure max(Severity) by WorkflowName

Grupperar aviseringar efter arbetsbelastning namn och returnerar värdet för maximal allvarlighetsgrad för varje arbetsflöde.

**Exempel 6**

    Type:Alert | measure min(Severity) by WorkflowName

Samma som i föregående exempel, men den *min* samman funktion.

**Exempel 7**

    Type:Perf | measure avg(CounterValue) by Computer

Grupper Perf per dator, och beräknar medelvärdet (medel).

**Exempel 8**

    Type:Perf | measure sum(CounterValue) by Computer

Samma som det tidigare exemplet, men använder *summan*.

**Exempel 9**

    Type:Perf | measure stddev(CounterValue) by Computer

Samma som det tidigare exemplet, men använder *stddev*.

**Exempel 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

Samma som det tidigare exemplet, men använder *percentile70*.

**Exempel 11**

    Type:Perf | measure pct70(CounterValue) by Computer

Samma som det tidigare exemplet, men använder *pct70*. Observera att *PCT ##* är endast ett alias för *percentil ##* funktion.

**Exempel 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

Grupper Perf först per dator och sedan CounterName och beräknar medelvärdet (medel).

**Exempel 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

Hämtar de översta fem arbetsflödena med det maximala antalet aviseringar.

**Exempel 14**

    * | måttet countdistinct(Computer) efter typ

Räknar antalet unika datorer som rapporterar för varje typ av.

**Exempel 15**

    * | måttet countdistinct(Computer) intervall 1 timme

Räknar antalet unika datorer som rapporterar varje timme.

**Exempel 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

Grupper processortid i procent av datorn, och returnerar medelvärdet för varje timme.

**Exempel 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

Grupper W3CIISLog av metoden, och returnerar högsta för var femte minut.

**Exempel 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

Grupper % processortid per dator, och returnerar den minsta, medel, 75: e percentilen och högsta för varje timme.

**Exempel 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

Grupper % processortid först efter dator och sedan efter instansnamnet, och returnerar den minsta, medel, 75: e percentilen och högsta för varje timme.

**Exempel 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

Beräknar maximalt för Diskskrivningar per minut för alla diskar på datorn.

### <a name="where"></a>där
Syntax:

```
**where** AggregatedValue>20
```

Kan endast användas när en *mått* kommando för att filtrera de aggregerade ytterligare resultat som den *mått* aggregeringsfunktionen har skapats.

Exempel:

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)



### <a name="dedup"></a>Dedupliceringen
Syntax:

    Dedup FieldName

Returnerar det första dokumentet som hittades för varje unikt värde i det angivna fältet.

Exempel:

    Type=Event | Dedup EventID | sort TimeGenerated DESC

Det här exemplet returnerar en händelse (senaste händelse) per EventID.

### <a name="join"></a>Slå ihop
Kopplar ihop resultatet av två frågor för att bilda en enda resultatmängd.  Har stöd för flera kopplingstyper som beskrivs i tabellen.

| Kopplingstyp | Beskrivning |
|:--|:--|
| inre | Returnera bara poster med ett matchande värde i båda frågorna. |
| yttre | Returnera alla poster från båda frågorna.  |
| vänster  | Returnera alla poster från vänster frågan och matchande poster från rätt fråga. |


- Kopplingar för närvarande inte stöder frågor som innehåller den **IN** nyckelord, den **mått** kommando eller **utöka** kommandot om det riktar sig till ett fält från den högra frågan.
- Du kan för närvarande innehålla bara ett enda fält i en koppling.
- En sökning får inte innehålla mer än en koppling.

**Syntax**

```
<left-query> | JOIN <join-type> <left-query-field-name> (<right-query>) <right-query-field-name>
```

**Exempel**

Överväg att ansluta till en datatyp som samlas in från en anpassad logg med namnet MyBackup_CL och pulsslag för varje dator för att illustrera olika kopplingstyper.  Dessa datatyper har följande data.

`Type = MyBackup_CL`

| TimeGenerated | Dator | LastBackupStatus |
|:---|:---|:---|
| 4/20/2017 01:26:32.137 AM | Srv01.contoso.com | Lyckades |
| 4/20/2017 02:13:12.381 AM | SRV02.contoso.com | Lyckades |
| 4/20/2017 02:13:12.381 AM | srv03.contoso.com | Fel |

`Type = Hearbeat`(Endast en delmängd av fält som visas)

| TimeGenerated | Dator | ComputerIP |
|:---|:---|:---|
| 2017-4/21 12:01:34.482 PM | Srv01.contoso.com | 10.10.100.1 |
| 2017-4/21 12:02:21.916 PM | SRV02.contoso.com | 10.10.100.2 |
| 2017-4/21 12:01:47.373 PM | srv04.contoso.com | 10.10.100.4 |

#### <a name="inner-join"></a>inre koppling

`Type=MyBackup_CL | join inner Computer (Type=Heartbeat) Computer`

Returnerar följande poster där fältet dator matchar för båda datatyper.

| Dator| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| Srv01.contoso.com | 4/20/2017 01:26:32.137 AM | Lyckades | 2017-4/21 12:01:34.482 PM | 10.10.100.1 | Pulsslag |
| SRV02.contoso.com | 4/20/2017 02:13:12.381 AM | Lyckades | 2017-4/21 12:02:21.916 PM | 10.10.100.2 | Pulsslag |


#### <a name="outer-join"></a>yttre koppling

`Type=MyBackup_CL | join outer Computer (Type=Heartbeat) Computer`

Returnerar följande poster för båda datatyper.

| Dator| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| Srv01.contoso.com | 4/20/2017 01:26:32.137 AM | Lyckades  | 2017-4/21 12:01:34.482 PM | 10.10.100.1 | Pulsslag |
| SRV02.contoso.com | 4/20/2017 02:14:12.381 AM | Lyckades  | 2017-4/21 12:02:21.916 PM | 10.10.100.2 | Pulsslag |
| srv03.contoso.com | 4/20/2017 01:33:35.974 AM | Fel  | 2017-4/21 12:01:47.373 PM | | |
| srv04.contoso.com |                           |          | 2017-4/21 12:01:47.373 PM | 10.10.100.2 | Pulsslag |



#### <a name="left-join"></a>koppling till vänster

`Type=MyBackup_CL | join left Computer (Type=Heartbeat) Computer`

Returnerar följande poster från MyBackup_CL med alla matchande fält från pulsslag.

| Dator| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| Srv01.contoso.com | 4/20/2017 01:26:32.137 AM | Lyckades | 2017-4/21 12:01:34.482 PM | 10.10.100.1 | Pulsslag |
| SRV02.contoso.com | 4/20/2017 02:13:12.381 AM | Lyckades | 2017-4/21 12:02:21.916 PM | 10.10.100.2 | Pulsslag |
| srv03.contoso.com | 4/20/2017 02:13:12.381 AM | Fel | | | |


### <a name="extend"></a>Utöka
Låter dig skapa körning fält i frågor. Observera att Runtime-fält inte kan användas med kommandot mått för aggregering.

**Exempel 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
Visar viktade rekommendation poäng för SQL-bedömning rekommendationer.

**Exempel 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
Visar räknarvärdet i KBs i stället för byte.

**Exempel 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
Skalar värdet för WireData TotalBytes så att alla resultat mellan 0 och 100.

**Exempel 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
Taggar Perf värde som är mindre än 50 procent som låg, och andra som hög.

**Exempel 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
Beräknar maximalt för Diskskrivningar per minut för alla diskar på datorn.

**Funktioner som stöds**

| Funktionen | Beskrivning | Syntaxexemplen |
| --- | --- | --- |
| ABS |Returnerar det absoluta värdet för det angivna värdet eller funktion. |`abs(x)` <br> `abs(-5)` |
| ARCCOS |Returnerar arcus cosinus av ett värde eller en funktion. |`acos(x)` |
| och |Returnerar värdet true om alla operanderna utvärderas till SANT. |`and(not(exists(popularity)),exists(price))` |
| ARCSIN |Returnerar sinus för båge för ett värde eller en funktion. |`asin(x)` |
| ARCTAN |Returnerar arctangens för ett värde eller en funktion. |`atan(x)` |
| ARCTAN2 |Returnerar den vinkel som uppstår till följd av konvertering av rectangular koordinaterna x, y till polärt koordinater. |`atan2(x,y)` |
| cbrt |Kuben rot. |`cbrt(x)` |
| ceil |Avrundar uppåt till ett heltal. |`ceil(x)`  <br> `ceil(5.6)`Returnerar 6 |
| COS |Returnerar cosinus för en vinkel. |`cos(x)` |
| COSH |Returnerar hyperboliskt cosinus för en vinkel. |`cosh(x)` |
| def |Förkortning för standard. Returnerar värdet för fältet ”Filter”. Om fältet inte finns, returnerar det angivna standardvärdet och ger det första värdet där: `exists()==true`. |`def(rating,5)`. Den här funktionen def() returnerar klassificeringen eller om ingen klassificering har angetts i dokumentet returnerar 5. <br> `def(myfield, 1.0)`motsvarar `if(exists(myfield),myfield,1.0)`. |
| gr |Konverterar radianer till grader. |`deg(x)` |
| div |`div(x,y)`dividerar x av y. |`div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| Dist |Returnerar avståndet mellan två angreppsmetoderna, (poäng) i ett n-dimensionell utrymme. Hämtar i kraften plus två eller fler instanser av ValueSource och beräknar avståndet mellan t.ex. Varje ValueSource måste vara ett tal. Det måste vara ett jämnt antal ValueSource instanser skickades och metoden förutsätter att den första delen representerar den första vektorn och den andra hälften representerar den andra metoden. |`dist(2, x, y, 0, 0)`Beräknar Euclidean avståndet mellan (0,0) och (x, y) för varje dokument. <br> `dist(1, x, y, 0, 0)`Beräknar Manhattan (Tag taxi) avståndet mellan (0,0) och (x, y) för varje dokument. <br> `dist(2,,x,y,z,0,0,0)`Euclidean avståndet mellan (0,0,0) och (x, y, z) för varje dokument.<br>`dist(1,x,y,z,e,f,g)`Manhattan avståndet mellan (x, y, z) och (e, f, g), där varje är ett fältnamn. |
| Det finns |Returnerar TRUE om alla medlemmar i fältet finns. |`exists(author)`Returnerar TRUE för alla dokument som har ett värde i fältet ”Författare”.<br>`exists(query(price:5.00))`Returnerar SANT om ”pris” matchar ”5,00”. |
| EXP |Returnerar Eulerss tal upphöjt till x. |`exp(x)` |
| våning |Avrundar nedåt till ett heltal. |`floor(x)`  <br> `floor(5.6)`Returnerar 5 |
| hypo |Returnerar sqrt(sum(pow(x,2),pow(y,2))) utan mellanliggande Dataspill eller -underskott. |`hypo(x,y)`  <br> ` |
| Om |Möjliggör villkorlig funktionen frågor. I `if(test,value1,value2)`, test är eller refererar till ett logiskt värde eller uttryck som returnerar ett logiskt värde (SANT eller FALSKT). `value1`värdet returneras av funktionen om test ger SANT. `value2`värdet returneras av funktionen om test ger FALSE. Ett uttryck kan vara en funktion som tillämpar booleska värden. Det kan också vara en funktion som returnerar numeriska värden, i vilket fall värdet 0 tolkas som false, eller returnera strängar, i vilket fall tom sträng tolkas som false. |`if(termfreq(cat,'electronics'),popularity,42)`Den här funktionen kontrollerar varje dokument och se om den innehåller termen ”electronics” i fältet cat. Om den finns, returneras värdet för fältet popularitet. I annat fall returneras värdet för 42. |
| linjär |Implementerar `m*x+c`där m och c är konstanter och x är en valfri funktion. Detta motsvarar `sum(product(m,x),c)`, men något mer effektiv som det implementeras som en funktion. |`linear(x,m,c) linear(x,2,4)`Returnerar`2*x+4` |
| LN |Returnerar en naturlig logg för den angivna funktionen. |`ln(x)` |
| Logg |Returnerar loggen bas 10 för den angivna funktionen. |`log(x)   log(sum(x,100))` |
| karta |Mappar alla värden i en inkommande funktion x som faller inom min och max, att det angivna målet. Argument min och max måste vara konstanter. Argument mål- och standard kan vara konstanter eller funktioner. Om värdet för x inte faller mellan min och max och värdet för x returneras eller ett standardvärde returneras om anges som en 5 argument. |`map(x,min,max,target) map(x,0,0,1)`Ändras alla värden 0 till 1. Detta kan vara användbart för att hantera standardvärden 0.<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)`Ändrar värden mellan 0 och 100 till 1 och alla andra värden till -1.<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))`Ändringar av alla värden mellan 0 och 100 x + 599 och alla andra värden för frekvens solr om du i fälttexten har löpt ut. |
| Max |Returnerar det största numeriska värdet flera kapslade funktioner eller konstanter som angetts som argument: `max(x,y,...)`. Funktionen max kan också användas för ”bottoming” en annan funktion eller fältet på några angetts konstant.  Använd den `field(myfield,max)` syntax för att välja det maximala värdet för ett enda flervärdesfält. |`max(myfield,myotherfield,0)` |
| min. |Returnerar det minsta numeriska värdet med flera kapslade funktioner av konstanter som angetts som argument: `min(x,y,...)`. Min-funktionen kan också användas för att tillhandahålla en ”övre gränsen” på en funktion med en konstant. Använd den `field(myfield,min)` syntax för att välja det lägsta värdet för ett enda flervärdesfält. |`min(myfield,myotherfield,0)` |
| MOD |Beräknar modulus av funktionen x av funktionen y. |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))` |
| MS |Returnerar skillnaden mellan argumenten millisekunder. Datum är i förhållande till Unix- eller POSIX tid epok, midnatt den 1 januari 1970 UTC. Argument kan vara namnet på en indexerad TrieDateField eller datum matematiska baserat på ett konstant datum eller nu. `ms()`motsvarar `ms(NOW)`, antal millisekunder sedan epok. `ms(a)`Returnerar antalet millisekunder sedan den epok som argumentet representerar. `ms(a,b)`Returnerar antalet millisekunder att b inträffar före en, vilket är `a - b`. |`ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| inte |Funktionen omsluten Negerade logiskt värde. |`not(exists(author))`GÄLLER endast när `exists(author)` är false. |
| eller |En logisk disjunktion. |`or(value1,value2)`TRUE om endera value1 eller value2 är true. |
| Pow |Genererar den angivna basen till angiven potens. `pow(x,y)`aktiverar x upphöjt till y. |`pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)`Samma som rot. |
| Produkten |Returnerar produkten av flera värden eller funktioner som anges i en kommaavgränsad lista. `mul(...)`kan också användas som ett alias för den här funktionen. |`product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip |Utför en ömsesidigt funktion med `recip(x,m,a,b)` implementera `a/(m*x+b)`, där m, a, b är konstanter och x är en godtyckligt komplexa funktion. När en och b är lika med- och x > = 0, den här funktionen har ett maximalt värde 1 släpper som x ökar. Öka värdet för en och b tillsammans ger en transport för hela funktionen till en plattare del av kurvan. De här egenskaperna kan göra detta till en perfekt funktion för att öka nyare dokument när x är `rord(datefield)`. |`recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| rad |Konverterar grader till radianer. |`rad(x)` |
| Skriv ut |Avrundar till närmaste heltal. |`rint(x)`  <br> `rint(5.6)`Returnerar 6 |
| sin |Returnerar sinus för en vinkel. |`sin(x)` |
| SINH |Returnerar hyperboliskt sinus för en vinkel. |`sinh(x)` |
| Skala |Skalar värden för funktionen x, utesluten mellan angivna minTarget och maxTarget inklusiva. Den aktuella implementeringen passerar alla funktionen värden att hämta min och max, så den kan välja rätt skalan. Den aktuella implementeringen kan inte skilja när dokument har tagits bort, eller dokument som har inget värde. Den använder 0.0 värden för dessa fall. Det innebär att om värdena är normalt alla större än 0,0, en kan fortfarande få 0,0 som det lägsta värdet för att mappa från. I dessa fall måste en lämplig `map()` funktionen kan användas som en lösning ändra 0,0 till ett värde inom intervallet verkliga som visas här:`scale(map(x,0,0,5),1,2)` |`scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)`Skalar värdena på x, så att alla värden är mellan 1 och 2 inklusiva. |
| rot |Returnerar kvadratroten av det angivna värdet eller funktion. |`sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist |Beräknar avståndet mellan två strängar. Använder Lucene stavningskontroll checker StringDistance gränssnitt och stöder alla implementeringar tillgängligt i paketet. Du kan också program att använda sina egna via Solrs resurs inläsning funktioner. strdist tar `(string1, string2, distance measure)`. Möjliga värden för avståndet måttet är:<ul><li>jw: Jaro Winkler</li><li>Redigera: Levenstein eller redigera avstånd</li><li>ngram: det NGramDistance om anges, eventuellt överföra ngram storlek för. Standardvärdet är 2.</li><li>FQN: Fullt kvalificerad klassnamn för en implementering av gränssnittet StringDistance. Måste ha en Nej %d{arg/ konstruktor.</li></ul> |`strdist("SOLR",id,edit)` |
| Sub |Returnerar x-y från `sub(x,y)`. |`sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| Summa |Returnerar summan av flera värden eller funktioner som anges i en kommaavgränsad lista. `add(...)`kan användas som ett alias för den här funktionen. |`sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| termfreq |Returnerar antalet gånger ordet visas i fältet för dokumentet. |termfreq(text,'memory') |
| TAN |Returnerar tangens för en vinkel. |`tan(x)` |
| TANH |Returnerar hyperbolisk tangens för en vinkel. |`tanh(x)` |

## <a name="search-field-and-facet-reference"></a>Sök fältet och aspekten referens
När du använder loggen Sök efter data visas resultatet olika fält och facets. En del information visas inte mycket beskrivande. Använd följande information som hjälper dig att förstå resultatet.

| Fält | Söktyp | Beskrivning |
| --- | --- | --- |
| Klient-ID |Alla |Används för att partitionera data. |
| TimeGenerated |Alla |Används för att driva en tidslinje, timeselectors (i sökningen och i andra skärmar). När informationen har genererats (vanligtvis på agenten) representerar. Tid uttryckt i ISO-format och är alltid UTC. För typer som är baserade på befintliga instrumentation (det vill säga händelser i en logg), är det vanligtvis realtidsplatsrapportering post/rad/loggposten loggades. För några av de typer som skapas via hanteringspaket eller i molnet (exempelvis rekommendationer eller varningar), representerar tiden något annat. Detta är tiden när den här ny typ av data med en ögonblicksbild av en konfiguration av något slag samlades in, eller en rekommendation/avisering har producerats baserat på den. |
| Händelse-ID |Händelse |Händelse-ID i Windows-händelseloggen. |
| Händelseloggen |Händelse |Händelselogg när händelsen loggades i Windows. |
| EventLevelName |Händelse |Kritisk/varning/information/lyckades |
| EventLevel |Händelse |Numeriskt värde för kritiska/varning/information/lyckad (Använd EventLevelName i stället för enklare/mer lättläst frågor). |
| SourceSystem |Alla |Där data hämtas från (i bifoga läge till tjänsten). Exempel: Microsoft System Center Operations Manager och Azure Storage. |
| Objektnamn |PerfHourly |Objektnamn för Windows-prestanda. |
| Instansnamn |PerfHourly |Windows-instansnamn på prestandaräknare. |
| CounteName |PerfHourly |Namn på Windows prestandaräknare. |
| ObjectDisplayName |PerfHourly ConfigurationAlert ConfigurationObject, ConfigurationObjectProperty |Visar namnet på det objekt som mål för en regel för prestandainsamling i Operations Manager. Också kan vara visningsnamnet för det objekt som upptäckts av åtgärdsinformation eller mot som aviseringen genererades. |
| RootObjectName |PerfHourly ConfigurationAlert ConfigurationObject, ConfigurationObjectProperty |Visningsnamn för överordnat för överordnat (i en dubbel värdrelation) objekt som mål för en regel för prestandainsamling i Operations Manager. Också kan vara visningsnamnet för det objekt som upptäckts av åtgärdsinformation eller mot som aviseringen genererades. |
| Dator |De flesta typer |Datornamnet som data som hör till. |
| Enhetsnamn |ProtectionStatus |Datornamn data tillhör (samma som ”dator”). |
| DetectionId |ProtectionStatus | |
| ThreatStatusRank |ProtectionStatus |Hot status rang är en numerisk representation av status för hot. Liknar HTTP svarskoder, rangordning har glapp mellan tal (vilket är anledningen till att inga hot är 150 och inte 100 eller 0), med utrymme för att lägga till nya tillstånd. För en sammanslagning av status för hot och skyddsstatus är avsikten att visa det sämsta tillståndet som datorn har varit i under den valda tidsperioden. Talen rangordnas olika lägen, så du kan söka efter posten med högst nummer. |
| ThreatStatus |ProtectionStatus |Beskrivning av ThreatStatus, mappar 1 till 1 med ThreatStatusRank. |
| TypeofProtection |ProtectionStatus |Produkten för program mot skadlig kod som har identifierats på datorn: none, verktyget Borttagning av skadlig kod för Microsoft Forefront och så vidare. |
| ScanDate |ProtectionStatus | |
| SourceHealthServiceId |ProtectionStatus RequiredUpdate |HealthService-ID för den här datorns agent. |
| HealthServiceId |De flesta typer |HealthService-ID för den här datorns agent. |
| ManagementGroupName |De flesta typer |Namn på Hanteringsgrupp för Operations Manager-anslutna agenter. Annars är null/tomt. |
| Objekttyp |ConfigurationObject |Ange (som Operations Manager management pack-typ /-klass) för det här objektet har identifierats av logganalys configuration assessment. |
| UpdateTitle |RequiredUpdate |Namnet på uppdateringen som hittades inte. |
| PublishDate |RequiredUpdate |När uppdateringen har publicerats på Microsoft Update. |
| Server |RequiredUpdate |Datornamn data tillhör (samma som ”dator”). |
| Produkt |RequiredUpdate |Produkten som uppdateringen gäller. |
| UpdateClassification |RequiredUpdate |Typ av uppdatering (till exempel Samlad uppdatering eller service pack). |
| KBID |RequiredUpdate |KB artikel-ID som beskriver det här bästa praxis eller update. |
| WorkflowName |ConfigurationAlert |Namnet på regeln eller övervakaren som producerade av avisering. |
| Allvarsgrad |ConfigurationAlert |Allvarlighetsgrad för aviseringen. |
| Prioritet |ConfigurationAlert |Prioritet för aviseringen. |
| IsMonitorAlert |ConfigurationAlert |Den här aviseringen som genererats av Övervakare (SANT) eller en regel (FALSKT)? |
| AlertParameters |ConfigurationAlert |XML med parametrar för logganalys-avisering. |
| Kontext |ConfigurationAlert |XML med kontext för logganalys-avisering. |
| Arbetsbelastning |ConfigurationAlert |Teknik eller arbetsbelastning som aviseringen refererar till. |
| AdvisorWorkload |Rekommendation |Teknik eller arbetsbelastning som rekommendationen refererar till. |
| Beskrivning |ConfigurationAlert |Aviseringsbeskrivningen (kort). |
| DaysSinceLastUpdate |UpdateAgent |Hur många dagar sedan (i förhållande till TimeGenerated för den här posten) denna agent installerades uppdateringar från Windows Server Update Service (WSUS) eller Microsoft Update? |
| DaysSinceLastUpdateBucket |UpdateAgent |Baserat på DaysSinceLastUpdate, en kategorisering i tid buckets av hur länge sedan en dator senast installerade uppdateringar från WSUS-/ Microsoft Update. |
| AutomaticUpdateEnabled |UpdateAgent |Kontroll av automatisk uppdatering aktiveras eller inaktiveras på den här agenten? |
| AutomaticUpdateValue |UpdateAgent |Automatisk uppdatering kontrollerar inställd på automatiskt hämta och installera, endast hämta eller bara kontrollera? |
| WindowsUpdateAgentVersion |UpdateAgent |Versionsnummer för Microsoft Update-agenten. |
| WSUSServer |UpdateAgent |Vilken WSUS-server är den här update-agenten på? |
| OSVersion |UpdateAgent |Version av operativsystemet update-agenten körs på. |
| Namn |Rekommendation ConfigurationObjectProperty |/ Titel på rekommendation eller namnet på egenskapen från logganalys configuration assessment. |
| Värde |ConfigurationObjectProperty |Värdet för en egenskap från logganalys configuration assessment. |
| KBLink |Rekommendation |URL-adress i KB-artikel som beskriver det här bästa praxis eller update. |
| RecommendationStatus |Rekommendation |Rekommendationerna är bland annat följande några vars poster som ska uppdateras inte just lade till sökindexet. Denna status ändras om rekommendationen är aktiv/öppna, eller om logganalys upptäcker att den har lösts. |
| RenderedDescription |Händelse |Renderade beskrivning (återanvända text ifyllda parametrar) av en Windows-händelse. |
| ParameterXml |Händelse |XML med parametrarna i dataavsnittet i en Windows-händelse (som visas i Loggboken). |
| EventData |Händelse |XML med avsnittet hela data i en Windows-händelse (som visas i Loggboken). |
| Källa |Händelse |Källan för händelseloggning som genererade händelsen. |
| EventCategory |Händelse |Kategori för händelsen direkt från Windows-händelseloggen. |
| Användarnamn |Händelse |Användarnamnet på Windows-händelse (normalt NT AUTHORITY\LOCALSYSTEM). |
| SampleValue |PerfHourly |Genomsnittligt värde för en prestandaräknare timvis sammanställning. |
| Min |PerfHourly |Minsta värdet i timintervall av en prestandaräknaren timvis aggregering. |
| Max. |PerfHourly |Maximalt värde i timintervall av en prestandaräknaren timvis aggregering. |
| Percentile95 |PerfHourly |95 percentilvärdet för timintervall av en prestandaräknaren timvis aggregering. |
| SampleCount |PerfHourly |Hur många rådataprestanda räknaren prover som användes för att skapa varje timme sammanställd posten. |
| Hot |ProtectionStatus |Namn på skadlig kod. |
| StorageAccount |W3CIISLog |Azure Storage-konto loggen lästes från. |
| AzureDeploymentID |W3CIISLog |Azure distributions-ID för Molntjänsten loggen tillhör. |
| Roll |W3CIISLog |Rollen för Azure-molntjänst loggen tillhör. |
| RoleInstance |W3CIISLog |RoleInstance för rollen Azure som loggen tillhör. |
| sSiteName |W3CIISLog |IIS-webbplatsen som loggen tillhör (metabasen notation); s-sitename fält i den ursprungliga loggen. |
| sComputerName |W3CIISLog |S-computername fält i den ursprungliga loggen. |
| sIP |W3CIISLog |Servern IP-adress HTTP-begäran var riktar sig till. S-ip-fält i den ursprungliga loggen. |
| csMethod |W3CIISLog |HTTP-metoden (till exempel GET/POST) används av klienten i HTTP-begäran. Cs-metod i den ursprungliga loggen. |
| cIP |W3CIISLog |Klientens IP-adress HTTP-begäran kommer från. C-ip-fält i den ursprungliga loggen. |
| csUserAgent |W3CIISLog |HTTP-användaragent deklareras av klienten (webbläsaren eller på annat sätt). Cs-användaragenten i den ursprungliga loggen. |
| scStatus |W3CIISLog |HTTP-statuskoden (till exempel 200/403/500) returnerades av servern till klienten. Cs-status i den ursprungliga loggen. |
| timeTaken |W3CIISLog |Hur lång tid (i millisekunder) som begäran tog för att slutföra. Fältet timetaken i den ursprungliga loggen. |
| csUriStem |W3CIISLog |Relativ URI (utan värdadress, som är, / söka) som begärdes. Cs-uristem fält i den ursprungliga loggen. |
| csUriQuery |W3CIISLog |URI-fråga. URI-frågor krävs endast för dynamiska sidor, till exempel ASP-sidor, så att det här fältet innehåller vanligtvis ett bindestreck för statiska sidor. |
| sPort |W3CIISLog |Serverporten som HTTP-begäran skickades till (och som IIS lyssnar på, eftersom den skaffat). |
| csUserName |W3CIISLog |Autentisera användarnamnet, om begäran är autentiserad och inte anonym. |
| csVersion |W3CIISLog |HTTP-protokollversion som används i begäran (till exempel HTTP/1.1). |
| csCookie |W3CIISLog |Cookie-information. |
| csReferer |W3CIISLog |Plats som användaren senast besökte. Denna plats innehöll en länk till den aktuella platsen. |
| csHost |W3CIISLog |Värdadressen (till exempel www.mysite.com) som begärdes. |
| scSubStatus |W3CIISLog |Understatus. |
| scWin32Status |W3CIISLog |Windows-statuskod. |
| csBytes |W3CIISLog |Antal byte som skickades i begäran från klienten till servern. |
| scBytes |W3CIISLog |Byte som returneras i svaret från servern till klienten. |
| ConfigChangeType |ConfigurationChange |Typ av ändring (till exempel WindowsServices/program). |
| ChangeCategory |ConfigurationChange |Kategori av ändring (Modified/lagts till/tagits bort). |
| SoftwareType |ConfigurationChange |Typ av programvara (uppdateringsprogrammet). |
| SoftwareName |ConfigurationChange |Namnet på programmet (gäller endast för programvaruändringar). |
| Utgivare |ConfigurationChange |Leverantören som publicerar program (gäller endast för programvaruändringar). |
| SvcChangeType |ConfigurationChange |Typ av ändring som tillämpades på en Windows-tjänst (tillstånd/StartupType/sökväg/tjänstkonto). Detta gäller endast Windows-tjänsten ändras. |
| SvcDisplayName |ConfigurationChange |Visningsnamn för den tjänst som har ändrats. |
| SvcName |ConfigurationChange |Namnet på den tjänst som har ändrats. |
| SvcState |ConfigurationChange |Nya (aktuella) tillstånd för tjänsten. |
| SvcPreviousState |ConfigurationChange |Föregående kända statusen för tjänsten (gäller endast om tjänstens tillstånd ändras). |
| SvcStartupType |ConfigurationChange |Starttyp för tjänsten. |
| SvcPreviousStartupType |ConfigurationChange |Tidigare tjänststarttyp (gäller endast om tjänstens starttyp ändras). |
| SvcAccount |ConfigurationChange |Tjänstkontot. |
| SvcPreviousAccount |ConfigurationChange |Tidigare tjänstkontot (gäller endast om tjänstkontot har ändrats). |
| SvcPath |ConfigurationChange |Sökvägen till den körbara filen för Windows-tjänst. |
| SvcPreviousPath |ConfigurationChange |Föregående sökvägen för den körbara filen för Windows-tjänsten (gäller endast om det ändras). |
| SvcDescription |ConfigurationChange |Beskrivning av tjänsten. |
| Föregående |ConfigurationChange |Föregående status för den här programvaran (installerad ej installerad/föregående versionen). |
| Aktuella |ConfigurationChange |Senaste status för den här programvaran (installerad ej installerad/current version). |

## <a name="next-steps"></a>Nästa steg
Mer information om loggen sökningar:

* Bekanta dig med [loggsökningar](log-analytics-log-searches.md) för att visa detaljerad information som samlas in av lösningar.
* Använd [anpassade fält i logganalys](log-analytics-custom-fields.md) att utöka loggen sökningar.
