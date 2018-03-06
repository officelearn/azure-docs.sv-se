---
title: "Använd bcp för att läsa in data till SQL Data Warehouse | Microsoft Docs"
description: "Läs mer om vad bcp är och hur man använder det för informationslager-scenarier."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 55211e29149cd334421bd8723d47278a19afbfbb
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="load-data-with-bcp"></a>Läs in data med bcp

**[bcp](/sql/tools/bcp-utility.md)** är ett kommandoradsverktyg för massinläsning som låter dig kopiera data mellan SQL Server, datafiler och SQL Data Warehouse. Använd bcp för att importera ett stort antal rader till SQL Data Warehouse-tabeller eller exportera data från SQL Server-tabeller till datafiler. Bcp kräver ingen Transact-SQL kunskap förutom när det används med queryout-alternativet.

bcp är ett snabbt och enkelt sätt att flytta mindre datauppsättningar in och ut från en SQL Data Warehouse-databas. Den exakta mängden data som du bör läsa in/extrahera via bcp beror på din nätverksanslutning till Azure.  Små dimensionstabeller kan enkelt läsas in och extraheras med bcp. Dock är Polybase, inte bcp, det rekommenderade verktyget för inläsning och extrahering av stora mängder data. PolyBase är utformat för den massivt parallella bearbetningsarkitekturen i SQL Data Warehouse.

Med bcp kan du:

* Använda ett kommandoradsverktyg till att läsa in data till SQL Data Warehouse.
* Använda ett kommandoradsverktyg till att extrahera data från SQL Data Warehouse.

I den här självstudien:

* importerar du data till en tabell med kommandot bcp in
* exporterar du data från en tabell med kommandot bcp out

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom de här självstudierna, behöver du:

* En SQL Data Warehouse-databas
* Kommandoradsverktygen bcp och sqlcmd. Du kan ladda ned dessa från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=36433). 

### <a name="data-in-ascii-or-utf-16-format"></a>Data i ASCII- eller UTF-16-format
Om du använder egna data i självstudierna, måste de använda sig av ASCII- eller UTF-16-kodning eftersom bcp inte stöder UTF-8. 

PolyBase stöder UTF-8 men har inte än stöd för UTF-16. Om du vill använda bcp för export av data och PolyBase för datainläsning måste du omvandla data till UTF-8 när de har exporterats från SQL Server. 

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

Mer information om att skapa en tabell finns i [Tabellöversikt](sql-data-warehouse-tables-overview.md) och syntaxen för [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md).
 

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

Frågan bör returnera följande resultat:

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
När du har läst in data är det sista steget att skapa eller uppdatera statistik. Det här förbättrar frågeprestandan. Mer information finns i [Statistik](sql-data-warehouse-tables-statistics.md). I följande sqlcmd-exempel skapas statistik för den tabell som innehåller nyinlästa data.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportera data från SQL Data Warehouse
I den här självstudien skapar du en datafil från en tabell i SQL Data Warehouse. Du exporterar de data du importerade i föregående avsnitt. Resultaten hamnar i en fil som heter DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Steg 1: Exportera data
Med bcp-verktyget kan du ansluta och exportera data med hjälp av följande kommando, där du ersätter värdena med lämpliga sådana:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Du kan verifiera att data exporterats korrekt genom att öppna den nya filen. Data i den här filen ska matcha texten nedan:

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
När du designar din inläsning kan du läsa [Inläsningsöversikt](sql-data-warehouse-design-elt-data-loading.md).  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
