---
title: I stället för ETL, design ELT
description: Implementera flexibla datainläsningsstrategier för Synapse SQL-pool i Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e99fd898956e11a4827d023691111a47e5a790c0
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744953"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Datainläsningsstrategier för Synapse SQL-pool

Traditionella SMP SQL-pooler använder en ETL-process (Extract, Transform, and Load) för inläsning av data. Synapse SQL-pool, inom Azure Synapse Analytics, har en massivt parallell bearbetning (MPP) arkitektur som drar nytta av skalbarhet och flexibilitet av beräknings- och lagringsresurser.

Med hjälp av en process för utvinning, inläsning och transformering (ELT) utnyttjar MPP och eliminerar de resurser som behövs för dataomvandling före inläsning.

Medan SQL-poolen stöder många inläsningsmetoder, inklusive populära SQL Server-alternativ som [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) och [SqlBulkCopy API,](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)är det snabbaste och mest skalbara sättet att läsa in data via PolyBase externa tabeller och [COPY-uttrycket](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (förhandsversion).

Med PolyBase och COPY-satsen kan du komma åt externa data som lagras i Azure Blob storage eller Azure Data Lake Store via T-SQL-språket. För största möjliga flexibilitet när du läser in rekommenderar vi att du använder COPY-satsen.

> [!NOTE]  
> COPY-satsen är för närvarande i offentlig förhandsversion. Om du vill ge feedback skickar sqldwcopypreview@service.microsoft.comdu e-post till följande distributionslista: .

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Vad är ELT?

Extrahera, läsa in och omforma (ELT) är en process genom vilken data extraheras från ett källsystem, läses in i en SQL-pool och sedan omvandlas.

De grundläggande stegen för att genomföra ELT är:

1. Extrahera källdata i textfiler.
2. Landa data i Azure Blob-lagring eller Azure Data Lake Store.
3. Förbered data för inläsning.
4. Läs in data i mellanlagringstabeller med PolyBase eller COPY-kommandot.
5. Omvandla data.
6. Infoga data i produktionstabeller.

En självstudiekurs för PolyBase-inläsning finns i [Använda PolyBase för att läsa in data från Azure blob storage](load-data-from-azure-blob-storage-using-polybase.md).

Mer information finns i [Läsa in mönsterblogg](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahera källdata till textfiler

Att få ut data från källsystemet beror på lagringsplatsen.  Målet är att flytta data till PolyBase och COPY stöds avgränsad text eller CSV-filer.

### <a name="polybase-and-copy-external-file-formats"></a>Externa filformat för PolyBase och COPY

Med PolyBase och COPY-satsen kan du läsa in data från UTF-8- och UTF-16-kodade avgränsade text- eller CSV-filer. Förutom avgränsad text eller CSV-filer, laddas den från Hadoop filformat som ORC och Parkett. PolyBase och COPY-satsen kan också läsa in data från Gzip- och Snappy-komprimerade filer.

Utökade ASCII-format, format med fast bredd och kapslade format som WinZip eller XML stöds inte. Om du exporterar från SQL Server kan du använda [kommandoradsverktyget bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för att exportera data till avgränsade textfiler.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. För in data i Azure Blob-lagring eller Azure Data Lake Store

Om du vill landa data i Azure-lagring kan du flytta dem till [Azure Blob storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eller [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). På båda platserna ska data lagras i textfiler. PolyBase och COPY-satsen kan läsas in från båda platserna.

Verktyg och tjänster som du kan använda för att flytta data till Azure Storage:

- [Azure ExpressRoute-tjänsten](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) förbättrar nätverksdataflödet, prestanda och förutsägbarhet. ExpressRoute är en tjänst som dirigerar dina data via en dedikerad privat anslutning till Azure. ExpressRoute-anslutningar dirigerar inte data via det offentliga internet. Anslutningarna ger mer tillförlitlighet, snabbare hastigheter, lägre fördröjningar och högre säkerhet än typiska anslutningar via det offentliga internet.
- [AZCopy-verktyget](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) flyttar data till Azure Storage via det offentliga internet. Detta fungerar om dina datastorlekar är mindre än 10 TB. För att utföra belastningar regelbundet med AZCopy testar du nätverkshastigheten för att se om det är acceptabelt.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) har en gateway som du kan installera på din lokala server. Sedan kan du skapa en pipeline för att flytta data från din lokala server upp till Azure Storage. Information om hur du använder Data Factory med SQL Analytics finns [i Läsa in data för SQL Analytics](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Förbereda data för inläsning

Du kan behöva förbereda och rensa data i ditt lagringskonto innan du läser in dem. Dataförberedelse kan utföras medan dina data finns i källan, när du exporterar data till textfiler eller efter att data finns i Azure Storage.  Det är lättast att arbeta med data så tidigt i processen som möjligt.  

### <a name="define-external-tables"></a>Definiera externa tabeller

Om du använder PolyBase måste du definiera externa tabeller i SQL-poolen innan du läser in dem. Externa tabeller krävs inte av COPY-satsen. PolyBase använder externa tabeller för att definiera och komma åt data i Azure Storage.

En extern tabell liknar en databasvy. Den externa tabellen innehåller tabellschemat och pekar på data som lagras utanför SQL-poolen.

Att definiera externa tabeller innebär att du anger datakällan, formatet på textfilerna och tabelldefinitionerna. T-SQL syntax referens artiklar som du behöver är:

- [SKAPA EXTERN DATAKÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

När du läser in Parkett är SQL-datatypsmappningen:

| **Typ av parkettdata** | **SQL-datatyp** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        boolean        |        bit        |
|        double         |       float       |
|         float         |       real        |
|        double         |       money       |
|        double         |    smallmoney     |
|        sträng         |       nchar       |
|        sträng         |     nvarchar      |
|        sträng         |       char        |
|        sträng         |      varchar      |
|        binary         |      binary       |
|        binary         |     varbinary     |
|       timestamp       |       date        |
|       timestamp       |   smalldatetime   |
|       timestamp       |     datetime2     |
|       timestamp       |     datetime      |
|       timestamp       |       time        |
|         date          |       date        |
|        decimal        |      decimal      |

Ett exempel på hur du skapar externa objekt finns i steget [Skapa externa tabeller](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) i inläsningsstudien.

### <a name="format-text-files"></a>Formatera textfiler

Om du använder PolyBase måste de definierade externa objekten justera raderna i textfilerna med den externa tabellen och filformatsdefinitionen. Data i varje rad i textfilen måste justeras mot tabelldefinitionen.
Så här formaterar du textfilerna:

- Om dina data kommer från en icke-relationell källa måste du omvandla dem till rader och kolumner. Oavsett om data kommer från en relations- eller icke-relationell källa måste data omvandlas så att de överensstämmer med kolumndefinitionerna för den tabell som du planerar att läsa in data i.
- Formatera data i textfilen så att de justeras med kolumnerna och datatyperna i måltabellen. Om du vill feljustering mellan datatyper i de externa textfilerna och SQL-biljardtabellen avvisas rader under inläsningen.
- Separata fält i textfilen med en terminator.  Var noga med att använda ett tecken eller en teckensekvens som inte finns i källdata. Använd den avslutare som du angav med [SKAPA EXTERNT FILFORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Ladda data med PolyBase eller COPY-satsen

Det är bäst att läsa in data i en mellanlagringstabell. Med mellanlagringstabeller kan du hantera fel utan att störa produktionstabellerna. En mellanlagringstabell ger dig också möjlighet att använda SQL-pool MPP för dataomvandlingar innan du infogar data i produktionstabeller.

Tabellen måste skapas i föraningen när den läses in i en mellanlagringstabell med COPY.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Alternativ för inläsning med PolyBase- och COPY-sats

Om du vill läsa in data med PolyBase kan du använda något av följande inläsningsalternativ:

- [PolyBase med T-SQL](load-data-from-azure-blob-storage-using-polybase.md) fungerar bra när dina data finns i Azure Blob storage eller Azure Data Lake Store. Det ger dig mest kontroll över inläsningsprocessen, men kräver också att du definierar externa dataobjekt. De andra metoderna definierar dessa objekt bakom kulisserna när du mappar källtabeller till måltabeller.  Om du vill dirigera T-SQL-belastningar kan du använda Azure Data Factory-, SSIS- eller Azure-funktioner.
- [PolyBase med SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) fungerar bra när källdata finns i SQL Server, antingen SQL Server lokalt eller i molnet. SSIS definierar källan till måltabellmappningar och dirigerar även inläsningen. Om du redan har SSIS-paket kan du ändra paketen så att de fungerar med det nya informationslagermålet.
- [PolyBase och COPY-sats med Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) är ett annat orkestreringsverktyg.  Den definierar en pipeline och schemalägger jobb.
- [PolyBase med Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) överför data från en tabell till en Databricks-dataram och/eller skriver data från en Databricks-dataram till en tabell med PolyBase.

### <a name="other-loading-options"></a>Andra inläsningsalternativ

Förutom PolyBase och COPY-satsen kan du använda [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) eller [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). bcp laddar direkt till databasen utan att gå igenom Azure Blob-lagring och är endast avsedd för små belastningar.

> [!NOTE]
> Belastningsprestanda för dessa alternativ är långsammare än PolyBase och COPY-satsen.

## <a name="5-transform-the-data"></a>5. Omvandla data

Medan data finns i mellanlagringstabellen utför du omvandlingar som din arbetsbelastning kräver. Flytta sedan data till en produktionstabell.

## <a name="6-insert-the-data-into-production-tables"></a>6. Infoga data i produktionstabeller

Insatsen i ... SELECT-satsen flyttar data från mellanlagringstabellen till den permanenta tabellen.

När du utformar en ETL-process kan du prova att köra processen på ett litet testexempel. Prova att extrahera 1 000 rader från tabellen till en fil, flytta den till Azure och försök sedan läsa in den i en mellanlagringstabell.

## <a name="partner-loading-solutions"></a>Lösningar för lastning av partner

Många av våra partners har lastningslösningar. Mer information finns i en lista över våra [lösningspartners.](sql-data-warehouse-partner-business-intelligence.md)

## <a name="next-steps"></a>Nästa steg

Hjälp med inläsningen hittar du i [Vägledning för att läsa in data](guidance-for-loading-data.md).
