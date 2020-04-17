---
title: Arbeta med R- och SQL-datatyper och objekt
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Lär dig hur du arbetar med datatyper och dataobjekt i R med Azure SQL Database med Machine Learning Services (förhandsversion), inklusive vanliga problem som kan uppstå.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e81cca3e20d5b6c050489e80b91d013d5e934cce
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453207"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Arbeta med R- och SQL-data i Azure SQL Database Machine Learning Services (förhandsversion)

I den hÃ¤r artikeln beskrivs några av de vanligaste problemen som uppstÃ¤r uppstÃ¤nsmål fÃ¤r data flyttas data mellan R och SQL Database i [Machine Learning Services (med R) i Azure SQL Database](sql-database-machine-learning-services-overview.md). Den erfarenhet du får genom den här övningen ger viktig bakgrund när du arbetar med data i ditt eget skript.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Vanliga problem som du kan stöta på är:

- Datatyper matchar ibland inte
- Implicita konverteringar kan ske
- Cast- och konverteringsåtgärder krävs ibland
- R och SQL använder olika dataobjekt

## <a name="prerequisites"></a>Krav

- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

- Om du vill köra exempelkoden i dessa övningar måste du först ha [Azure SQL Database med Machine Learning Services (med R)](sql-database-machine-learning-services-overview.md) aktiverat.

- Kontrollera att du har installerat den senaste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med andra databashanterings- eller frågeverktyg, men i den här snabbstarten använder du SSMS.

## <a name="working-with-a-data-frame"></a>Arbeta med en dataram

När skriptet returnerar resultat från R till SQL måste det returnera data som en **data.frame**. Alla andra typer av objekt som du genererar i skriptet - oavsett om det är en lista, faktor, vektor eller binära data - måste konverteras till en dataram om du vill mata ut den som en del av de lagrade procedureresultaten. Lyckligtvis finns det flera R-funktioner som stöder att ändra andra objekt till en dataram. Du kan även serialisera en binär modell och returnera den i en dataram, vilket du ska göra senare i den här artikeln.

Låt oss först experimentera med några grundläggande R-objekt - vektorer, matriser och listor - och se hur konvertering till en dataram ändrar utdata som skickas till SQL.

Jämför dessa två "Hello World" skript i R. Skripten ser nästan identiska ut, men den första returnerar en enda kolumn med tre värden, medan den andra returnerar tre kolumner med ett enda värde vardera.

**Exempel 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Exempel 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Varför är resultaten så olika?

Svaret kan vanligtvis hittas med `str()` kommandot R. Lägg till `str(object_name)` funktionen var som helst i R-skriptet om du vill att dataschemat för det angivna R-objektet ska returneras som ett informationsmeddelande. Du kan visa meddelandena på fliken **Meddelanden** i SSMS.

Om du vill ta reda på varför exempel 1 `str(OutputDataSet)` och exempel 2 har så olika resultat infogar du raden i slutet av variabeldefinitionen `@script` i varje sats, så här:

**Exempel 1 med str-funktion tillagd**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Exempel 2 med str-funktion tillagd**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Nu, granska texten i **Meddelanden** för att se varför utdata är annorlunda.

