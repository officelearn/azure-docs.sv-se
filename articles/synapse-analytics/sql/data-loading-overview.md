---
title: Istället för ETL, design ELT för Synapse SQL pool | Microsoft-dokument
description: I stället för ETL utformar du en utvinnings-, inläsnings- och transformeringsprocess (ELT) för inläsning av data eller SQL-pool.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 596f4bcf2e3f829430fdc90eb1806a44a84b2bc5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429595"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-synapse-sql-pool"></a>Utforma en PolyBase-strategi för datainläsning för Azure Synapse SQL-pool

Traditionella SMP-datalager använder en ETL-process (Extract, Transform and Load) för inläsning av data. Azure SQL Pool är en massivt parallell bearbetning (MPP) arkitektur som drar nytta av skalbarhet och flexibilitet av beräknings-och lagringsresurser. Genom att använda en process för utvinning, belastning och transformering (ELT) kan du dra nytta av MPP och eliminera resurser som behövs för att omvandla data före inläsning.

Sql-poolen stöder många inläsningsmetoder, inklusive icke-Polybase-alternativ som BCP och SQL BulkCopy API, men det snabbaste och mest skalbara sättet att läsa in datum är via PolyBase.  PolyBase är en teknik som kommer åt externa data som lagras i Azure Blob storage eller Azure Data Lake Store via T-SQL-språket.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Vad är ELT?

Extrahera, läsa in och omforma (ELT) är en process genom vilken data extraheras från ett källsystem, läses in i ett informationslager och sedan omvandlas.

De grundläggande stegen för att implementera en PolyBase ELT för SQL-pool är:

1. Extrahera källdata i textfiler.
2. Landa data i Azure Blob-lagring eller Azure Data Lake Store.
3. Förbered data för inläsning.
4. Läs in data i SQL-mellanlagringstabeller med PolyBase.
5. Omvandla data.
6. Infoga data i produktionstabeller.

