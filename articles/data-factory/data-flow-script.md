---
title: Mappa dataflödesskript
description: Översikt över Data Factorys skriptkod för dataflödesskript bakom
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.openlocfilehash: 251507c5740af69bd0818391dd2e8f857338b6cf
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313919"
---
# <a name="data-flow-script-dfs"></a>Dataflödesskript (DFS)

Dataflödesskript (DFS) är de underliggande metadata, som liknar ett kodningsspråk, som används för att köra de omvandlingar som ingår i ett mappningsdataflöde. Varje omvandling representeras av en serie egenskaper som ger nödvändig information för att köra jobbet korrekt. Skriptet är synligt och redigerbart från ADF genom att klicka på "script"-knappen i det övre menyfliksområdet i webbläsargränssnittet.

![Knappen Skript](media/data-flow/scriptbutton.png "Knappen Skript")

I en `allowSchemaDrift: true,` källomvandling talar till exempel om för tjänsten att inkludera alla kolumner från källdatauppsättningen i dataflödet även om de inte ingår i schemaprojektionen.

## <a name="use-cases"></a>Användningsfall
DFS produceras automatiskt av användargränssnittet. Du kan klicka på knappen Skript för att visa och anpassa skriptet. Du kan också generera skript utanför ADF-användargränssnittet och sedan skicka det till PowerShell-cmdleten. När du felsöker komplexa dataflöden kan det vara enklare att skanna skriptet bakomkod i stället för att skanna gränssnittsdiagramrepresentationen av dina flöden.

Här är några exempel användningsfall:
- Programatiskt producera många dataflöden som är ganska lika, dvs "stämpling ut" dataflöden.
- Komplexa uttryck som är svåra att hantera i användargränssnittet eller som resulterar i valideringsproblem.
- Felsökning och bättre förståelse av olika fel som returnerades under körningen.

När du skapar ett dataflödesskript som ska användas med PowerShell eller ett API måste du komprimera den formaterade texten till en enda rad. Du kan behålla flikar och nya linjer som escape-tecken. Men texten måste formateras så att den får plats i en JSON-egenskap. Det finns en knapp på skriptredigerarens användargränssnitt längst ned som formaterar skriptet som en enda rad åt dig.

![Knappen Kopiera](media/data-flow/copybutton.png "Knappen Kopiera")

## <a name="how-to-add-transforms"></a>Så här lägger du till transformeringar
Att lägga till omvandlingar kräver tre grundläggande steg: lägga till kärnomvandlingsdata, omdirigera indataströmmen och sedan omdirigera utdataströmmen. Detta kan ses enklast i ett exempel.
Låt oss säga att vi börjar med en enkel källa för att sänka dataflödet som följande:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Om vi bestämmer oss för att lägga till en härleda omvandling, först måste vi `upperCaseTitle`skapa kärnan omvandling text, som har ett enkelt uttryck för att lägga till en ny versal kolumn som heter:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Sedan tar vi den befintliga DFS och lägger till omvandlingen:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Och nu omdirigerar vi den inkommande strömmen genom att identifiera vilken omvandling vi `source1`vill att den nya omvandlingen ska komma efter (i det här fallet) och kopiera strömmens namn till den nya omvandlingen:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Slutligen identifierar vi den omvandling vi vill komma efter denna nya omvandling, `sink1`och ersätta dess ingångsström (i detta fall) med utdataströmnamnet på vår nya omvandling:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS fundamenta
DFS består av en serie anslutna omvandlingar, inklusive källor, sänkor och flera andra som kan lägga till nya kolumner, filtrera data, koppla data och mycket mer. Vanligtvis skriptet med början med en eller flera källor följt av många omvandlingar och slutar med en eller flera sänkor.

Källor har alla samma grundläggande konstruktion:
```
source(
  source properties
) ~> source_name
```

En enkel källa med tre kolumner (movieId, titel, genrer) skulle till exempel vara:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Alla andra omvandlingar än källor har samma grundläggande konstruktion:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

En enkel härleda omformning som tar en kolumn (rubrik) och skriver över den med en versalversion skulle till exempel vara följande:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

Och en diskbänk utan schema skulle helt enkelt vara:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Skriptutdrag

Skriptkodavsnitt är delningsbar kod för dataflödesskript som du kan använda för att dela mellan dataflöden. I videon nedan beskrivs hur du använder skriptkodavsnitt och använder dataflödesskript för att kopiera och klistra in delar av skriptet bakom dataflödesdiagrammen:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Aggregerad sammanfattningsstatistik
Lägg till en aggregerad omvandling till ditt dataflöde som kallas "SummaryStats" och klistra sedan in den här koden nedan för den mängdiska funktionen i skriptet och ersätter de befintliga SummaryStats. Detta ger ett allmänt mönster för statistik över sammanfattning av dataprofiler.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Du kan också använda exemplet nedan för att räkna antalet unika och antalet olika rader i dina data. Exemplet nedan kan klistras in i ett dataflöde med aggregerad omvandling som kallas ValueDistAgg. I det här exemplet används en kolumn som heter "titel". Var noga med att ersätta "titel" med strängkolumnen i dina data som du vill använda för att få värde räknas.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Inkludera alla kolumner i en mängd
Det här är ett allmänt aggregerat mönster som visar hur du kan behålla de återstående kolumnerna i utdatametadata när du skapar aggregat. I det här fallet ```first()``` använder vi funktionen för att välja det första värdet i varje kolumn vars namn inte är "film". Om du vill använda detta skapar du en mängdomvandling som kallas DistinctRows och klistrar sedan in den i skriptet över toppen av det befintliga Aggregatet För DistinctRows.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Skapa radhh-fingeravtryck 
Använd den här koden i dataflödesskriptet ```DWhash``` om du ```sha1``` vill skapa en ny härledd kolumn som kallas som skapar en hash med tre kolumner.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>Nästa steg

Utforska dataflöden genom att börja med [översiktsartikeln för dataflöden](concepts-data-flow-overview.md)
