---
title: I stället för ETL, utforma ELT för Azure SQL Data Warehouse | Microsoft Docs
description: I stället för ETL, utforma en extrahera, Load and Transform ELT ()-processen för inläsning av data eller Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: jhubbard
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 03/28/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: c27ad843c9ee9beed871dcc03254cb1266f6ebe2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Designa extrahera, Load and Transform (ELT) för Azure SQL Data Warehouse

I stället för extrahering, transformering och inläsning (ETL) att utforma en extrahera, Load and Transform ELT ()-process för att läsa in data i Azure SQL Data Warehouse. Den här artikeln introducerar sätt att utforma en ELT process som flyttar data till ett Azure data warehouse.

## <a name="what-is-elt"></a>Vad är ELT?

Extrahera belastning och transformera ELT () är en process där data flyttas från ett källsystem till en mål-datalagret. Den här processen utförs regelbundet, till exempel varje timme eller varje dag, för att få nyligen genereras data till datalagret. Det bästa sättet att hämta data från källan till data warehouse är att utveckla en ELT process som använder PolyBase för att läsa in data till SQL Data Warehouse.

ELT läser in först och sedan omvandlar data, medan extrahering, transformering och inläsning (ETL) överför informationen innan du läser in den. Utför ELT i stället för ETL sparar kostnaden för att tillhandahålla resurser för att omvandla data innan den har lästs in. När du använder SQL Data Warehouse utnyttjar ELT MPP-systemet för att utföra omvandlingarna.

Även om det finns många variationer för ELT för SQL Data Warehouse, är dessa de grundläggande steg:  

1. Extrahera källdata till textfiler.
2. Mark data i Azure Blob storage eller Azure Data Lake Store.
3. Förbered data för inläsning.
2. Läs in data i mellanlagringstabellerna genom att använda PolyBase för SQL Data Warehouse.
3. Transformera data.
4. Infoga data i produktion tabeller.