En inläsningsdator självstudie finns i [Använda PolyBase för att läsa in data från Azure blob storage till Azure SQL Data Warehouse](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Mer information finns i [Läsa in mönsterblogg](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahera källdata till textfiler

Att få ut data från källsystemet beror på lagringsplatsen.  Målet är att flytta data till PolyBase stöds avgränsade textfiler.

### <a name="polybase-external-file-formats"></a>Externa polybas-format

PolyBase läser in data från UTF-8 och UTF-16 kodade avgränsade textfiler. Förutom de avgränsade textfilerna läses in från Hadoop-filformaten RC File, ORC och Parquet. PolyBase kan också läsa in data från Gzip och Snappy komprimerade filer. PolyBase stöder för närvarande inte utökat ASCII-format med fast bredd och kapslade format som WinZip, JSON och XML.

Om du exporterar från SQL Server kan du använda [bcp-kommandoradsverktyget](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att exportera data till avgränsade textfiler. Mappningen av parkett till SQL DW-datatyp är följande:

| **Typ av parkettdata** |                      **SQL-datatyp**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        sträng         |                            nchar                             |
|        sträng         |                           nvarchar                           |
|        sträng         |                             char                             |
|        sträng         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. För in data i Azure Blob-lagring eller Azure Data Lake Store

Om du vill landa data i Azure-lagring kan du flytta dem till [Azure Blob storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) eller [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). På båda platserna ska data lagras i textfiler. PolyBase kan läsas in från båda platserna.

Verktyg och tjänster som du kan använda för att flytta data till Azure Storage:

- [Azure ExpressRoute-tjänsten](../../expressroute/expressroute-introduction.md) förbättrar nätverksdataflödet, prestanda och förutsägbarhet. ExpressRoute är en tjänst som dirigerar dina data via en dedikerad privat anslutning till Azure. ExpressRoute-anslutningar dirigerar inte data via det offentliga internet. Anslutningarna ger mer tillförlitlighet, snabbare hastigheter, lägre fördröjningar och högre säkerhet än typiska anslutningar via det offentliga internet.
- [AZCopy-verktyget](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) flyttar data till Azure Storage via det offentliga internet. Detta fungerar om dina datastorlekar är mindre än 10 TB. För att utföra belastningar regelbundet med AZCopy testar du nätverkshastigheten för att se om det är acceptabelt.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) har en gateway som du kan installera på din lokala server. Sedan kan du skapa en pipeline för att flytta data från din lokala server upp till Azure Storage. Information om hur du använder Data Factory med SQL-pool finns [i Läsa in data i SQL-poolen](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Förbereda data för inläsning

Du kan behöva förbereda och rensa data i ditt lagringskonto innan du läser in dem i SQL-poolen. Dataförberedelse kan utföras medan dina data finns i källan, när du exporterar data till textfiler eller efter att data finns i Azure Storage.  Det är lättast att arbeta med data så tidigt i processen som möjligt.  

### <a name="define-external-tables"></a>Definiera externa tabeller

Innan du kan läsa in data måste du definiera externa tabeller i informationslagret. PolyBase använder externa tabeller för att definiera och komma åt data i Azure Storage. En extern tabell liknar en databasvy. Den externa tabellen innehåller tabellschemat och pekar på data som lagras utanför informationslagret.

Att definiera externa tabeller innebär att du anger datakällan, formatet på textfilerna och tabelldefinitionerna. Dessa är de T-SQL-syntaxämnen som du behöver:

- [SKAPA EXTERN DATAKÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

Ett exempel på hur du skapar externa objekt finns i steget [Skapa externa tabeller](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-external-tables-for-the-sample-data) i inläsningsstudien.

### <a name="format-text-files"></a>Formatera textfiler

När de externa objekten har definierats måste du justera raderna i textfilerna med den externa tabell- och filformatsdefinitionen. Data i varje rad i textfilen måste justeras mot tabelldefinitionen.
Så här formaterar du textfilerna:

- Om dina data kommer från en icke-relationell källa måste du omvandla dem till rader och kolumner. Oavsett om data kommer från en relations- eller icke-relationell källa måste data omvandlas så att de överensstämmer med kolumndefinitionerna för den tabell som du planerar att läsa in data i.
- Formatera data i textfilen så att de justeras med kolumnerna och datatyperna i SQL-poolens måltabell. Feljustering mellan datatyper i de externa textfilerna och datalagertabellen gör att rader avvisas under inläsningen.
- Separata fält i textfilen med en terminator.  Var noga med att använda ett tecken eller en teckensekvens som inte finns i källdata. Använd den avslutare som du angav med [SKAPA EXTERNT FILFORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-into-sql-pool-staging-tables-using-polybase"></a>4. Ladda in data i SQL-pool mellanlagringstabeller med PolyBase

Det är bäst att läsa in data i en mellanlagringstabell. Med mellanlagringstabeller kan du hantera fel utan att störa produktionstabellerna. En mellanlagringstabell ger dig också möjlighet att använda SQL-pool MPP för dataomvandlingar innan du infogar data i produktionstabeller.

### <a name="options-for-loading-with-polybase"></a>Alternativ för inläsning med PolyBase

Om du vill läsa in data med PolyBase kan du använda något av följande inläsningsalternativ:

- [PolyBase med T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) fungerar bra när dina data finns i Azure Blob storage eller Azure Data Lake Store. Det ger dig mest kontroll över inläsningsprocessen, men kräver också att du definierar externa dataobjekt. De andra metoderna definierar dessa objekt bakom kulisserna när du mappar källtabeller till måltabeller.  Om du vill dirigera T-SQL-belastningar kan du använda Azure Data Factory-, SSIS- eller Azure-funktioner.
- [PolyBase med SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) fungerar bra när källdata finns i SQL Server, antingen SQL Server lokalt eller i molnet. SSIS definierar källan till måltabellmappningar och dirigerar även inläsningen. Om du redan har SSIS-paket kan du ändra paketen så att de fungerar med det nya informationslagermålet.
- [PolyBase med Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) är ett annat orkestreringsverktyg.  Den definierar en pipeline och schemalägger jobb.
- [PolyBase med Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) överför data från en SQL Data Warehouse-tabell till en Databricks-dataram och/eller skriver data från en Databricks-dataram till en SQL Data Warehouse-tabell med PolyBase.

### <a name="non-polybase-loading-options"></a>Alternativ för inläsning av icke-PolyBase

Om dina data inte är kompatibla med PolyBase kan du använda [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) eller [SQLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). bcp laddar direkt till SQL-pool utan att gå igenom Azure Blob-lagring och är endast avsedd för små belastningar. Belastningsprestanda för dessa alternativ är betydligt långsammare än PolyBase.

## <a name="5-transform-the-data"></a>5. Omvandla data

Medan data finns i mellanlagringstabellen utför du omvandlingar som din arbetsbelastning kräver. Flytta sedan data till en produktionstabell.

## <a name="6-insert-the-data-into-production-tables"></a>6. Infoga data i produktionstabeller

Insatsen i ... SELECT-satsen flyttar data från mellanlagringstabellen till den permanenta tabellen.

När du utformar en ETL-process kan du prova att köra processen på ett litet testexempel. Prova att extrahera 1 000 rader från tabellen till en fil, flytta den till Azure och försök sedan läsa in den i en mellanlagringstabell.

## <a name="partner-loading-solutions"></a>Lösningar för lastning av partner

Många av våra partners har lastningslösningar. Mer information finns i en lista över våra [lösningspartners.](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Nästa steg

Läs vägledning för inläsning finns i [Vägledning för inläsningsdata](data-loading-best-practices.md).
