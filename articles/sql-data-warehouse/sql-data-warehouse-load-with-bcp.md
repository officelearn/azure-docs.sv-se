---
title: "Använd bcp för att läsa in data till SQL Data Warehouse | Microsoft Docs"
description: "Läs mer om vad bcp är och hur man använder det för informationslager-scenarier."
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: e368ae8b249fe3c33371794160440e472b0f35e3
ms.lasthandoff: 01/30/2017



---
# <a name="load-data-with-bcp"></a>Läsa in data med bcp
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

**[bcp][bcp]** är ett kommandoradsverktyg för massinläsning som låter dig kopiera data mellan SQL Server, datafiler och SQL Data Warehouse. Använd bcp för att importera ett stort antal rader till SQL Data Warehouse-tabeller eller exportera data från SQL Server-tabeller till datafiler. Bcp kräver ingen Transact-SQL kunskap förutom när det används med queryout-alternativet.

bcp är ett snabbt och enkelt sätt att flytta mindre datauppsättningar in och ut från en SQL Data Warehouse-databas. Den exakta mängden data som rekommenderas att läsa in/extrahera med bcp, beror på din nätverksanslutning till Azure-datacentret.  Generellt sett kan dimensionstabeller enkelt läsas in och extraheras med bcp, men bcp rekommenderas inte för inläsning eller extrahering av stora mängder data.  Polybase rekommenderas för inläsning och extrahering av stora mängder data, eftersom det är bättre på att utnyttja den massivt parallella bearbetningsarkitekturen i SQL Data Warehouse.

Med bcp kan du:

* Använda ett enkelt kommandoradsverktyg för att läsa in data till SQL Data Warehouse.
* Använda ett enkelt kommandoradsverktyg för att extrahera data från SQL Data Warehouse.

De här självstudierna visar hur du:

* Importerar data till en tabell med bcp in-kommandot
* Exporterar data från en tabell med bcp out-kommandot

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>Krav
För att gå igenom de här självstudierna, behöver du:

* En SQL Data Warehouse-databas
* Kommandoradsverktyget bcp installerat
* Kommandoradsverktyget SQLCMD installerat

> [!NOTE]
> Du kan hämta verktygen bcp och sqlcmd från [Microsoft Download Center][Microsoft Download Center].
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>Importera data till SQL Data Warehouse
I de här självstudierna kommer du att skapa en tabell i Azure SQL Data Warehouse och importera data till tabellen.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Steg 1: Skapa en tabell i Azure SQL Data Warehouse
Använd sqlcmd från en kommandotolk för att köra följande fråga och skapa en tabell på din instans:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

> [!NOTE]
> Se [Tabellöversikt][Table Overview] eller [CREATE TABLE-syntax][CREATE TABLE syntax] för ytterligare information om hur man skapar en tabell i SQL Data Warehouse och alternativen som finns i WITH-satsen.
> 
> 

### <a name="step-2-create-a-source-data-file"></a>Steg 2: Skapa en källdatafil
Öppna Anteckningar och kopiera följande datarader till en ny textfil. Spara sedan filen till din lokala temp-katalog, C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Det är viktigt att komma ihåg att bcp.exe inte har stöd för UTF-8 filkodning. Använd ASCII-filer eller UTF-16-kodade filer när du använder bcp.exe.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>Steg 3: Anslut och importera data
Med bcp kan du ansluta och importera data med hjälp av följande kommando, där du ersätter värdena med lämpliga sådana:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Du kan verifiera att data lästs in genom att köra följande fråga med hjälp av sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Det ska returnera följande resultat:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Steg 4: Skapa statistik på dina nyinlästa data
SQL Data Warehouse stöder inte än automatiskt skapande eller uppdatering av statistik. För att få bästa möjliga prestanda från dina frågor, är det viktigt att statistik skapas på alla kolumner i alla tabeller efter den första inläsningen eller vid betydande dataändringar. En detaljerad förklaring av statistik finns i ämnet [Statistik][Statistics] i ämnesgruppen Utveckla. Nedan följer ett enkelt exempel på hur du skapar statistik på tabellerna som lästs in i det här exemplet

Kör följande CREATE STATISTICS-uttryck från en sqlcmd-kommandotolk:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportera data från SQL Data Warehouse
I de här självstudierna kommer du att skapa en datafil från en tabell i SQL Data Warehouse. Vi kommer att exportera de data vi skapade ovan till en ny datafil som heter DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Steg 1: Exportera data
Med bcp-verktyget kan du ansluta och exportera data med hjälp av följande kommando, där du ersätter värdena med lämpliga sådana:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Du kan verifiera att data exporterats korrekt genom att öppna den nya filen. De data som finns i filen ska matcha texten nedan:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> På grund av hur distribuerade system fungerar, är det möjligt att dataordningen inte är samma i alla SQL Data Warehouse-databaser. Ett annat alternativ är att använda **queryout**-funktionen i bcp för att skriva ett fråge-extrakt istället för att exportera hela tabellen.
> 
> 

## <a name="next-steps"></a>Nästa steg
Se [Läs in data till SQL Data Warehouse][Load data into SQL Data Warehouse] för en översikt över inläsning.
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

