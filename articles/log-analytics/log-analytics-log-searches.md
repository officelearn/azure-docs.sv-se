---
title: "Söka efter data med loggen sökningar i Azure Log Analytics | Microsoft Docs"
description: "Loggsökningar låter dig kombinera och korrelera datordata från flera källor i din miljö."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 0d7b6712-1722-423b-a60f-05389cde3625
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: aa4608d37b06db88819e6175dcf8f94a7e13f04a
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="find-data-using-log-searches-in-log-analytics"></a>Hitta data med hjälp av loggen sökningar i logganalys

>[!NOTE]
> Den här artikeln beskriver loggen sökningar med äldre frågespråket i logganalys.  Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), läser du bör [förstå loggen söker i logganalys (nya)](log-analytics-log-search-new.md).


Kärnan i logganalys är loggen sökfunktionen där du kan kombinera och korrelera datorn data från flera källor i din miljö. Lösningar är också drivs av logg-sökning för att sätta mått pivoteras runt ett specifikt problem.

Du kan skapa en fråga på sidan Sök efter och sedan när du söker kan du filtrera resultaten genom att använda aspekten kontroller. Du kan också skapa avancerade frågor för att transformera, filter och rapporten på dina resultat.

Vanliga loggen sökfrågor visas på de flesta sidor för lösningen. I hela OMS-portalen du klicka på paneler eller detaljer till andra objekt att visa detaljer om objektet genom att söka i loggen.

I den här självstudiekursen kommer går vi igenom exemplen innehåller alla grundläggande information när du använder loggen sökning.

Vi börjar med enkla, praktiska exempel och bygga på dem så att du kan få en förståelse av praktiska användningsområden om hur du använder syntaxen för att extrahera de insikter som du vill använda från data.

När du är bekant med sökmetoder kan du läsa den [logganalys logga Sök referens](log-analytics-search-reference.md).

## <a name="use-basic-filters"></a>Grundläggande filter
Det första du behöver veta är att den första delen av en sökning fråga innan någon ”|” lodräta vertikalstrecket är alltid en *filter*. Du kan se den som en WHERE-sats i TSQL--den avgör *vad* delmängd av data och hämtar utanför og Analytics-arbetsyta. Söka i datalagret är i stort sett information om hur du anger egenskaperna för de data som du vill extrahera, så är det naturligt att en fråga börjar med WHERE-satsen.

De mest grundläggande filter som du kan använda *nyckelord*, till exempel 'fel' eller 'timeout- eller ett datornamn. Dessa typer av enkla frågor returnerar vanligtvis olika former av data i samma resultatmängden. Detta beror på att Log Analytics har olika *typer* av data i systemet.

