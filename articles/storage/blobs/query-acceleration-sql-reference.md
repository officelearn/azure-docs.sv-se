---
title: SQL-språkreferens för frågeacceleration (förhandsgranskning)
titleSuffix: Azure Storage
description: Lär dig hur du använder sql-syntax för frågeacceleration.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772124"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>SQL-språkreferens för frågeacceleration (förhandsgranskning)

Frågeacceleration stöder ett ANSI SQL-liknande språk för att uttrycka frågor över blob-innehåll.  Frågeacceleration SQL dialekt är en delmängd av ANSI SQL, med en begränsad uppsättning datatyper som stöds, operatorer, etc., men det expanderar också på ANSI SQL för att stödja frågor över hierarkiska semi-strukturerade dataformat som JSON. 

> [!NOTE]
> Frågeaccelerationfunktionen är i offentlig förhandsversion och är tillgänglig i regionerna Kanada Central och France Central. Information om hur du granskar begränsningar finns i artikeln [Kända problem.](data-lake-storage-known-issues.md) Om du vill registrera dig i förhandsgranskningen läser du [det här formuläret](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>MARKERA Syntax

Det enda SQL-uttryck som stöds av frågeacceleration är SELECT-uttrycket. Det här exemplet returnerar varje rad som uttrycket returnerar sant.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

För CSV-formaterade data *table* måste `BlobStorage`tabellen vara .  Det innebär att frågan körs mot vilken blob som angavs i REST-anropet.
För JSON-formaterade data är *tabellen* en "tabellbeskrivare".   Se avsnittet [Tabellbeskrivningar](#table-descriptors) i den här artikeln.

I följande exempel returnerar den här uttrycket en ny rad som görs från utvärderingen av vart och ett av projektionsuttrycken för varje rad som returneras i *WHERE-uttrycket.*


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

I följande exempel returneras en mängdberäkning (till exempel medelvärdet för en viss kolumn) över var och en av de rader för vilka *uttrycket* returnerar sant. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

I följande exempel returneras lämpliga förskjutningar för att dela upp en CSV-formaterad blob.  Se avsnittet [Sys.Split](#sys-split) i den här artikeln.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Datatyper

|Datatyp|Beskrivning|
|---------|-------------------------------------------|
|INT      |64-bitars signerat heltal.                     |
|Flyta    |64-bitars ("dubbel precision") flyttals.|
|Sträng   |Unicode-sträng med variabel längd.            |
|TIMESTAMP|En tidpunkt.                           |
|Boolean  |Sant eller falskt.                             |

När du läser värden från CSV-formaterade data läses alla värden som strängar.  Strängvärden kan konverteras till andra typer med CAST-uttryck.  Värden kan implicit kastas till andra typer beroende på kontext. Mer information finns i [Datatypsprioritet (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Uttryck

### <a name="referencing-fields"></a>Referera till fält

För JSON-formaterade data, eller CSV-formaterade data med en rubrikrad, kan fält refereras med namn.  Fältnamn kan citeras eller tas bort. Citerade fältnamn omges av dubbla citattecken ("), kan innehålla blanksteg och är skiftlägeskänsliga.  Ocierade fältnamn är skiftlägesokänsliga och får inte innehålla några specialtecken.

I CSV-formaterade data kan fält också refereras av ordningsfält, som föregås av ett understreck (_) tecken.  Det första fältet kan till exempel refereras till _1, eller så kan det elfte fältet refereras till _11.  Att referera till fält efter ordningsordning är användbart för CSV-formaterade data som inte innehåller en rubrikrad, i vilket fall det enda sättet att referera till ett visst fält är genom ordningstid.

### <a name="operators"></a>Operatorer

Följande standard-SQL-operatörer stöds:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Om datatyperna till vänster och höger om en operator är olika utförs automatisk konvertering enligt de regler som anges här: [Datatypsprioritet (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)

SQL-språket för frågeacceleration stöder endast en mycket liten delmängd av de datatyper som beskrivs i den artikeln.  Se avsnittet [Datatyper](#data-types) i den här artikeln.

### <a name="casts"></a>Kastar

SQL-språket för frågeacceleration stöder CAST-operatorn enligt reglerna här: [Datatypskonvertering (databasmotor)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

SQL-språket för frågeacceleration stöder endast en liten delmängd av de datatyper som beskrivs i den artikeln.  Se avsnittet [Datatyper](#data-types) i den här artikeln.

### <a name="string-functions"></a>Strängfunktioner

SQL-språket för frågeacceleration stöder följande vanliga SQL-strängfunktioner:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

Här är några exempel:

|Funktion|Exempel|Resultat|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

[Funktionen LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) hjälper dig att söka efter ett mönster. Här är några exempel som använder funktionen [GILLA](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``för att söka i datasträngen .

|Söka i data|Exempel|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Datumfunktioner

Följande standardfunktioner för SQL-datum stöds:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

För närvarande konverterar vi alla [datumformat av standard IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>DATE_ADD funktion

Frågeacceleration SQL-språket stöder år, månad, dag, timme, minut, sekund för ``DATE_ADD`` funktionen.

Exempel:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF funktion

Frågeacceleration SQL-språket stöder år, månad, dag, timme, minut, sekund för ``DATE_DIFF`` funktionen.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>FUNKTIONEN EXTRACT

För EXTRA än datumdel ``DATE_ADD`` som stöds för funktionen stöder SQL-språket för frågeacceleration timezone_hour och timezone_minute som datumdel.

Exempel:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING funktion

Exempel:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

I den här tabellen beskrivs strängar som du ``TO_STRING`` kan använda för att ange utdataformatet för funktionen.

|Sträng för format    |Resultat                               |
|-----------------|-------------------------------------|
|yy               |År i tvåsiffrigt format – 1999 som "99"|
|y                |År i fyrsiffrigt format               |
|yyyy             |År i fyrsiffrigt format               |
|M                |Månad på året – 1                    |
|MM               |Noll vadderad månad – 01               |
|MMM              |Abbr. månad år -JAN            |
|MMMM             |Hel månad – maj                      |
|d                |Dag i månaden (1-31)                  |
|dd               |Noll vadderad dag i månaden (01-31)     |
|a                |AM eller PM                             |
|h                |Timme på dagen (1-12)                   |
|hh               |Noll vadderade timmar od dag (01-12)     |
|H                |Timme på dagen (0-23)                   |
|HH               |Noll vadderad timme på dagen (00-23)      |
|m                |Minut (0-59)                |
|mm               |Noll vadderad minut (00-59)           |
|s                |Andra av protokollet (0-59)             |
|ss               |Noll vadderade sekunder (00-59)          |
|S                |Bråkdel av sekunder (0,1-0,9)        |
|SS               |Bråkdel av sekunder (0,01-0,99)      |
|Sss              |Bråkdel av sekunder (0,001-0,999)    |
|X                |Förskjutning i timmar                      |
|XX eller XXXX       |Förskjutning i timmar och minuter (+0430)  |
|XXX eller XXXXX     |Förskjutning i timmar och minuter (-07:00) |
|x                |Förskjutning i timmar (7)                  |
|xx eller xxxx       |Förskjutning i timme och minut (+0530)    |
|Xxx eller xxxxx     |Förskjutning i timme och minut (+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP funktion

Endast IS08601-format stöds.

Exempel:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Du kan också ``UTCNOW`` använda funktionen för att få systemtiden.


## <a name="aggregate-expressions"></a>Aggregera uttryck

En SELECT-sats kan innehålla antingen ett eller flera projektionsuttryck eller ett enda aggregerat uttryck.  Följande aggregerade uttryck stöds:

|Uttryck|Beskrivning|
|--|--|
|[ANTAL(\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Returnerar antalet poster som matchade predikatuttrycket.|
|[ANTAL(uttryck)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Returnerar antalet poster för vilka uttrycket inte är null.|
|[MEDEL(uttryck)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Returnerar medelvärdet av de icke-null-värden för uttrycket.|
|[MIN(uttryck)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Returnerar uttryckets lägsta icke-null-värde.|
|[MAX(uttryck)](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)    |Returnerar uttryckets högsta icke-null-värde.|
|[SUMMA(uttryck)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Returnerar summan av alla icke-null-värden för uttryck.|

### <a name="missing"></a>Saknas

Operatorn ``IS MISSING`` är den enda icke-standard som frågeacceleration SQL-språket stöder.  Om ett fält saknas i en viss indatapost ``IS MISSING`` för JSON-data utvärderas uttrycksfältet till det booleska värdet true.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Tabellbeskrivningar

För CSV-data är tabellnamnet alltid `BlobStorage`.  Ett exempel:

```sql
SELECT * FROM BlobStorage
```

För JSON-data finns ytterligare alternativ:

```sql
SELECT * FROM BlobStorage[*].path
```

Detta gör det möjligt att fråga över delmängder av JSON-data.

För JSON-frågor kan du nämna sökvägen i en del av FROM-satsen. Dessa sökvägar hjälper till att tolka delmängden av JSON-data. Dessa banor kan referera till JSON-matris- och objektvärden.

Låt oss ta ett exempel för att förstå detta mer i detalj.

Detta är våra exempeldata:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Du kanske bara är `warehouses` intresserad av JSON-objektet från ovanstående data. Objektet `warehouses` är en JSON-matristyp, så du kan nämna detta i FROM-satsen. Exempelfrågan kan se ut ungefär så här.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

Frågan hämtar alla fält men väljer bara latitud.

Om du bara vill `dimensions` komma åt JSON-objektvärdet kan du använda referera till objektet i frågan. Ett exempel:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Detta begränsar också din åtkomst `dimensions` till medlemmar i objektet. Om du vill komma åt andra medlemmar i JSON-fält och inre värden för JSON-objekt kan du använda en fråga som visas i följande exempel:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage och BlobStorage båda\*hänvisar till hela objektet. Men om du har en sökväg i FROM-satsen måste du\*använda BlobStorage[ ].path

<a id="sys-split" />

## <a name="syssplit"></a>Sys.Dela

Detta är en särskild form av SELECT-satsen, som endast är tillgänglig för CSV-formaterade data.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Använd den här uttryckssatsen i fall där du vill hämta och sedan bearbeta CSV-dataposter i batchar. På så sätt kan du bearbeta poster parallellt i stället för att behöva ladda ner alla poster på en gång. Den här policyn returnerar inte poster från CSV-filen. I stället returneras en samling batchstorlekar. Du kan sedan använda varje batchstorlek för att hämta en bunt dataposter. 

Använd parametern *split_size* för att ange hur många byte som du vill att varje batch ska innehålla. Om du till exempel bara vill bearbeta 10 MB data åt gången skulle `SELECT sys.split(10485760)FROM BlobStorage` du se ut så här: eftersom 10 MB är lika med 10 485 760 byte. Varje batch innehåller så många poster som kan passa in i dessa 10 MB. 

I de flesta fall är storleken på varje batch något högre än det tal som du anger. Det beror på att en batch inte kan innehålla en partiell post. Om den sista posten i en batch startar före slutet av tröskelvärdet blir batchen större så att den kan innehålla den fullständiga posten. Storleken på den sista batchen kommer sannolikt att vara mindre än den storlek som du anger.

>[!NOTE]
> Den split_size måste vara minst 10 MB (10485760).

## <a name="see-also"></a>Se även

- [Azure Data Lake Storage-frågeacceleration (förhandsgranskning)](data-lake-storage-query-acceleration.md)
- [Filtrera data med hjälp av Azure Data Lake Storage-frågeacceleration (förhandsgranskning)](data-lake-storage-query-acceleration-how-to.md)

