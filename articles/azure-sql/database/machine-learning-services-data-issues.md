---
title: Arbeta med R-och SQL-datatyper och-objekt
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Lär dig hur du arbetar med data typer och data objekt i R med Azure SQL Database att använda Machine Learning Services (för hands version), inklusive vanliga problem som kan uppstå.
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
ms.openlocfilehash: f784d6ef56ad5cb800c0061fbb5d0d4ca3252fa0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048233"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Arbeta med R-och SQL-data i Azure SQL Database Machine Learning Services (för hands version)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln beskrivs några vanliga problem som kan uppstå när du flyttar data mellan R och SQL Database i [Machine Learning Services (med R) i Azure SQL Database](machine-learning-services-overview.md). Den upplevelse du får i den här övningen ger en viktig bakgrund när du arbetar med data i ditt eget skript.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Vanliga problem som kan uppstå är:

- Data typerna matchar ibland inte
- Implicita konverteringar kan äga rum
- Cast-och Convert-åtgärder krävs ibland
- R och SQL använder olika data objekt

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

- Om du vill köra exempel koden i de här övningarna måste du först ha [Azure SQL Database med Machine Learning Services (med R)](machine-learning-services-overview.md) aktiverat.

- Kontrol lera att du har installerat den senaste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med hjälp av andra databas hanterings-eller specialverktyg, men i den här snabb starten ska du använda SSMS.

## <a name="working-with-a-data-frame"></a>Arbeta med en data ram

När skriptet returnerar resultat från R till SQL måste det returnera data som **data. Frame**. En annan typ av objekt som du skapar i skriptet – oavsett om det är en lista, en faktor, en Vector eller binära data – måste konverteras till en data ram om du vill att den ska visas som en del av de lagrade procedur resultaten. Lyckligt vis finns det flera R-funktioner som stöder ändring av andra objekt till en data ram. Du kan till och med serialisera en binär modell och returnera den i en data ram, vilket du gör senare i den här artikeln.

Först ska vi experimentera med några grundläggande R-objekt – vektorer, matriser och listor – och se hur konverteringen till en data ram ändrar utdata som skickas till SQL.

Jämför dessa två "Hello World"-skript i R. Skripten ser nästan identiska ut, men det första returnerar en enda kolumn med tre värden, medan den andra returnerar tre kolumner med ett enda värde.

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

Varför är resultatet annorlunda?

Det går vanligt vis att hitta svaret med hjälp av R- `str()` kommandot. Lägg till funktionen `str(object_name)` var som helst i R-skriptet om du vill att data schemat för det angivna R-objektet ska returneras som ett informations meddelande. Du kan visa meddelandena på fliken **meddelanden** i SSMS.

För att ta reda på varför exempel 1 och exempel 2 har sådana olika resultat, infogar du linjen i `str(OutputDataSet)` slutet av `@script` variabel definitionen i varje instruktion, så här:

**Exempel 1 med funktionen Str tillagd**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Exempel 2 med funktionen Str tillagd**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Granska nu texten i **meddelanden** för att se varför utdata är olika.

