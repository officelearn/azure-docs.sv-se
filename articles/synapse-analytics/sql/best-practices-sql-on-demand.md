---
title: Metod tips för SQL på begäran (för hands version) i Azure Synapse Analytics
description: Rekommendationer och metod tips du bör känna till när du arbetar med SQL på begäran (för hands version).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a1a33404982b16e458e97aaf9959ff5dd52d1cce
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198886"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Metod tips för SQL på begäran (för hands version) i Azure Synapse Analytics

I den här artikeln hittar du en samling bästa metoder för att använda SQL på begäran (för hands version). SQL på begäran är en ytterligare resurs i Azure Synapse Analytics.

## <a name="general-considerations"></a>Generella saker att tänka på

Med SQL på begäran kan du söka efter filer i dina Azure Storage-konton. Den har inte funktioner för lokal lagring eller inmatning. Därför är alla filer som fråge målen är externa för SQL på begäran. Allt som rör läsning av filer från lagring kan påverka frågans prestanda.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Samplacera Azure Storage konto och SQL på begäran

Du kan minimera svars tiden genom att samplacera ditt Azure Storage-konto och din SQL-slutpunkt på begäran. Lagrings konton och slut punkter som tillhandahålls när arbets ytan skapas befinner sig i samma region.

För optimala prestanda bör du kontrol lera att de finns i samma region om du har åtkomst till andra lagrings konton med SQL på begäran. Om de inte finns i samma region ökar svars tiden för data överföring mellan den fjärranslutna och slut punktens regioner.

## <a name="azure-storage-throttling"></a>Azure Storage begränsning

Flera program och tjänster kan komma åt ditt lagrings konto. Lagrings begränsning sker när den sammanlagda IOPS eller data flödet som genereras av program, tjänster och SQL på begäran-arbetsbelastning överskrider lagrings kontots gränser. Därför får du en betydande negativ effekt på frågans prestanda.

När begränsningen har identifierats har SQL på begäran inbyggd hantering av det här scenariot. SQL på begäran kommer att göra begär anden till lagringen i en långsammare takt tills begränsningen har lösts.

> [!TIP]
> För optimal frågekörning bör du inte stressa lagrings kontot med andra arbets belastningar under frågekörningen.

## <a name="prepare-files-for-querying"></a>Förbered filer för frågor

Om möjligt kan du förbereda filer för bättre prestanda:

