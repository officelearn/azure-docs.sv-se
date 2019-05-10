---
title: Arbeta med R och SQL-datatyper och objekt
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Lär dig hur du arbetar med datatyper och dataobjekt i R med Azure SQL Database med Machine Learning (förhandsversion), inklusive vanliga problem som kan uppstå.
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
ms.openlocfilehash: 01d3af14963e92393d34a952bddc8097b7b08f18
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232607"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Arbeta med R och SQL-data i Azure SQL Database Machine Learning Services (förhandsversion)

Den här artikeln beskrivs några vanliga problem du kan stöta på när du flyttar data mellan R- och SQL-databas i [Machine Learning Services (med R) i Azure SQL Database](sql-database-machine-learning-services-overview.md). Den guiden får du via den här övningen innehåller viktiga bakgrunden när du arbetar med data i ditt eget skript.

Vanliga problem som kan uppstå är:

- Datatyper ibland stämmer inte överens
- Implicita konverteringar kan äga rum
- CAST och convert är ibland krävs
- R- och SQL använder olika dataobjekt

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

- Du måste ha en Azure SQL database med Machine Learning Services (med R) aktiverad för att köra exempelkoden i den här övningen. Under den offentliga förhandsversionen introducerar Microsoft dig och aktiverar maskininlärning för din befintliga eller nya databas. Följ stegen i [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

- Kontrollera att du har installerat senast [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med hjälp av andra databashantering eller Frågeverktyg, men i den här snabbstarten ska du använda SSMS.

## <a name="working-with-a-data-frame"></a>Arbeta med en dataram

När skriptet returnerar resultat från R till SQL, måste den returnera data som en **data.frame**. Alla andra typer av objekt som du genererar i ditt skript - om de vill ha en lista, faktor, vektor eller binära data - måste konverteras till en dataram om du vill spara den som en del av de lagrade procedurresultaten. Som tur är kan finns det flera R-funktioner till stöd för att ändra andra objekt till en dataram. Du kan även serialisera en binär modell och returnera det i en dataram som senare i den här artikeln.

Först ska vi experimentera med vissa grundläggande R-objekt – vektorer, matriser och -listor - och se hur konvertering till en dataram ändras utdata skickas till SQL.

Jämföra dessa två ”Hello World”-skript i R. Skripten titta nästan identiska, men först returnerar en kolumn av tre värden, medan andra returnerar tre kolumner med ett enda värde var och en.

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

Varför är resultatet innebär?

Svaret kan vanligtvis hittas med hjälp av R `str()` kommando. Lägg till funktion `str(object_name)` var som helst i ditt R-skript för att informationen schemat för det angivna R-objektet returneras som ett informationsmeddelande. Du kan visa meddelanden i den **meddelanden** fliken i SSMS.

Om du vill ta reda på varför exempel 1 och exempel 2 har sådana olika resultat, Infoga rad `str(OutputDataSet)` i slutet av den `@script` variabeldefinitionen i varje policy så här:

**Exempel 1 med str funktion har lagts till**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Exempel 2 med str funktion har lagts till**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Nu läser du igenom texten i **meddelanden** att se varför utdata är olika.

**Resultat – exempel 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Resultat – exempel 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Som du ser hade en liten ändring i R-syntaxen en stor inverkan på schemat för resultaten. All information om skillnaderna i R-datatyper beskrivs i informationen i den *datastrukturer* i avsnittet [”avancerade R” av Hadley Wickham](http://adv-r.had.co.nz).

För tillfället bara Tänk på att du måste kontrollera de förväntade resultaten när coercing R-objekt till dataramar.

> [!TIP]
> Du kan också använda R identity-funktioner, till exempel `is.matrix`, `is.vector`, för att returnera information om den interna datastrukturen.

## <a name="implicit-conversion-of-data-objects"></a>Implicit konvertering av dataobjekt

Varje data R-objekt har sina egna regler för hur värden ska hanteras när de kombineras med andra dataobjekt om de två objekten har samma antal dimensioner, eller om alla dataobjekt innehåller heterogena datatyper.

Anta exempelvis att du vill utföra matris multiplikation med R. Du vill multiplicera en kolumn-matris med de tre värdena med en matris med fyra värden och därmed förvänta dig en 4 x 3-matris.

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

Nu ska du köra följande skript.

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

Under försättsbladen konverteras kolumnen av tre värden till en kolumn-matris. Eftersom en matris är bara ett specialfall i en matris i R, matrisen `y` implicit tvingas till en kolumn matris att göra de två argumenten som följer.

**Results**

|Kol1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Tänk på vad som händer när du ändrar storleken på matrisen `y`.

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

R returnerar nu ett enda värde som ett resultat.

**Results**
    
|Kol1|
|---|
|1542|

Varför då? I det här fallet eftersom två argument kan hanteras som vektorer till samma längd returnerar R inre produkten som en matris.  Detta är förväntat beteende enligt reglerna för linjär algebra. Dock kan det orsaka problem om din nedströms program förväntar sig utdataschema aldrig ändra!

## <a name="merge-or-multiply-columns-of-different-length"></a>Sammanfoga eller multiplicera kolumner med olika längd

R ger stor flexibilitet för att arbeta med vektorer av olika storlekar och för att kombinera dessa strukturer för kolumn-liknande till dataramar. En lista över vektorer kan se ut som en tabell, men de följer inte de regler som styr databastabeller.

Till exempel följande skript definierar en matris med längden 6 och lagrar den i variabeln R `df1`. Numeriska matrisen sedan kombineras med heltal tabellens RTestData (skapade ovan) som innehåller tre (3) värden, för att göra en ny dataram `df2`.

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

Om du vill fylla i fältet data, R upprepas elementen som hämtats från RTestData så många gånger som behövs för att matcha antalet element i matrisen `df1`.

**Results**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Kom ihåg att en dataram endast ser ut som en tabell, men är faktiskt en lista över vektorer.

## <a name="cast-or-convert-sql-data"></a>CAST eller convert SQL-data

R- och SQL använda inte samma datatyper av, så när du kör en fråga i SQL för att hämta data och skicka det till körningsmiljön för r. några typ av implicit konvertering sker vanligtvis. En annan uppsättning konverteringar sker när du returnera data från R till SQL.

- SQL skickar data från frågan till R-processen och konverterar den till en intern representation för större effektivitet.
- Körningsmiljön för r. läser in data i en variabel för data.frame och utför en egen åtgärder på data.
- Databasmotorn returnerar data till SQL med hjälp av en skyddad interna anslutning och visar data i SQL-datatyper.
- Du hämtar data genom att ansluta till SQL med hjälp av ett bibliotek för klient- eller nätverk som kan utfärda SQL-frågor och hantera tabular datauppsättningar. Det här klientprogrammet kan potentiellt påverka data på andra sätt.

Om du vill se hur det fungerar, kör du en fråga som följande på den [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) datalagret. Den här vyn returnerar försäljningsdata som används för att skapa prognoser.

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
> Du kan använda valfri version av AdventureWorks eller skapa en annan fråga med hjälp av en egen databas. Punkten är att försöka att hantera vissa data som innehåller text, datum/tid och numeriska värden.

Prova med hjälp av den här frågan som indata till den lagrade proceduren.

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

Om du får ett felmeddelande, behöver du förmodligen göra vissa ändringar i frågetexten. Till exempel måste sträng-predikat i WHERE-satsen omges av två uppsättningar med enkla citattecken.

När du får frågan fungerar kan du granska resultatet av den `str` funktionen för att se hur R behandlar indata.

**Results**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Datetime-kolumn har bearbetats med datatypen R **POSIXct**.
- Textkolumnen ”ProductSeries” har identifierats som en **faktor**, vilket innebär att en kategoriska variabel. Strängvärden hanteras som faktorer som standard. Om du skickar en sträng till R, den konverteras till ett heltal för intern användning, och sedan mappas till sträng i utdata.

## <a name="summary"></a>Sammanfattning

Från och med de här korta exempel ser du att behöva kontrollera effekterna av datakonvertering när skicka SQL-frågor som indata. Eftersom vissa SQL-datatyper inte stöds av R, Överväg metoder för att undvika fel:

- Testa dina data i förväg och kontrollera kolumner eller värden i ditt schema som kan vara ett problem när skickades till R-kod.
- Ange kolumner i datakällan inkommande separat, i stället för `SELECT *`, och veta hur varje kolumn hanteras.
- Utför explicit sändningar efter behov när du förbereder dina indata för att undvika överraskningar.
- Undvik att skicka kolumner med data (till exempel GUID eller rowguids) som orsakar fel och inte är användbart för modellering.

Mer information om datatyper som stöds respektive R finns [R-bibliotek och datatyper](/sql/advanced-analytics/r/r-libraries-and-data-types).
