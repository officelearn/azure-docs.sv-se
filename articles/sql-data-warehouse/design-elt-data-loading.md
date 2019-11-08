---
title: Utforma ELT i stället för ETL
description: I stället för ETL kan du utforma en process för att extrahera, läsa in och transformera (ELT) för att läsa in data eller Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 11/07/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9220bf0cf94eaae6ddc945e83deac2a6041158d2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748522"
---
# <a name="data-loading-strategies-for-azure-sql-data-warehouse"></a>Data inläsnings strategier för Azure SQL Data Warehouse

Traditionella SMP-datalager använder en process för extrahering, transformering och inläsning (ETL) för att läsa in data. Azure SQL Data Warehouse är en arkitektur för massivt parallell bearbetning (MPP) som drar nytta av skalbarheten och flexibiliteten i beräknings-och lagrings resurser. Användning av en process för att extrahera, läsa in och transformera (ELT) kan dra nytta av MPP och eliminera resurser som behövs för att transformera data innan de läses in. Även om SQL Data Warehouse stöder många inläsnings metoder, inklusive populära SQL Server alternativ som BCP och SQL BulkCopy API, är det snabbaste och mest skalbara sättet att läsa in data via PolyBase-externa tabeller och [kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (för hands version).  Med PolyBase och KOPIERINGs instruktionen kan du komma åt externa data som lagras i Azure Blob Storage eller Azure Data Lake Store via T-SQL-språket. Vi rekommenderar att du använder COPY-instruktionen för att få störst flexibilitet vid inläsning till SQL Data Warehouse. 

> [!NOTE]  
> KOPIERINGs instruktionen är för närvarande en offentlig för hands version. Skicka e-post till följande distributions lista för att ge feedback: sqldwcopypreview@service.microsoft.com.
>
        
 
> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Vad är ELT?

Extrahera, läsa in och transformera (ELT) är en process som data extraheras från ett käll system, läses in i ett data lager och sedan omvandlas. 

De grundläggande stegen för att implementera ELT för SQL Data Warehouse är:

1. Extrahera källdata till textfiler.
2. Landa data i Azure Blob Storage eller Azure Data Lake Store.
3. Förbered data för inläsning.
4. Läs in data i SQL Data Warehouse mellanlagrings tabeller med PolyBase eller kommandot COPY. 
5. Transformera data.
6. Infoga data i produktions tabeller.


En inläsnings kurs för PolyBase finns i [använda PolyBase för att läsa in data från Azure Blob Storage till Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Mer information finns i [blogg om inläsnings mönster](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. extrahera källdata i textfiler

Att hämta data från käll systemet beror på lagrings platsen.  Målet är att flytta data till PolyBase och KOPIAn som stöder avgränsade text-eller CSV-filer. 

### <a name="polybase-and-copy-external-file-formats"></a>PolyBase och kopiera externa fil format

Med PolyBase och KOPIERINGs instruktionen kan du läsa in data från UTF-8-och UTF-16-kodade avgränsade text-eller CSV-filer. Förutom avgränsade text-eller CSV-filer läses de in från Hadoop-filformat som ORC och Parquet. PolyBase och KOPIERINGs instruktionen kan också läsa in data från gzip och fästa komprimerade filer. Utökad ASCII, fast bredds format och kapslade format som WinZip eller XML stöds inte. Om du exporterar från SQL Server kan du använda [kommando rads verktyget BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest) för att exportera data till avgränsade textfiler. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. landa data i Azure Blob Storage eller Azure Data Lake Store

Om du vill landa data i Azure Storage kan du flytta dem till [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) eller [Azure Data Lake Store Gen2](../data-lake-store/data-lake-store-overview.md). På någon av platserna ska data lagras i textfiler. PolyBase och KOPIERINGs instruktionen kan läsas in från vilken plats som helst.

Verktyg och tjänster som du kan använda för att flytta data till Azure Storage:

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) service förbättrar nätverks data flöde, prestanda och förutsägbarhet. ExpressRoute är en tjänst som dirigerar dina data via en dedikerad privat anslutning till Azure. ExpressRoute-anslutningar dirigerar inte data via det offentliga Internet. Anslutningarna ger högre tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via det offentliga Internet.
- [AzCopy-verktyget](../storage/common/storage-moving-data.md) flyttar data till Azure Storage över det offentliga Internet. Detta fungerar om data storlekarna är mindre än 10 TB. Om du vill utföra belastningen regelbundet med AZCopy testar du nätverks hastigheten för att se om den är acceptabel. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) har en gateway som du kan installera på den lokala servern. Sedan kan du skapa en pipeline för att flytta data från din lokala server upp till Azure Storage. Om du vill använda Data Factory med SQL Data Warehouse, se [Läs in data i SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Förbered data för inläsning

Du kan behöva förbereda och rensa data i ditt lagrings konto innan du läser in det i SQL Data Warehouse. Data förberedelse kan utföras medan dina data är i källan, när du exporterar data till textfiler, eller när data har Azure Storage.  Det är enklast att arbeta med data så tidigt i processen som möjligt.  

### <a name="define-external-tables"></a>Definiera externa tabeller

Om du använder PolyBase måste du definiera externa tabeller i ditt informations lager innan du läser in. Externa tabeller krävs inte av KOPIERINGs instruktionen. PolyBase använder externa tabeller för att definiera och komma åt data i Azure Storage. En extern tabell liknar en Database-vy. Den externa tabellen innehåller tabellens schema och pekar på data som lagras utanför data lagret. 

Definiera externa tabeller innebär att du anger data källan, formatet på textfilerna och tabell definitionerna. Avsnitt om T-SQL-syntax som du behöver:
- [SKAPA EXTERN DATA KÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

Vid inläsning av Parquet är data typs mappningen med SQL DW:

| **Data typen Parquet** |                      **SQL-datatyp**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolesk        |                             bitmask                              |
|        double         |                            flyt                             |
|         flyt         |                             verkligen                             |
|        double         |                            mynt                             |
|        double         |                          smallmoney                          |
|        sträng         |                            nchar                             |
|        sträng         |                           nvarchar                           |
|        sträng         |                             hängande                             |
|        sträng         |                           varchar                            |
|        binär         |                            binär                            |
|        binär         |                          varbinary                           |
|       tidsstämpel       |                             datum                             |
|       tidsstämpel       |                        datatyp                         |
|       tidsstämpel       |                          datetime2                           |
|       tidsstämpel       |                           datetime                           |
|       tidsstämpel       |                             time                             |
|       datum            |                             datum                             |
|        decimal        |                            decimal                           |

Ett exempel på hur du skapar externa objekt finns i steget [skapa externa tabeller](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) i inläsnings kursen.

### <a name="format-text-files"></a>Formatera textfiler

Om du använder PolyBase måste de externa objekt som definieras justera raderna i textfilerna med den externa tabell-och fil formats definitionen. Data i varje rad i text filen måste överensstämma med tabell definitionen.
Formatera textfilerna:

- Om dina data kommer från en icke-relationell källa måste du omvandla den till rader och kolumner. Oavsett om data kommer från en Relations källa eller icke-relationell källa måste data omvandlas för att anpassas efter kolumn definitionerna för den tabell som du planerar att läsa in data i. 
- Formatera data i text filen så att de överensstämmer med kolumnerna och data typerna i SQL Data Warehouse mål tabellen. Fel justering mellan data typer i externa textfiler och data lager tabellen gör att rader avvisas under belastningen.
- Separera fält i text filen med en avslutning.  Se till att du använder ett Character eller en teckensekvens som inte finns i dina källdata. Använd den avslutnings fil som du angav med [Skapa externt fil format](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase-or-the-copy-statement"></a>4. Läs in data i SQL Data Warehouse mellanlagrings tabeller med PolyBase eller KOPIERINGs instruktionen

Vi rekommenderar att du läser in data i en mellanlagringsplats. Med mellanlagrings tabeller kan du hantera fel utan att störa produktions tabellerna. En mellanlagringsplats ger dig också möjlighet att använda SQL Data Warehouse MPP för data transformationer innan du infogar data i produktions tabeller. Tabellen måste skapas i förväg när den läses in i en mellanlagringsplats med COPY.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Alternativ för att läsa in med PolyBase-och COPY-instruktion

Om du vill läsa in data med PolyBase kan du använda något av följande inläsnings alternativ:

- [PolyBase med T-SQL](load-data-from-azure-blob-storage-using-polybase.md) fungerar bra när dina data finns i Azure Blob storage eller Azure Data Lake Store. Du får störst kontroll över inläsnings processen, men du måste också definiera externa data objekt. De andra metoderna definierar dessa objekt bakom scenerna när du mappar käll tabeller till mål tabeller.  För att dirigera T-SQL-inläsningar kan du använda Azure Data Factory, SSIS eller Azure Functions. 
- [PolyBase med SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) fungerar bra när dina källdata är i SQL Server, antingen SQL Server lokalt eller i molnet. SSIS definierar källa till mål tabell mappningar och dirigerar även belastningen. Om du redan har SSIS-paket kan du ändra paketen så att de fungerar med det nya informations lager målet. 
- [PolyBase-och Copy-uttryck med Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) är ett annat Orchestration-verktyg.  Den definierar en pipeline och schemalägger jobb. 
- [PolyBase med Azure Databricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) överför data från en SQL Data Warehouse tabell till en Databricks-dataframe och/eller skriver data från en Databricks-dataframe till en SQL Data Warehouse tabell med PolyBase.

### <a name="other-loading-options"></a>Andra inläsnings alternativ

Förutom PolyBase och KOPIERINGs instruktionen kan du använda [BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest) -eller SQLBulkCopy- [API: et](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP läser in direkt till SQL Data Warehouse utan att gå via Azure Blob Storage, och är endast avsett för små belastningar. Obs! belastnings prestandan för de här alternativen är långsammare än PolyBase och KOPIERINGs instruktionen. 


## <a name="5-transform-the-data"></a>5. transformera data

När data finns i mellanlagringsplatsen ska du utföra omvandlingar som din arbets belastning kräver. Flytta sedan data till en produktions tabell.


## <a name="6-insert-the-data-into-production-tables"></a>6. infoga data i produktions tabeller

INSERT INTO... SELECT-instruktionen flyttar data från mellanlagrings tabellen till den permanenta tabellen. 

När du utformar en ETL-process kan du prova att köra processen på ett litet test exempel. Försök att extrahera 1000 rader från tabellen till en fil, flytta den till Azure och försök sedan att läsa in den i en mellanlagringsplats. 


## <a name="partner-loading-solutions"></a>Lösningar för partner inläsning

Många av våra partner har inläsnings lösningar. Om du vill veta mer kan du se en lista över våra [lösnings partner](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Nästa steg

Information om hur du läser in vägledning finns i [rikt linjer för att läsa in data](guidance-for-loading-data.md).