- Konvertera CSV och JSON till Parquet-Parquet är kolumn format. Eftersom den är komprimerad är fil storleken mindre än CSV-eller JSON-filer med samma data. SQL på begäran behöver mindre tids-och lagrings begär Anden för att kunna läsa det.
- Om en fråga är riktad mot en enda stor fil, drar du nytta av att dela upp den i flera mindre filer.
- Försök att behålla storleken på CSV-filen under 10 GB.
- Det är bättre att ha lika stora filer för en enskild OpenRowSet-sökväg eller en extern tabell plats.
- Partitionera dina data genom att lagra partitioner i olika mappar eller fil namn – kontrol lera [Använd fil namn och fil Sök väg funktioner för att ange specifika partitioner](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Jokertecken för push-meddelanden till lägre nivåer i sökvägen

Du kan använda jokertecken i din sökväg för att [fråga flera filer och mappar](develop-storage-files-overview.md#query-multiple-files-or-folders). SQL på begäran visar en lista över filer på ditt lagrings konto som börjar från första * med Storage API och eliminerar filer som inte matchar den angivna sökvägen. Att minska den inledande listan över filer kan förbättra prestanda om det finns många filer som matchar den angivna sökvägen upp till det första jokertecknet.

## <a name="use-appropriate-data-types"></a>Använd lämpliga data typer

Data typer som används i frågan påverkar prestanda. Du kan få bättre prestanda om du: 

- Använd den minsta data storlek som ska hantera det största möjliga värdet.
  - Om max längden för tecken värde är 30 tecken, använder du tecken data typen 30.
  - Om alla tecken kolumn värden har fast storlek använder du char eller nchar. Annars använder du varchar eller nvarchar.
  - Om värdet för högsta heltals kolumn är 500 använder du smallint eftersom det är den minsta data typen som kan hantera det här värdet. Du kan hitta data typs intervall för heltal [här](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15).
- Använd om möjligt VARCHAR och Char i stället för nvarchar och nchar.
- Använd Integer-baserade data typer om möjligt. Sorterings-, Join-och Group by-åtgärder utförs snabbare på heltal än på tecken data.
- Om du använder schema härledning, [kontrol lera data typen härledd](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Kontrol lera härledda data typer

[Schema härledning](query-parquet-files.md#automatic-schema-inference) hjälper dig att snabbt skriva frågor och utforska data utan att känna till filschemat. Den här bekvämligheten är till för kostnad av härledda data typer som är större än de faktiskt är. Det inträffar när det inte finns tillräckligt med information i källfilerna för att säkerställa att lämplig datatyp används. Parquet-filer innehåller till exempel inte metadata om maximal tecken kolumn längd och SQL on-demand härleds som varchar (8000). 

Du kan kontrol lera resulterande data typer i din fråga med hjälp av [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15).

I följande exempel visas hur du kan optimera härledda data typer. Proceduren används för att Visa härledda data typer. 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Här är resultatuppsättningen.

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2 (7)|8|
|0|3|passenger_count|int|4|

När vi känner till härledda data typer för frågor kan vi ange lämpliga data typer:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Använda fileinfo-och fil Sök vägar för att fokusera på specifika partitioner

Data är ofta ordnade i partitioner. Du kan instruera SQL på begäran att fråga specifika mappar och filer. Funktionen kommer att minska antalet filer och mängden data som frågan behöver läsa och bearbeta. En extra bonus är att du får bättre prestanda.

Mer information finns i funktioner för [fil namn](develop-storage-files-overview.md#filename-function) och fil [Sök väg](develop-storage-files-overview.md#filepath-function) och exempel på hur du [frågar efter vissa filer](query-specific-files.md).

> [!TIP]
> Omvandla alltid resultatet av fil Sök väg och fileinfo-funktioner till lämpliga data typer. Om du använder tecken data typer ser du till att lämplig längd används.

> [!NOTE]
> Funktioner som används för partitions Eli minering, sökväg och fileinfo stöds för närvarande inte för andra externa tabeller än de som skapats automatiskt för varje tabell som skapats i Synapse Spark.

Om dina lagrade data inte är partitionerade bör du överväga att partitionera dem så att du kan använda dessa funktioner för att optimera frågor som riktar sig mot dessa filer. När du [frågar partitionerade Spark-tabeller](develop-storage-files-spark-tables.md) från SQL på begäran, kommer frågan automatiskt att rikta in sig på de filer som behövs.

## <a name="use-parser_version-20-for-querying-csv-files"></a>Använd PARSER_VERSION 2,0 för att fråga CSV-filer

Du kan använda prestanda optimerad parser när du frågar CSV-filer. Mer information finns i [PARSER_VERSION](develop-openrowset.md) .

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Använd CETAS för att förbättra frågornas prestanda och kopplingar

[CETAS](develop-tables-cetas.md) är en av de viktigaste funktionerna som är tillgängliga i SQL på begäran. CETAS är en parallell åtgärd som skapar externa tabellens metadata och exporterar URVALs resultatet till en uppsättning filer i ditt lagrings konto.

Du kan använda CETAS för att lagra ofta använda delar av frågor som kopplade referens tabeller till en ny uppsättning filer. Sedan kan du koppla till den här enskilda externa tabellen i stället för att upprepa vanliga kopplingar i flera frågor.

När CETAS genererar Parquet-filer skapas statistik automatiskt när den första frågan riktar sig till den externa tabellen, vilket resulterar i förbättrade prestanda.

## <a name="aad-pass-through-performance"></a>Prestanda för AAD-vidarekoppling

Med SQL på begäran kan du komma åt filer i lagring med AAD-direktautentisering eller SAS-autentiseringsuppgifter. Du kan uppleva sämre prestanda med AAD genom strömnings jämförelse till SAS. 

Om du behöver bättre prestanda kan du prova SAS-autentiseringsuppgifter för att få åtkomst till lagring tills AAD-vidarekoppling har förbättrats.

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [fel sökning](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för vanliga problem och lösningar. Om du arbetar med SQL-pool i stället för SQL på begäran, kan du läsa mer i artikeln [metod tips för SQL-pooler](best-practices-sql-pool.md) .
