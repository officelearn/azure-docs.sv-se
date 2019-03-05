---
title: I stället för ETL, designa ELT för Azure SQL Data Warehouse | Microsoft Docs
description: I stället för ETL, utforma en process för extrahering, inläsning och transformering (ELT) för att läsa in data eller Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 96f6da7e081430768b5a6f8fd874e289b8256271
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308490"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>Designa en PolyBase för datainläsning strategi för Azure SQL Data Warehouse

Traditionella SMP-datalager kan du använda en process för extrahering, transformering och inläsning (ETL) för inläsning av data. Azure SQL Data Warehouse är en arkitektur för massiv parallellbearbetning (MPP) som drar nytta av skalbarheten och flexibiliteten i beräknings- och lagringsresurser. Använda en process för extrahering, inläsning och transformering (ELT) kan du utnyttja MPP och eliminera resurser som behövs för att transformera data innan de läses in. Medan SQL Data Warehouse stöder många läser in metoder, inklusive icke-Polybase, till exempel BCP och SQL BulkCopy API, är det snabbaste och mest skalbara sättet att läsa in datum via PolyBase.  PolyBase är en teknik som ansluter till externa data som lagras i Azure Blob storage eller Azure Data Lake Store via T-SQL-språket.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Vad är ELT?

Extrahera, belastning, och omvandla (ELT) är en process som data extraheras från ett källsystem, läses in i ett data warehouse och sedan omvandlas. 

De grundläggande stegen för att implementera en PolyBase ELT för SQL Data Warehouse är:

1. Extrahera källdata i en textfil.
2. Få data till Azure Blob storage eller Azure Data Lake Store.
3. Förbereda data för inläsning.
4. Läs in data i SQL Data Warehouse med PolyBase mellanlagringstabeller. 
5. Transformera data.
6. Infoga data i produktionstabellerna.


