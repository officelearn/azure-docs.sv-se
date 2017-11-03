---
title: "Läs in exempeldata i SQL Data Warehouse | Microsoft Docs"
description: "läs in exempeldata i SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 1e0df958a2f18fe1e988168918e5cfd293f84e64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="load-sample-data-into-sql-data-warehouse"></a>läs in exempeldata i SQL Data Warehouse
Följ dessa enkla steg för att läsa in och fråga exempeldatabasen för Adventure Works. Dessa skript använder sqlcmd först för att köra SQL som kommer att skapa tabeller och vyer. När tabeller har skapats, använder skripten bcp för att läsa in data.  Om du inte redan har sqlcmd och bcp installerat kan du följa dessa länkar till [installera bcp] [ install bcp] och [installera sqlcmd][install sqlcmd].

## <a name="load-sample-data"></a>Läs in exempeldata
1. Hämta den [Adventure Works-exempelskript för SQL Data Warehouse] [ Adventure Works Sample Scripts for SQL Data Warehouse] zip-filen.
2. Extrahera filerna från hämtade zip till en katalog på din lokala dator.
3. Redigera den hämtade filen aw_create.bat och ange följande variabler som finns längst upp i filen.  Se till att lämna inga blanksteg mellan den ”=” och parametern.  Nedan följer exempel på hur redigeringarna kan se ut.
   
    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```
4. Kör den redigerade aw_create.bat från en Windows-kommandotolk.  Var noga med att du är i katalogen där du sparade den redigerade versionen av aw_create.bat.
   Det här skriptet kommer...
   
   * Ta bort alla Adventure Works tabeller eller vyer som redan finns i databasen
   * Skapa Adventure Works-tabeller och vyer
   * Läsa in varje Adventure Works-tabell med bcp
   * Validera radantal för varje Adventure Works-tabell
   * Samla in statistik på varje kolumn för varje Adventure Works-tabell

## <a name="query-sample-data"></a>Fråga exempeldata
När du har läst in exempeldata i SQL Data Warehouse, kan du snabbt köra några frågor.  Om du vill köra en fråga, ansluta till din nyskapade Adventure Works-databas i Azure SQL DW med hjälp av Visual Studio och SSDT, enligt beskrivningen i den [fråga med Visual Studio] [ query with Visual Studio] dokumentet.

Exempel på enkla select-instruktion att hämta informationen som anställda:

```sql
SELECT * FROM DimEmployee;
```

Exempel på en mer komplex fråga med konstruktioner, till exempel GRUPPEN genom att titta på totalsumman för alla försäljning varje dag:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Exempel på en väljer med en WHERE-sats för att filtrera ut order från före ett visst datum:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse stöder nästan alla T-SQL-konstruktioner som har stöd för SQL Server.  Eventuella skillnader som finns dokumenterade i vår [Migrera koden] [ migrate code] dokumentation.

## <a name="next-steps"></a>Nästa steg
Nu när du har haft möjlighet att prova några frågor med exempeldata, kolla hur man [utveckla][develop], [ladda][load], eller [ Migrera] [ migrate] till SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[query with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrate code]: sql-data-warehouse-migrate-code.md
[install bcp]: sql-data-warehouse-load-with-bcp.md
[install sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works Sample Scripts for SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