**Resultat – exempel 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Resultat – exempel 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Som du kan se har en liten ändring i R-syntaxen en stor inverkan på schemat för resultaten. För all information beskrivs skillnaderna i R-datatyper i detalj i avsnittet *data strukturer* i ["Advanced R" av Hadley Wickham](http://adv-r.had.co.nz).

För närvarande är det bara viktigt att du behöver kontrol lera de förväntade resultaten när du tvingar R-objekt till data ramar.

> [!TIP]
> Du kan också använda R Identity-funktioner, till exempel, `is.matrix` `is.vector` för att returnera information om den interna data strukturen.

## <a name="implicit-conversion-of-data-objects"></a>Implicit konvertering av data objekt

Varje R-dataobjekt har egna regler för hur värden hanteras när de kombineras med andra data objekt om de två data objekten har samma antal dimensioner, eller om ett data objekt innehåller heterogena data typer.

Anta till exempel att du vill utföra mat ris multiplikation med R. Du vill multiplicera en matris med en kolumn med de tre värdena genom en matris med fyra värden och förväntar sig en 4x3-matris som ett resultat.

Skapa först en liten tabell med test data.

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

Under försättsblad konverteras kolumnen med tre värden till en matris med en kolumn. Eftersom en matris bara är ett specialfall av en matris i R, `y` tvingas matrisen implicit till en matris med en kolumn som gör att de två argumenten stämmer överens.

**Gör**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Observera dock vad som händer när du ändrar storlek på matrisen `y` .

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

Nu returnerar R ett enda värde som resultatet.

**Gör**
    
|Col1|
|---|
|1542|

Varför det? I det här fallet, eftersom de två argumenten kan hanteras som vektorer av samma längd, returnerar R den inre produkten som en matris.  Detta är det förväntade beteendet enligt reglerna för linjära algebra. Det kan dock orsaka problem om det underordnade programmet förväntar sig att schemat inte ändras!

## <a name="merge-or-multiply-columns-of-different-length"></a>Sammanfoga eller multiplicera kolumner med olika längd

R ger stor flexibilitet för att arbeta med vektorer med olika storlekar och för att kombinera dessa kolumnbaserade strukturer i data ramar. Listor över vektorer kan se ut som en tabell, men de följer inte alla regler som styr databas tabeller.

Följande skript definierar till exempel en numerisk matris med längden 6 och lagrar den i R-variabeln `df1` . Den numeriska matrisen kombineras sedan med heltalen i tabellen RTestData (som skapats ovan) som innehåller tre (3) värden, för att skapa en ny data ram `df2` .

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

För att fylla i data ramen upprepar R de element som hämtats från RTestData så många gånger som behövs för att matcha antalet element i matrisen `df1` .

**Gör**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Kom ihåg att en data ram bara ser ut som en tabell, men är i själva verket en lista över vektorer.

## <a name="cast-or-convert-sql-data"></a>Omvandla eller konvertera SQL-data

R och SQL använder inte samma data typer, så när du kör en fråga i SQL för att hämta data och sedan skickar den till R-körningsmiljön sker en viss typ av implicit konvertering vanligt vis. En annan uppsättning konverteringar äger rum när du returnerar data från R till SQL.

- SQL push-överför data från frågan till R-processen och konverterar dem till en intern representation för bättre effektivitet.
- R-körningen läser in data i en data. Frame-variabel och utför egna åtgärder för data.
- Databas motorn returnerar data till SQL med en skyddad intern anslutning och visar data i termer av SQL-datatyper.
- Du får data genom att ansluta till SQL med en klient eller ett nätverks bibliotek som kan utfärda SQL-frågor och hantera tabell data uppsättningar. Det här klient programmet kan potentiellt påverka data på andra sätt.

Du kan se hur det fungerar genom att köra en fråga som den här i informations lagret för [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) . Den här vyn returnerar försäljnings data som används för att skapa prognoser.

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
> Du kan använda vilken version som helst av AdventureWorks eller skapa en annan fråga med en egen databas. Punkten är att försöka hantera vissa data som innehåller text-, DateTime-och numeriska värden.

Prova nu att använda den här frågan som indatamängd för den lagrade proceduren.

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

Om du får ett fel meddelande måste du förmodligen göra vissa ändringar i frågetexten. Till exempel måste String-predikatet i WHERE-satsen omges av två uppsättningar enkla citat tecken.

När du har bearbetat frågan kan du granska resultatet av `str` funktionen och se hur R behandlar indata.

**Gör**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Datetime-kolumnen har bearbetats med data typen R, **POSIXct**.
- Text kolumnen "ProductSeries" har identifierats som en **faktor**, vilket innebär en kategoriska-variabel. Sträng värden hanteras som standard faktorer. Om du skickar en sträng till R konverteras den till ett heltal för intern användning och mappas sedan tillbaka till strängen vid utdata.

## <a name="summary"></a>Sammanfattning

Från och med de här korta exemplen kan du se att du behöver kontrol lera effekterna av data konverteringen när du skickar SQL-frågor som indata. Eftersom vissa SQL-datatyper inte stöds av R kan du tänka på följande sätt för att undvika fel:

- Testa dina data i förväg och kontrol lera att kolumner eller värden i schemat kan vara ett problem när de skickas till R-koden.
- Ange kolumner i din indata-datakälla individuellt, i stället för `SELECT *` att använda, och se hur varje kolumn ska hanteras.
- Utför explicita sändningar vid behov när du förbereder indata för att undvika överraskningar.
- Undvik att skicka data kolumner (t. ex. GUID eller ROWGUIDS) som orsakar fel och inte är användbara för modellering.

Mer information om vilka typer av R-data som stöds och som inte stöds finns i [r-bibliotek och data typer](/sql/advanced-analytics/r/r-libraries-and-data-types).