En självstudiekurs om inläsning finns i [använda PolyBase för att läsa in data från Azure blob storage till Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Mer information finns i [läser in mönster blogg](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahera källdata i en textfil

Hämta data från källsystemet beror på lagringsplatsen.  Målet är att flytta data till PolyBase stöds avgränsade textfiler. 

### <a name="polybase-external-file-formats"></a>PolyBase externa filformaten

PolyBase läser in data från UTF-8 och UTF-16-kodade textfiler. Förutom avgränsade textfiler laddas från Hadoop-filformat RC-filen, ORC och Parquet. PolyBase kan också läsa in data från Gzip och Snappy komprimerade filer. PolyBase stöder för närvarande inte utökade ASCII, fast bredd format och kapslade format, till exempel WinZip, JSON och XML. Om du exporterar från SQL Server, kan du använda [kommandoradsverktyget bcp](/sql/tools/bcp-utility) att exportera data till avgränsade textfiler.


## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Få data till Azure Blob storage eller Azure Data Lake Store

Om du vill få data i Azure storage kan du flytta den till [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md) eller [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). I båda fallen bör data lagras i textfiler. PolyBase kan läsa in från någon av platserna.

Verktyg och tjänster som du kan använda för att flytta data till Azure Storage:

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) tjänsten förbättrar nätverkets dataflöde, prestanda och förutsägbarhet. ExpressRoute är en tjänst som dirigerar data med en dedikerad privat anslutning till Azure. ExpressRoute-anslutningar dirigerar inte data via det offentliga internet. Anslutningarna är mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via det offentliga internet.
- [AZCopy-verktyget](../storage/common/storage-moving-data.md) flyttar data till Azure Storage via det offentliga internet. Detta fungerar om dina datastorlekar är mindre än 10 TB. Testa nätverkshastigheten om du vill se om värdena är godtagbara för att utföra belastningar med jämna mellanrum med AZCopy. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) har en gateway som du kan installera på den lokala servern. Du kan skapa en pipeline som flyttar data från din lokala server upp till Azure Storage. Om du vill använda Data Factory med SQL Data Warehouse, se [läser in data i SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Förbereda data för inläsning

Du kan behöva förbereda och rensa data i ditt storage-konto innan du läser in dem i SQL Data Warehouse. Förberedelse av data kan utföras medan dina data finns i källan, eftersom du exportera data till textfiler, eller när data är i Azure Storage.  Det är enklast att arbeta med data så tidigt i processen som möjligt.  

### <a name="define-external-tables"></a>Definiera externa tabeller

Innan du kan läsa in data, måste du definiera externa tabeller i datalagret. PolyBase använder externa tabeller för att definiera och komma åt data i Azure Storage. En extern tabell liknar en databasvy. Den externa tabellen innehåller tabellschemat och pekar på data som lagras utanför datalagret. 

Definiera externa tabeller som inkluderar det att ange datakällan, textfiler och tabelldefinitionerna. Det här är ämnen för T-SQL-syntax som du behöver:
- [SKAPA EXTERN DATAKÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql)

Ett exempel för att skapa externa objekt finns i den [skapa externa tabeller](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) steg i självstudien läses in.

### <a name="format-text-files"></a>Textfiler format

När de externa objekt har definierats måste du justera raderna i textfiler med extern tabell och fil formatdefinition. Data i varje rad i filen måste justeras med tabelldefinitionen.
Att formatera textfiler:

- Om dina data kommer från en icke-relationell källa, måste du omvandla det till rader och kolumner. Om data kommer från en relationsdatabas eller icke-relationell källa, måste du omvandlade data så att den överensstämmer med kolumndefinitionerna för tabellen som du planerar att läsa in data. 
- Formatera data i filen för att anpassas till kolumner och datatyper i SQL Data Warehouse tabellen. Misspassning mellan datatyper i externa textfiler och data warehouse-tabell orsakar rader som ska avvisas under inläsningen.
- Olika fält i textfilen med en avgränsare.  Var noga med att använda ett tecken eller en teckensekvens som inte går att hitta i dina källdata. Använd avgränsare som du angav med [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4. Läsa in data till SQL Data Warehouse med PolyBase mellanlagringstabeller

Det är bäst att läsa in data i en mellanlagringstabell. Mellanlagring tabeller kan du hantera fel utan att störa produktionstabellerna. En mellanlagringstabell ger dig också möjlighet att använda SQL Data Warehouse MPP för Datatransformationer innan du infogar data i produktionstabellerna.

### <a name="options-for-loading-with-polybase"></a>Alternativ för att läsa in med PolyBase

Du kan använda något av dessa inläsning av alternativ för att läsa in data med PolyBase:

- [PolyBase med T-SQL](load-data-from-azure-blob-storage-using-polybase.md) fungerar bra när data kommer från Azure Blob storage eller Azure Data Lake Store. Det ger dig de flesta kontroll över inläsningen, men kräver också att definiera externa dataobjekt. De andra metoderna definiera objekten i bakgrunden som du mappa källtabellerna till måltabellerna.  Du kan använda Azure Data Factory, SSIS eller Azure functions för att dirigera T-SQL-belastning. 
- [PolyBase med SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) fungerar bra när dina källdata är i SQL Server, SQL Server lokalt eller i molnet. SSIS definierar källan till målet tabellkopplingar och också styr belastningen. Du kan ändra de paket som du arbetar med den nya data warehouse mål om du redan har SSIS-paket. 
- [PolyBase med Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) är ett annat verktyg för dirigering.  Den definierar en pipeline och schemalägger jobb. 
- [PolyBase med Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) överför data från en SQL Data Warehouse-tabell till en Databricks-dataframe och/eller skriver data från en Databricks-dataframe till en SQL Data Warehouse-tabell.

### <a name="non-polybase-loading-options"></a>Alternativ för icke-PolyBase-inläsning

Om dina data inte är kompatibelt med PolyBase kan du använda [bcp](/sql/tools/bcp-utility) eller [SqlBulkCopy körs API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP läser in direkt till SQL Data Warehouse utan att gå via Azure Blob storage och är endast avsett för små belastning. Observera att inläsningsprestanda av dessa alternativ är avsevärt långsammare än PolyBase. 


## <a name="5-transform-the-data"></a>5. Transformera data

När data är i mellanlagringstabellen kan utföra omvandlingar som kräver att din arbetsbelastning. Flytta data i en produktionstabell.


## <a name="6-insert-the-data-into-production-tables"></a>6. Infoga data i produktionstabellerna

INSERT INTO... SELECT-instruktion flyttas data från mellanlagringstabellen till tabellen permanent. 

När du utformar en ETL-processen, försök att köra processen på ett litet test-exempel. Försök att extrahera 1000 raderna från tabellen till en fil, flytta den till Azure och försök sedan läser in dem i en mellanlagringstabell. 


## <a name="partner-loading-solutions"></a>Läser in partnerlösningar

Många av våra partner har inläsning av lösningar. Om du vill veta mer kan du se en lista över våra [samarbetspartner](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Nästa steg

Vägledning för inläsning av, finns i [vägledning för att läsa in data](guidance-for-loading-data.md).