En självstudiekurs inläsning finns [Använd PolyBase för att läsa data från Azure blobblagring till Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Mer information finns i [inläsning mönster blogg](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Alternativ för att läsa in med PolyBase

PolyBase är en teknik som har åtkomst till data utanför databasen via T-SQL-språket. Det är det bästa sättet att läsa in data till SQL Data Warehouse. Med PolyBase läser in data parallellt från datakällan direkt till compute-noder. 

Du kan använda något av dessa alternativ för inläsning för att läsa in data med PolyBase.

- [PolyBase med T-SQL](load-data-from-azure-blob-storage-using-polybase.md) fungerar bra när dina data i Azure Blob storage eller Azure Data Lake Store. Det ger dig de flesta kontroll över inläsningen, men kräver också att du kan definiera externa dataobjekt. De andra metoderna definiera objekten i bakgrunden som du mappa källtabellerna till måltabell.  Du kan använda Azure Data Factory, SSIS eller Azure functions för att dirigera T-SQL-belastning. 
- [PolyBase med SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md) fungerar bra när datakällan är i SQL Server, antingen SQL Server lokalt eller i molnet. SSIS definierar källan till målet tabell mappningar, och även samordnar belastningen. Om du redan har SSIS-paket kan du ändra paket att arbeta med den nya data warehouse mål. 
- [PolyBase med Azure Data Factory (ADM)](sql-data-warehouse-load-with-data-factory.md) är ett annat orchestration-verktyg.  Den definierar en pipeline och schemalägger jobb. 

### <a name="polybase-external-file-formats"></a>PolyBase externt filformat

PolyBase läser in data från UTF-8 och UTF-16-kodade avgränsade textfiler. Förutom avgränsade textfiler laddas från Hadoop-filformat RC-filen, ORC och parkettgolv. PolyBase kan läsa in data från Gzip och snygga komprimerade filer. PolyBase stöder för närvarande inte utökade ASCII, fast bredd format och kapslade format till exempel WinZip, JSON och XML.

### <a name="non-polybase-loading-options"></a>Icke-PolyBase läser in alternativ
Om dina data inte är kompatibel med PolyBase, kan du använda [bcp](sql-data-warehouse-load-with-bcp.md) eller [SqlBulkCopy körs API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP läser in direkt till SQL Data Warehouse utan att gå via Azure Blob storage och är endast avsett för små belastning. Observera att prestandan belastningen av dessa alternativ är avsevärt långsammare än PolyBase. 


## <a name="extract-source-data"></a>Extrahera källdata

Hämtar data från källsystemet beror på källan.  Målet är att flytta data till avgränsade textfiler. Om du använder SQL Server kan du använda [kommandoradsverktyget bcp](/sql/tools/bcp-utility) att exportera data.  

## <a name="land-data-to-azure-storage"></a>Mark data till Azure-lagring

För att hamna data i Azure-lagring kan du flytta den till [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md) eller [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). I båda fallen bör data lagras i textfiler. Polybase kan läsa in i båda fallen.

Dessa är verktyg och tjänster som du kan använda för att flytta data till Azure Storage.

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) tjänsten förbättrar dataflödet i nätverket, prestanda och förutsägbarhet. ExpressRoute är en tjänst som vidarebefordrar data med en dedikerad privat anslutning till Azure. ExpressRoute anslutningar inte vidarebefordra data via det offentliga internet. Anslutningarna erbjuder flera tillförlitlighet, högre hastighet, lägre latens och högre säkerhet än vanliga anslutningar via det offentliga internet.
- [AZCopy verktyget](../storage/common/storage-use-azcopy.md) flyttar data till Azure Storage via det offentliga internet. Det fungerar storlekar för dina data är mindre än 10 TB. Testa nätverkets hastighet för att se om den är godkänd för att utföra belastningar regelbundet med AZCopy. 
- [Azure Data Factory (ADM)](../data-factory/introduction.md) har en gateway som kan installeras på den lokala servern. Du kan skapa en pipeline för att flytta data från den lokala servern upp till Azure Storage.

Mer information finns i [flytta data till och från Azure Storage](../storage/common/storage-moving-data.md)


## <a name="prepare-data"></a>Förbereda data

Du kan behöva förbereda och rensa data i ditt lagringskonto innan den läses in i SQL Data Warehouse. Förberedelse av data kan utföras medan dina data är i källan, som du vill exportera data till textfiler, eller när data har i Azure Storage.  Det är enklast att arbeta med data tidigt i processen som möjligt.  

### <a name="define-external-tables"></a>Definiera externa tabeller
Innan du kan läsa in data, måste du definiera externa tabeller i datalagret. PolyBase använder externa tabeller för att definiera och komma åt data i Azure Storage. Den externa tabellen liknar en vanlig tabell. Den största skillnaden är extern tabell pekar på data som lagras utanför datalagret. 

Definiera externa tabeller innebär att ange datakällan, i form av textfiler och tabelldefinitionerna. Det här är information för T-SQL-syntax som du behöver:
- [SKAPA EXTERN DATAKÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql)

Ett exempel för att skapa externa objekt finns i [skapa externa tabeller](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) steg i självstudiekursen lästes in.

### <a name="format-text-files"></a>Textfiler format

Du måste justera raderna i textfiler med extern tabell och formatdefinition när externa objekt har angetts. Data i varje rad i filen måste överensstämma med tabelldefinitionen.

Formatera textfiler:

- Om dina data kommer från en icke-relationella källa, måste du omvandla till rader och kolumner. Om data från en källa för Relations- eller icke-relationella omvandlas data om du vill justera med kolumndefinitionerna för tabellen som du vill läsa in data. 
- Formatera data i filen att justera kolumner och datatyper i SQL Data Warehouse tabellen. Feljusteringarna mellan datatyperna i externa textfiler och tabellen data warehouse gör rader som ska avvisas under inläsningen.
- Olika fält i en textfil med avslutas.  Se till att använda ett tecken eller en teckensekvens som inte finns i datakällan. Använd Begränsare som du har angett med [skapa externt FILFORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Inläsning i en mellanlagringstabell
Om du vill hämta data till datalagret fungerar det bra att först ladda data till en mellanlagringstabellen. Du kan hantera fel utan att störa produktions-tabeller med hjälp av en mellanlagringstabellen och du undvika att köra återställningen åtgärder på produktionstabellen. En mellanlagringstabellen ger också en möjlighet för att köra transformationer innan du infogar data i produktion tabeller med SQL Data Warehouse.

Om du vill läsa in med T-SQL, kör den [Skapa tabell AS Välj (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) T-SQL-instruktionen. Det här kommandot infogar resultatet av en select-instruktion i en ny tabell. När instruktionen väljer från en extern tabell, importerar externa data. 

I följande exempel externt Datumet är en extern tabell. Alla rader importeras till en ny tabell som kallas dbo. Datum.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>Transformera data
När data är i mellanlagringstabellen kan genomföra transformationer som kräver din arbetsbelastning. Flytta sedan informationen till en tabell i produktion.

## <a name="insert-data-into-production-table"></a>Infoga data i produktionstabeller

INSERT INTO... SELECT-instruktion flyttar data från mellanlagringstabellen till tabellen permanent. 

När du utformar ett ETL-processen, försök att köra processen på ett litet prov. Försök extraherar 1000 översta raderna från tabellen till en fil, flytta den till Azure och försök läsa in informationen i en mellanlagringstabellen. 

## <a name="partner-loading-solutions"></a>Läser in partnerlösningar
Många av våra samarbetspartners har inläsning av lösningar. Om du vill veta mer, se en lista över våra [lösningspartners](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Nästa steg
För att läsa in vägledning, se [vägledning för Läs in data](guidance-for-loading-data.md).