**Resultat - Exempel 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Resultat - Exempel 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Som du kan se, en liten förändring i R syntax hade en stor effekt på schemat för resultaten. För alla detaljer förklaras skillnaderna i R-datatyper i detaljer i avsnittet *Datastrukturer* i ["Advanced R" av Hadley Wickham](http://adv-r.had.co.nz).

För nu, tänk bara på att du måste kontrollera de förväntade resultaten när tvinga R-objekt i dataramar.

> [!TIP]
> Du kan också använda R-identitetsfunktioner, till exempel `is.matrix`, `is.vector`för att returnera information om den interna datastrukturen.

## <a name="implicit-conversion-of-data-objects"></a>Implicit konvertering av dataobjekt

Varje R-dataobjekt har sina egna regler för hur värden hanteras i kombination med andra dataobjekt om de två dataobjekten har samma antal dimensioner, eller om något dataobjekt innehåller heterogena datatyper.

Anta till exempel att du vill utföra matrismultsexplikation med R. Du vill multiplicera en matris med en kolumn med de tre värdena med en matris med fyra värden och förvänta dig en matris med 4x3 som följd.

Skapa först en liten tabell med testdata.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Kör nu följande skript.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

Under skalorna konverteras kolumnen med tre värden till en matris med en kolumn. Eftersom en matris bara är ett specialfall för `y` en matris i R tvingas matrisen implicit till en matris med en kolumn för att få de två argumenten att överensstämma.

**Resultat**

|Col1|Col2|Col3 (på andra)|Col4 (på andra)|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Observera dock vad som händer när du `y`ändrar storleken på matrisen .

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Nu returnerar R ett enda värde som resultat.

**Resultat**
    
|Col1|
|---|
|1542|

Varför det? I det här fallet, eftersom de två argumenten kan hanteras som vektorer av samma längd, returnerar R den inre produkten som en matris.  Detta är det förväntade beteendet enligt reglerna för linjär algebra. Det kan dock orsaka problem om ditt nedströmsprogram förväntar sig att utdataschemat aldrig ska ändras!

## <a name="merge-or-multiply-columns-of-different-length"></a>Sammanfoga eller multiplicera kolumner av olika längd

R ger stor flexibilitet för att arbeta med vektorer av olika storlekar och för att kombinera dessa kolumnliknande strukturer i dataramar. Listor över vektorer kan se ut som en tabell, men de följer inte alla regler som styr databastabeller.

Följande skript definierar till exempel en numerisk matris med längd 6 `df1`och lagrar den i R-variabeln . Den numeriska matrisen kombineras sedan med heltalen i tabellen RTestData (skapad ovan) som innehåller tre `df2`(3) värden för att skapa en ny dataram.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Om du vill fylla i dataramen upprepar R de element som hämtats från RTestData `df1`så många gånger som behövs för att matcha antalet element i matrisen .

**Resultat**
    
|*Col2*|*Col3 (på andra)*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Kom ihåg att en dataram bara ser ut som en tabell, men faktiskt är en lista över vektorer.

## <a name="cast-or-convert-sql-data"></a>Casta eller konvertera SQL-data

R och SQL använder inte samma datatyper, så när du kör en fråga i SQL för att hämta data och sedan skicka det till R-körningen sker vanligtvis någon typ av implicit konvertering. En annan uppsättning konverteringar sker när du returnerar data från R till SQL.

- SQL skickar data från frågan till R-processen och konverterar dem till en intern representation för större effektivitet.
- R-körningen läser in data i en data.frame-variabel och utför egna åtgärder på data.
- Databasmotorn returnerar data till SQL med hjälp av en säker intern anslutning och presenterar data i termer av SQL-datatyper.
- Du hämtar data genom att ansluta till SQL med hjälp av en klient eller ett nätverksbibliotek som kan utfärda SQL-frågor och hantera tabelldatauppsättningar. Det här klientprogrammet kan potentiellt påverka data på andra sätt.

Om du vill se hur det fungerar kör du en fråga som den här på [affärslagret AdventureWorksDW.](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) Den här vyn returnerar försäljningsdata som används för att skapa prognoser.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Du kan använda vilken version som helst av AdventureWorks eller skapa en annan fråga med hjälp av en egen databas. Poängen är att försöka hantera vissa data som innehåller text, datetime och numeriska värden.

Försök nu att använda den här frågan som indata till den lagrade proceduren.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Om du får ett felmeddelande måste du förmodligen göra vissa ändringar i frågetexten. Strängen predikat i WHERE-satsen måste till exempel omges av två uppsättningar med enkla citattecken.

När du har fått frågan att `str` fungera, granska resultatet av funktionen för att se hur R behandlar indata.

**Resultat**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Kolumnen datetime har bearbetats med datatypen R, **POSIXct**.
- Textkolumnen "ProductSeries" har identifierats som en **faktor**, vilket innebär en kategorisk variabel. Strängvärden hanteras som faktorer som standard. Om du skickar en sträng till R konverteras den till ett heltal för internt bruk och mappas sedan tillbaka till strängen vid utdata.

## <a name="summary"></a>Sammanfattning

Från även dessa korta exempel kan du se behovet av att kontrollera effekterna av datakonvertering när du skickar SQL-frågor som indata. Eftersom vissa SQL-datatyper inte stöds av R bör du överväga följande sätt att undvika fel:

- Testa dina data i förväg och verifiera kolumner eller värden i schemat som kan vara ett problem när de skickas till R-kod.
- Ange kolumner i indatakällan individuellt, i stället för att använda `SELECT *`, och vet hur varje kolumn ska hanteras.
- Utför explicita kast efter behov när du förbereder dina indata, för att undvika överraskningar.
- Undvik att skicka kolumner med data (till exempel GUIDS eller rowguids) som orsakar fel och inte är användbara för modellering.

Mer information om R-datatyper som stöds och inte stöds finns i [R-bibliotek och datatyper](/sql/advanced-analytics/r/r-libraries-and-data-types).