### <a name="to-conduct-a-simple-search"></a>Att genomföra en enkel sökning
1. I OMS-portalen klickar du på **loggen Sök**.  
    ![Sök sida vid sida](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. Ange i fältet fråga `error` och klicka sedan på **Sök**.  
    ![sökningsfel](./media/log-analytics-log-searches/oms-search-error.png)  
    Till exempel fråga om `error` i följande bild returnerade 100 000 **händelse** poster (som samlas in av loggen Management), 18 **ConfigurationAlert** poster (som genereras av Configuration Assessment) och 12 **ConfigurationChange** poster (avbildas av spårning av ändringar).   
    ![sökresultat](./media/log-analytics-log-searches/oms-search-results01.png)  

Dessa filter är inte riktigt typer/objektklasser. *Typen* är bara en tagg, eller en egenskap eller en sträng/namn/kategori, som är kopplad till en typ av data. Vissa dokument i systemet märks **typ: ConfigurationAlert** och vissa märks **typ: Perf**, eller **typ: händelsen**och så vidare. Varje sökresultat, dokument, post eller post visar rådata egenskaperna och deras värden för var och en av de olika delarna av data och du kan använda dessa fältnamn ange i filtret om du vill bara poster där fältet har det angivna värdet.

*Typen* är egentligen bara ett fält som alla poster har det inte skiljer sig från ett annat fält. Det har upprättats baserat på värdet för det typ av fältet. Posten har ett annat formen eller formulär. Tillfälligtvis, **typ = Perf**, eller **typ = händelse** är också den syntax som du behöver för att lära sig att fråga efter prestandadata och händelser.

Du kan använda ett kolon (:) eller ett likhetstecken (=) efter fältnamnet och före värdet. **Typ: händelsen** och **typ = händelse** har samma betydelse, du kan välja det format du föredrar.

I så fall om typen = Perf poster har ett fält med namnet 'CounterName, och du kan skriva en fråga som liknar `Type=Perf CounterName="% Processor Time"`.

Detta ger dig endast prestandadata där prestandaräknarnamnet är ”% processortid”.

### <a name="to-search-for-processor-time-performance-data"></a>Sök efter processor tid prestandadata
* Skriv i fältet Sök fråga`Type=Perf CounterName="% Processor Time"`

Du kan också vara mer specifik och använda **InstanceName = _ ”totalt'** i frågan, vilket är en Windows-prestandaräknare. Du kan också välja en begränsningsaspekt och en annan **fältvärdet:**. Filtret läggs automatiskt till ditt filter i frågan-fältet. Du kan se dessa i följande bild. Den visar var du ska klicka för att lägga till **instansnamn: ”_Total”** i frågan utan att ange något.

![Sök aspekten](./media/log-analytics-log-searches/oms-search-facet.png)

Frågan blir nu`Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

I det här exemplet du inte behöver ange **typ = Perf** till resultatet för den här. Eftersom det finns endast fälten CounterName och instansnamn för poster av typen = Perf, frågan är det specifikt nog för att returnera samma resultat som längre, tidigare:

```
CounterName="% Processor Time" InstanceName="_Total"
```

Detta beror på att alla filter i frågan utvärderas som *och* med varandra. Effektivt, fler fält du lägga till kriterierna du får mindre, mer specifikt och förfinad resultat.

Till exempel fråga `Type=Event EventLog="Windows PowerShell"` är identisk med `Type=Event AND EventLog="Windows PowerShell"`. Den returnerar alla händelser som har loggat in och samlas in från händelseloggen i Windows PowerShell. Om du lägger till ett filter flera gånger genom att välja samma aspekten flera gånger och sedan problemet är rent kosmetiskt--kanske det tar upp plats sökfältet, men samma resultat returneras fortfarande eftersom operatorn implicit och finns alltid.

Du kan enkelt ångra operatorn implicit och genom att använda en inte explicit. Exempel:

`Type:Event NOT(EventLog:"Windows PowerShell")`eller motsvarande `Type=Event EventLog!="Windows PowerShell"` returnera alla händelser från andra loggar som inte är Windows PowerShell-loggen.

Du kan också använda andra booleska operatorn som 'Eller'. Följande fråga returnerar poster där händelseloggen är antingen program eller System.

```
EventLog=Application OR EventLog=System
```

Med hjälp av frågan ovan, får du poster för båda loggar i samma resultatet.

Men om du tar bort eller genom att låta den implicita och i drift genererar sedan följande fråga inte något resultat eftersom det inte finns en post i händelseloggen som hör till båda loggarna. Varje post i händelseloggen skrevs till endast en av två loggar.

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>Använda ytterligare filter
Följande fråga returnerar poster för 2 händelseloggar för alla datorer som har skickat data.

```
EventLog=Application OR EventLog=System
```

![sökresultat](./media/log-analytics-log-searches/oms-search-results03.png)

Att välja ett fält eller filter ska begränsa frågan till en viss dator, förutom alla de. Den resulterande frågan liknar följande.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Vilket motsvarar följande, på grund av implicit och.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Varje fråga utvärderas i följande ordning explicit. Observera parentesen.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Precis som fältet händelselogg kan du hämta data endast för en uppsättning specifika datorer genom att lägga till eller. Exempel:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

På liknande sätt kan detta följande fråga returnerar **% processortid** för de valda två endast datorerna.

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```

### <a name="field-types"></a>Fälttyper
När du skapar filter, bör du förstå skillnader i att arbeta med olika typer av fält som returneras av loggen sökningar.

**Sökbara fält** visas i blått i sökresultaten.  Du kan använda sökbara fält i sökvillkor specifika fält, till exempel följande:

```
Type: Event EventLevelName: "Error"
Type: SecurityEvent Computer:Contains("contoso.com")
Type: Event EventLevelName IN {"Error","Warning"}
```

**Kostnadsfri text sökbara fält** visas i grått i sökresultaten.  De kan inte användas med sökvillkor som är specifika för fält som sökbara fält.  De genomsöks endast när du utför en fråga i alla fält till exempel följande.

```
"Error"
Type: Event "Exception"
```


### <a name="boolean-operators"></a>Booleska operatorer
Med datetime och numeriska fält, kan du söka efter värden med *större än*, *mindre än*, och *mindre än eller lika med*. Du kan använda enkla operatorer som >, <>, =, < =,! = i sökfältet frågan.

Du kan fråga en specifik händelselogg för en viss tidsperiod. Till exempel uttrycks de senaste 24 timmarna med följande mnemoteknisk uttryck.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>Att sökningen med booleska operatorer
* Skriv i fältet Sök fråga`EventLog=System TimeGenerated>NOW-24HOURS`  
    ![söka med booleskt värde](./media/log-analytics-log-searches/oms-search-boolean.png)

Även om du kan styra tidsintervallet grafiskt och de flesta gånger kanske du vill göra det, har fördelar inklusive ett tidsfilter direkt i frågan. T.ex, detta passar utmärkt instrumentpaneler där du kan åsidosätta tid för varje bricka oberoende av den *globala* tid val på instrumentpanelssidan. Mer information finns i [tid frågor i instrumentpanelen](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

När du filtrerar efter tid, Tänk på att du får resultat för de *skärningspunkten* av två tidsperioder: den som anges i OMS-portalen (S1) och den som angetts i frågan (S2).

![skärningspunkten](./media/log-analytics-log-searches/oms-search-intersection.png)

Detta innebär att, om tidsperioderna inte överlappa, till exempel i OMS-portalen där du väljer **den här veckan** och i frågan där du definierar **förra veckan**, det finns inga skärningspunkten och visas inte några resultat.

Jämförelseoperatorer som används för fältet TimeGenerated är också användbara i andra situationer. Till exempel med numeriska fält.

Till exempel anges att konfigurationen Assessment aviseringar har följande allvarlighetsgrader:

* 0 = information
* 1 = varning
* 2 = kritisk

Du kan fråga efter både varningar och kritiska aviseringar och utesluta information de med följande fråga:

```
Type=ConfigurationAlert  Severity>=1
```


Du kan också använda intervallet frågor. Det innebär att du kan ange början och slutet intervallet för värden i en sekvens. Till exempel om du vill ha händelser från Operations Manager-händelseloggen där den händelse-ID är större än eller lika med 2100 men inte mer än 2199 returnerar sedan följande fråga dem.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


> [!NOTE]
> Intervallet syntaxen måste du använda är kolon (:) fältvärdet: avgränsare och *inte* likhetstecken (=). Lägre och övre delen av intervallet omges av hakparenteser och avgränsa dem med två punkter (.).
>
>

## <a name="manipulate-search-results"></a>Hantera sökresultat
När du söker efter data vill du förfina sökningen och har en bra kontroll över resultatet. Du kan använda kommandon för att omvandla dem när resultaten hämtas.

Kommandon i logganalys sökningar *måste* följer efter det lodräta vertikalstrecket (|). Ett filter måste alltid vara den första delen av en frågesträng. Den definierar vilka data du arbetar med och sedan ”kommer” resultaten i ett kommando. Du kan sedan använda pipe för att lägga till ytterligare kommandon. Detta liknar löst Windows PowerShell-pipeline.

I allmänhet försöker logganalys sökspråk följer PowerShell format och riktlinjer för att göra det liknar IT-proffs och underlättar inlärningskurvan.

Kommandon har namn med verb, så att du enkelt kan se vad de gör.  

### <a name="sort"></a>Sortera
Sortera kan du definiera sorteringsordningen genom att ett eller flera fält. Även om du inte använder den, som standard, tillämpas taget fallande ordning. Senaste resultat är alltid högst upp i sökresultaten. Detta innebär att när du kör en sökning med `Type=Event EventID=1234` vad verkligen körs du är:

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

Det beror på att det är typen av du är bekant med i loggarna. Till exempel i Loggboken i Windows.

Du kan använda sortera så att resultaten returneras. I följande exempel visas hur det fungerar.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Enkel exemplen ovan visar hur kommandon fungerar--de ändra formen av de resultat som returneras av filtret.

### <a name="limit-and-top"></a>Gränsen och upp
Ett annat mindre kända kommando är GRÄNSEN. Gränsen är ett PowerShell-liknande verb. Gränsen är funktionellt identiskt med kommandot ÖVERSTA. Följande frågor ger samma resultat.

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>Att söka med överst
* Skriv i fältet Sök fråga`Type=Event EventID=600 | Top 1`   
    ![Sök upp](./media/log-analytics-log-searches/oms-search-top.png)

I bilden ovan finns 358 tusen poster med händelse-ID = 600. Fält, facets och filter till vänster alltid visar information om resultaten *av filter-del* av frågan som är en del innan alla vertikalstrecket. Den **resultat** fönstret returnerar endast det senaste resultatet 1, eftersom detta kommando Formats och omvandlas resultaten.

### <a name="select"></a>Välj
SELECT-kommandot fungerar som Select-Object i PowerShell. Returnerar den filtrerade resultat som inte har alla sina ursprungliga egenskaper. I stället väljs egenskaper som du anger.

#### <a name="to-run-a-search-using-the-select-command"></a>Att köra en sökning med select-kommando
1. Skriv i Sök `Type=Event` och klicka sedan på **Sök**.
2. Klicka på **+ visa fler** i ett resultat för att visa alla egenskaper som har resultaten.
3. Välj några av de uttryckligen och frågan ändras till `Type=Event | Select Computer,EventID,RenderedDescription`.  
    ![sökningens](./media/log-analytics-log-searches/oms-search-select.png)

Det här kommandot är särskilt användbart när du vill styra sökningen utdata och välj endast delar av data som verkligen är viktiga för din undersökning, vilket ofta är inte fullständig posten. Detta är också användbart när poster av olika typer har *vissa* gemensamma egenskaper, men inte *alla* är gemensamma för deras egenskaper. Du kan generera utdata som ser ut mer naturligt som en tabell eller fungera bra när exporteras till en CSV-fil och sedan massaged i Excel.

## <a name="use-the-measure-command"></a>Använd kommandot mått
MÅTTET är något av kommandona mest flexibla i logganalys-sökningar. Det kan du använda statistiska *funktioner* till dina data och mängdresultat grupperade efter ett visst fält. Det finns flera statistiska funktioner som har stöd för måttet.

### <a name="measure-count"></a>Måttet count()
Den första statistiska funktionen att arbeta med och en av enklast att förstå är den *count()* funktion.

Resultat från en sökfråga som `Type=Event`, visa filter som kallas även aspekter på vänster sida i sökresultaten. Filtren visar en fördelning av värden med ett visst fält för resultaten i sökningen utförs.

![Sök mått Antal](./media/log-analytics-log-searches/oms-search-measure-count01.png)

Till exempel i bilden ovan visas den **datorn** och visar att i nästan 739 tusen-händelser i resultatet vissa 68 unika och skilda värden för den **datorn** i dessa poster. Panelen visar bara upp 5, som är de vanligaste 5 värdena som har skrivits i den **datorn** fält), sorterade efter antalet dokument som innehåller det specifikt värdet i fältet. I bilden ser du att – 90 tusen bland de nästan 369 tusen händelserna – komma från datorns OpsInsights04.contoso.com 83 tusen från DB03.contoso.com-datorn och så vidare.

Vad händer om du vill se alla värden eftersom panelen endast visar endast överst 5?

Det här är vad kommandot mått kan göra med funktionen count(). Den här funktionen används inte några parametrar. Du bara ange det fält som du vill gruppera efter – den **datorn** fältet i det här fallet:

`Type=Event | Measure count() by Computer`

![Sök mått Antal](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

Dock **datorn** är bara ett fält används *i* varje datadel – det finns inga relationsdatabaser inblandade och det finns ingen separat **datorn** objekt var som helst. Bara värdena *i* data beskrivs vilken entitet genererade dem, och ett antal andra egenskaper och aspekter av data och därför termen *aspekten*. Du kan bara också gruppera efter andra fält. Eftersom de ursprungliga resultaten av nästan 739 tusen händelser som skickas till kommandot mått har också ett fält med namnet **EventID**, kan du använda samma metod för att gruppera efter fältet och få ett antal händelser efter händelse-ID:

```
Type=Event | Measure count() by EventID
```

Om du inte är intresserad av att antalet faktiska post som innehåller ett visst värde, men i stället om du bara vill en lista över de faktiska värdena du kan lägga till en *Välj* kommando i slutet av den och markera den första kolumnen:

```
Type=Event | Measure count() by EventID | Select EventID
```

Sedan kan du få mer komplicerat och sortera resultaten i frågan, eller klicka bara kolumner i rutnätet för.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>Så här söker du använder mått Antal
* Skriv i fältet Sök fråga`Type=Event | Measure count() by EventID`
* Lägg till `| Select EventID` till slutet av frågan.
* Lägg slutligen till `| Sort EventID asc` till slutet av frågan.

Det finns några viktiga saker att Observera och betona:

Först är resultatet visas inte de ursprungliga rådata resultat längre. De är i stället sammanlagda resultat – i princip grupper av resultaten. Detta är inte ett problem, men du bör känna till att du interagerar med en mycket annan form av data som skiljer sig från den ursprungliga rådata formen som skapas direkt på grund av funktionen aggregering för statistisk.

Andra, **mäta antalet** returnerar för närvarande endast de 100 främsta distinkta resultat. Den här begränsningen gäller inte för de statistiska funktionerna. Så behöver du vanligtvis använda mer exakta filter först att söka efter specifika objekt innan du installerar mått count().

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>Använda max och min med kommandot mått
Det finns olika scenarier där **mått Max()** och **mått Min()** är användbara. Men eftersom varje funktion är motsatt av varandra, kommer vi illustrerar Max() och du kan experimentera med Min() på egen hand.

Om du frågar efter säkerhetshändelser som de har en **nivå** egenskap som kan variera. Exempel:

```
Type=SecurityEvent
```

![Sök mått Antal start](./media/log-analytics-log-searches/oms-search-measure-max01.png)

Om du vill visa det högsta värdet för alla säkerheten kan händelser ges en gemensam dator, gruppera efter fält, du använda

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![söka mått max dator](./media/log-analytics-log-searches/oms-search-measure-max02.png)

Visas som för de datorer som hade **nivå** poster, de flesta av dem har minst nivå 8 många hade en nivå av 16.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![max söktiden för måttet genereras dator](./media/log-analytics-log-searches/oms-search-measure-max03.png)

Den här funktionen fungerar bra med siffror, men den fungerar även med DateTime-fält. Det är praktiskt att söka efter senaste eller senaste tidsstämpeln för ett datablock indexerat för varje dator. Exempel: när den senaste säkerhetshändelse rapporterades för varje dator?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>Använda avg-funktionen med kommandot mått
Avg() statistisk funktionen som används med mått kan du beräkna medelvärdet för vissa fält och gruppera resultat från samma eller andra fält. Detta är användbart i en mängd fall, till exempel prestandadata.

Vi börjar med prestandadata. Observera att Log Analytics för närvarande samlas in prestandaräknare för både Windows- och Linux-datorer.

Sök efter *alla* prestandadata, mest grundläggande frågan är:

```
Type=Perf
```

![Sök avg start](./media/log-analytics-log-searches/oms-search-avg01.png)

Det första du märker är att visar logganalys tre perspektiv: listan som visas som visar de faktiska posterna bakom bubbeldiagram. Tabell som visar en tabellvy prestandaräknardata; och mått som visar diagram för prestandaräknare.

Det finns två uppsättningar med fält som har markerats som visar följande i bilden ovan:

* Den första uppsättningen identifierar Windows namn på prestandaräknare, objektnamn och instansnamnet i fråga filter. Dessa är de fält du förmodligen oftast används som facets-filter
* **CounterValue** är det faktiska värdet för räknaren. I det här exemplet är värdet *75*.
* **TimeGenerated** är 12:51 i 24-timmarsformat.

Här är en vy över mått i ett diagram.

![Sök avg start](./media/log-analytics-log-searches/oms-search-avg02.png)

Efter att läsa mer om formen Perf poster och läst om andra sökmetoder kan använda du måttet Avg() för att sammanställa den här typen av numeriska data.

Här är ett enkelt exempel:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![Sök avg samplevalue](./media/log-analytics-log-searches/oms-search-avg03.png)

I det här exemplet väljer du Total CPU-tid prestanda räknare och genomsnittlig per dator. Om du vill begränsa sökresultaten till endast de senaste 6 timmarna kan du använda filterkontrollen tid eller ange i frågan på följande sätt:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>Att söka med avg-funktionen med kommandot mått
* Skriv i sökrutan frågan `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`.

Du kan aggregera och korrelera data *över* datorer. Anta exempelvis att du har en uppsättning värdar i någon form av servergruppen där varje nod är lika med andra någon och de samma skriver arbete och bör vara ungefär belastningsutjämnade. Du kan få räknare i en Gå med följande fråga och få medelvärden för hela servergruppen. Du kan starta genom att välja datorer med följande exempel:

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Nu när du har datorer kan du bara vill välja två nyckeltal (KPI: er): % CPU-användning och % ledigt utrymme. Därför blir den delen av frågan:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Nu kan du lägga till datorer och räknare med följande exempel:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Eftersom du har en särskild markering av **mäta Avg()** kommando kan returnera medelvärdet inte av datorn, men hela servergruppen, genom att gruppera efter CounterName. Exempel:

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

Detta ger dig en användbar komprimerad vy i ett par KPI: er i din miljö.

![Sök avg gruppering](./media/log-analytics-log-searches/oms-search-avg04.png)

Du kan enkelt använda frågan i en instrumentpanel. Exempelvis kan du spara frågan och skapa en instrumentpanel i den namngivna *Web servergruppen KPI: er*. Läs mer om att använda instrumentpaneler i [skapa en anpassad instrumentpanel i logganalys](log-analytics-dashboards.md).

![Sök avg instrumentpanelen](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>Använd sum-funktionen med kommandot mått
Sum-funktionen liknar andra funktioner i kommandot mått. Du kan se ett exempel om hur du använder summafunktionen på [W3C IIS loggar sökning i Microsoft Azure åtgärdsinformation](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Du kan använda Max() och Min() med siffror, datum och textsträngar. Med textsträngar, de sorteras alfabetiskt och du hämta första och sista.

Du kan dock använda SUMMA() med något annat än numeriska fält. Detta gäller även för Avg().

### <a name="use-the-percentile-function-with-the-measure-command"></a>Använd percentilfunktionen med kommandot mått
Percentilfunktionen liknar Avg() och SUMMA() i som kan endast använda för numeriska fält. Du kan använda alla percentil mellan 1 och 99 för ett numeriskt fält. Du kan också använda båda **percentil** och **pct** kommandon. Här följer några exempel:  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>Använd till kommandot
Den där kommandot fungerar som ett filter, men den kan användas i pipeline för att filtrera ytterligare sammanlagda resultat som har producerats av måttet kommandot – i stället för raw resultat som är filtrerade i början av en fråga.

Exempel:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

Du kan lägga till en annan pipe ”|” tecken och Where-kommandot för att bara hämta datorer vars Genomsnittlig CPU är över 80% med följande exempel:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Om du är bekant med Microsoft System Center - Operations Manager kan du se where kommandot i management pack villkor. En regel om exemplet, den första delen av frågan skulle vara datakällan och var skulle kommandot se ut villkorsidentifiering.

Du kan använda frågan som en panel i **min instrumentpanel**, som en Övervakare av sorteringar och se när datorn processorer är överutnyttjade. Läs mer om instrumentpaneler i [skapa en anpassad instrumentpanel i logganalys](log-analytics-dashboards.md). Du kan också skapa och använda instrumentpaneler med hjälp av mobilappen. Mer information finns i [OMS Mobilapp ](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). I de nedre två panelerna i följande bild visas övervakaren visas en lista och som ett tal. I princip vill du alltid värdet till noll och listan måste vara tomma. Annars visar en varning villkor. Om det behövs kan du använda den för att ta en titt på vilka datorer som är under hög belastning.

![mobila instrumentpanelen](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>Använda operatorn i
Den *IN* -operator, tillsammans med *NOT IN* kan du använda subsearches som sökningar som inkluderar en ny sökning som argument. De finns i klammerparenteserna {} i en annan *primära* eller *yttre* sökning. Resultatet av en subsearch ofta en lista över olika resultat används sedan som ett argument i dess primära sökningen.

Du kan använda subsearches för att matcha delmängder av data som du inte kan beskriva direkt i ett sökuttryck, men som kan genereras från en sökning. Om du är intresserad av att använda en sökning efter alla händelser från exempelvis *datorer som saknar säkerhetsuppdateringar*, måste du utforma en subsearch som identifierar som först *datorer som saknar säkerhetsuppdateringar* innan den hittar händelser som hör till de här värdarna.

Så kan du express *datorer som för närvarande saknar nödvändiga säkerhetsuppdateringar* med följande fråga:

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![I Sök-exempel](./media/log-analytics-log-searches/oms-search-in01-revised.png)

När du har i listan kan använda du sökningen som en inre sökning för att mata in en lista med datorer i en yttre (primära) sökning som söker efter händelser för dessa datorer. Du kan göra detta genom att omsluta den inre sökningen med klammerparenteser och mata resultaten som möjliga värden för yttre sökningen med operatorn i filtret/fältet. Frågan liknar följande:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![I Sök-exempel](./media/log-analytics-log-searches/oms-search-in02-revised.png)

Även meddelandet tidsfiltret i inre sökningen eftersom systemet Update Assessment tar en ögonblicksbild av alla datorer var 24: e timme. Du kan göra den inre frågan lightweight och mer exakt genom att bara söka efter en dag. Yttre sökningen används i stället valet av tid i användargränssnittet, hämta händelser från de senaste 7 dagarna. Se [booleska operatorer](#boolean-operators) mer information om tid operatörer.

Eftersom du verkligen Använd bara resultaten av den inre som ett filter värde för den yttre, kan du fortfarande använda kommandon i yttre sökningen. Du kan till exempel fortfarande gruppera händelserna ovan med ett annat mått kommando:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![I Sök-exempel](./media/log-analytics-log-searches/oms-search-in03-revised.png)

I allmänhet kan du inre frågan köras snabbt eftersom logganalys har tjänsten på klientsidan tidsgränser för det och att returnera en liten mängd resultat. Om den inre frågan returnerar fler resultat, hämtar resultatlistan trunkerad, vilket kan medföra yttre sökning för att returnera felaktiga resultat.

En annan regel är att inre sökningen för närvarande måste ange *aggregerade* resultat. Med andra ord, det måste innehålla en *mått* kommandot; du kan för närvarande feed rådata resultat i en yttre sökning.

Dessutom kan det finnas en enda IN-operatorn och det måste vara det sista filtret i frågan. Flera IN operatorer får inte vara eller skulle – det i praktiken innebär att köra flera subsearches: Det viktiga är att endast en sub/inre sökningen är möjligt för varje yttre sökning.

Även om dessa begränsningar i gör många typer av korrelerade sökningar och kan du definiera något som liknar grupper, till exempel datorer, användare eller filer – oavsett fälten i dina data innehåller. Här följer fler exempel:

**Alla uppdateringar som saknas från datorer där inställningen för automatiska uppdateringar är inaktiverad**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**Alla fel-händelser från datorer som kör SQL Server (= där SQL-bedömning har körts)**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**Alla säkerhetshändelser från datorer som är domänkontrollanter (= där AD-bedömning har körts)**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**Som andra konton har loggat in till samma datorer där kontot BACONLAND\jochan har loggat in?**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>Använd kommandot distinkta
Det här kommandot ger en lista över distinkta värden för ett fält som namnet antyder. Det är förstås enkla men ganska användbart. Samma kan uppnås med måttet count() kommandot samt, enligt nedan.

```
Type=Event | Measure count() by Computer
```

![DISTINKTA Sök exemplet](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

Om du är intresserad är dock bara en lista över distinkta värden och inte antalet dokument som innehåller värden och sedan DISTINCT kan ge tydligare och lättare att läsa utdata och kortare syntax som visas nedan.

```
Type=Event | Distinct Computer
```
![DISTINKTA Sök exemplet](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>Använd funktionen countdistinct med kommandot mått
Funktionen countdistinct räknar antalet distinkta värden inom varje grupp. Till exempel kan den användas för att räkna antalet unika datorer som rapporterar för varje typ:

```
* | measure countdistinct(Computer) by Type
```

![OMS-countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>Använd kommandot mått intervall
Med nästan realtid prestandadatainsamling, kan du samla in och visualisera alla prestandaräknare i logganalys. Att bara ange frågan **typ: Perf** returnerar tusentals mått diagram baserat på antalet räknare och servrar i logganalys-miljö. Med på begäran mått aggregering tittar du på den övergripande måtten i din miljö vid en hög nivå och ingående till mer detaljerade data som du behöver.

Anta att du vill veta vad är den genomsnittliga CPU på alla datorer. Titta på Genomsnittlig CPU för varje dator kanske inte praktiskt eftersom resultat kan hämta jämnas ut. Om du vill se mer detaljer kan du aggregera dina resultat i en mindre tid fönstret blocken och leta till en tidsserie på olika dimensioner. T.ex, kan du utföra timvis medelvärdet av CPU-användning på alla datorer på följande sätt:

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![måttet genomsnittlig intervall](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

Som standard visas resultaten i interaktiva linjediagram med flera serier.  Det här diagrammet stöder serien växla (med y-axeln rescaling), Zooma och placera markören.  Visningsalternativ för tabellen finns kvar för att visa rådata om det behövs.

Du kan också gruppera efter andra fält. I det här exemplet jag tittar på alla % räknare för en viss dator och du vill veta vad är varje timme 70 percentiler för varje räknare:

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
Observera är dessa frågor inte är begränsade till prestandaräknare. Du kan använda dem på alla mått. I det här exemplet tittar jag på W3C IIS-loggar. Jag vill veta vad är den maximala tid det tar över 5-minutersintervall för bearbetning av varje förfrågan:

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>Använda flera aggregeringar i en fråga
Du kan ange flera sammanställd satser i kommandot mått.  Alias kan vara oberoende av var och en.  Om den inte ges ett alias blir det resulterande fältnamnet mängdfunktionen som var används (d.v.s. ”avg(CounterValue)” för avg(CounterValue)).

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

Här är ett annat exempel:

 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>Nästa steg
Mer information om loggen sökningar finns:

* Använd [anpassade fält i logganalys](log-analytics-custom-fields.md) att utöka loggen sökningar.
* Granska de [logganalys logga Sök referens](log-analytics-search-reference.md) att visa alla sökfält och aspekter som är tillgängliga i logganalys.
