---
title: "Läs in data till Azure SQL Data Warehouse | Microsoft Docs"
description: "Läs om vanliga scenarier för datainläsning till SQL Data Warehouse. Dessa inkluderar med PolyBase, Azure blob storage, flat-filer och disk leverans. Du kan också använda verktyg från tredje part."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: c4199a387f5cdbd477a5e348e48ba8e8b5900075
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="load-data-into-azure-sql-data-warehouse"></a>Läs in data till Azure SQL Data Warehouse
En sammanfattning av scenariot alternativ och rekommendationer för att läsa in data i SQL Data Warehouse.

Den svåraste delen av läsning av data är vanligtvis förbereder data för den. Azure förenklar inläsning genom att använda Azure blob-lagring som en gemensam datalager för många av tjänsterna och använda Azure Data Factory för att dirigera kommunikation och flytt mellan Azure-tjänster. De här processerna är integrerade med PolyBase-teknik som använder massivt parallell bearbetning (MPP) för att läsa in data parallellt från Azure blob storage till SQL Data Warehouse. 

Självstudier som läser in exempeldatabaserna finns [ladda exempeldatabaserna][Load sample databases].

## <a name="load-from-azure-blob-storage"></a>Läsa in från Azure blob storage
Det snabbaste sättet att importera data till SQL Data Warehouse är att använda PolyBase för att läsa in data från Azure blob storage. PolyBase använder SQL Data Warehouse massivt parallell bearbetning (MPP) design för att läsa in data parallellt från Azure blob storage. Du kan använda T-SQL-kommandon eller ett Azure Data Factory-pipelinen för att använda PolyBase.

### <a name="1-use-polybase-and-t-sql"></a>1. Använd PolyBase och T-SQL
Översikt över processen för inläsning:

1. Flytta dina data till Azure blob storage eller Azure Data Lake Store och lagra den i textfiler.
2. Konfigurera externa objekt i SQL Data Warehouse definiera plats och formatet för data
3. Köra ett T-SQL-kommando för att läsa in data parallellt i en ny databastabell.

<!-- 5. Schedule and run a loading job. --> 

En självstudiekurs finns [läsa in data från Azure blob storage till SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="2-use-azure-data-factory"></a>2. Använda Azure Data Factory
Ett enklare sätt att använda PolyBase kan du skapa ett Azure Data Factory-pipelinen som använder PolyBase för att läsa in data från Azure blob storage till SQL Data Warehouse. Det går snabbt att konfigurera eftersom du inte behöver att definiera T-SQL-objekt. Om du behöver fråga efter externa data utan att importera den använda T-SQL. 

Översikt över processen för inläsning:

1. Flytta data till Azure blob storage och lagrar den i textfiler. Azure Data Factory stöder för närvarande inte ADLS-anslutning med PolyBase).
2. Skapa ett Azure Data Factory-pipelinen för att mata in data. Använd PolyBase-alternativet.
4. Schemalägga och köra pipelinen.

En självstudiekurs finns [läsa in data från Azure blob storage till SQL Data Warehouse (Azure Data Factory)][Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)].

## <a name="load-from-sql-server"></a>Läs in från SQLServer
Du kan använda Integration Services (SSIS), överföring flat-filer eller leverera diskar till Microsoft för att läsa in data från SQL Server till SQL Data Warehouse. Läs in finns en sammanfattning av de olika överföring processer och länkar till självstudier.

Om du vill planera en fullständig datamigrering från SQL Server till SQL Data Warehouse, finns det [migreringsöversikt][Migration overview]. 

### <a name="use-integration-services-ssis"></a>Använda Integration Services (SSIS)
Om du redan använder Integration Services (SSIS) paket att läsa in i SQL Server, kan du uppdatera dina paket för att använda SQL Server som käll- och SQL Data Warehouse som mål. Det går snabbt och enkelt att göra, och är ett bra alternativ om du inte vill migrera din inläsningen om du vill använda redan data i molnet. Förhållandet är belastningen blir långsammare än att använda PolyBase eftersom den här SSIS inte utför belastningen parallellt.

Översikt över processen för inläsning:

1. Ändra Integration Services-paket för att peka till SQL Server-instansen för källan och SQL Data Warehouse-databas för mål.
2. Migrera schemat till SQL Data Warehouse, om du inte redan har gjort det.
3. Ändra mappningen i dina paket använda endast de datatyper som stöds av SQL Data Warehouse.
4. Schemalägga och köra paketet.

En självstudiekurs finns [läsa in data från SQL Server till Azure SQL Data Warehouse (SSIS)][Load data from SQL Server to Azure SQL Data Warehouse (SSIS)].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Använda AZCopy (rekommenderas för < 10 TB data)
Om dina data är < 10 TB kan du exportera data från SQL Server till flat-filer, kopiera filer till Azure blob storage och sedan använda PolyBase för att läsa in data till SQL Data Warehouse

Översikt över processen för inläsning:

1. Använd kommandoradsverktyget BCP för att exportera data från SQL Server till flat-filer.
2. Använd kommandoradsverktyget azcopy för att kopiera data från flata filer till Azure blob storage.
3. Använd PolyBase för att läsa in i SQL Data Warehouse.

En självstudiekurs finns [läsa in data från Azure blob storage till SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="use-bcp"></a>Använd bcp
Om du har en liten mängd data kan du använda bcp för att läsa in direkt i Azure SQL Data Warehouse.

Översikt över processen för inläsning:

1. Använd kommandoradsverktyget BCP för att exportera data från SQL Server till flat-filer.
2. Använd bcp för att läsa in data från flata filer direkt till SQL Data Warehouse.

En självstudiekurs finns [läsa data från SQL Server till Azure SQL Data Warehouse (bcp)][Load data from SQL Server to Azure SQL Data Warehouse (bcp)].

### <a name="use-importexport-recommended-for--10-tb-data"></a>Använd Import/Export (rekommenderas för > 10 TB data)
Om dina data är > 10 TB och du vill flytta den till Azure, rekommenderar vi att du använder våra disk som levererar tjänsten [Import/Export][Import/Export]. 

Översikt över processen för inläsning

1. Använd kommandoradsverktyget BCP för att exportera data från SQL Server till flat-filer på överföringsbar diskar.
2. Leverera diskar till Microsoft.
3. Microsoft läser in data i SQL Data Warehouse

## <a name="load-from-hdinsight"></a>Läsa in från HDInsight
SQL Data Warehouse stöder läsning av data från HDInsight via PolyBase. Processen är densamma som läser in data från Azure Blob Storage - med PolyBase för att ansluta till HDInsight för att läsa in data. 

### <a name="1-use-polybase-and-t-sql"></a>1. Använd PolyBase och T-SQL
Översikt över processen för inläsning:

1. Flytta data till HDInsight och lagra den på textfiler, ORC eller parkettgolv format.
2. Konfigurera externa objekt i SQL Data Warehouse definiera plats och formatet för data.
3. Köra ett T-SQL-kommando för att läsa in data parallellt i en ny databastabell.

En självstudiekurs finns [läsa in data från Azure blob storage till SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

## <a name="recommendations"></a>Rekommendationer
Många av våra samarbetspartners har inläsning av lösningar. Om du vill veta mer, se en lista över våra [lösningspartners][solution partners]. 

Om dina data kommer från en icke-relationella källa och du vill läsa in det i SQL Data Warehouse måste du omvandla det till rader och kolumner innan du läser in. Omvandlade data behöver inte lagras i en databas, den kan lagras i textfiler.

Skapa statistik på nyinlästa data. SQL Data Warehouse stöder inte än automatiskt skapande eller uppdatering av statistik.  För att få bästa möjliga prestanda från dina frågor, är det viktigt att skapa statistik på alla kolumner i alla tabeller efter den första inläsningen eller vid betydande dataändringar i data.  Mer information finns i [statistik][Statistics].

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se den [utvecklingsöversikt][development overview].

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Load data from SQL Server to Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Load sample databases]: ./sql-data-warehouse-load-sample-databases.md
[Migration overview]: ./sql-data-warehouse-overview-migrate.md
[solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Import/Export]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
