---
title: Läs in data från CSV-filen till Azure SQL Database (bcp) | Microsoft Docs
description: För mindre datastorlekar används bcp för att importera data till Azure SQL Database.
services: sql-database
documentationcenter: NA
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 09/13/2016
ms.author: carlrab

---
# Läs in data från CSV till Azure SQL Data Warehouse (flat-filer)
Du kan använda kommandoradsverktyget bcp för att importera data från en CSV-fil till Azure SQL Database.

## Innan du börjar
### Förutsättningar
För att gå igenom de här självstudierna, behöver du:

* Skapa en logisk Azure SQL Database-server och -databas
* Kommandoradsverktyget bcp installerat
* Kommandoradsverktyget sqlcmd installerat

Du kan hämta verktygen bcp och sqlcmd från [Microsoft Download Center][Microsoft Download Center].

### Data i ASCII- eller UTF-16-format
Om du använder egna data i självstudierna, måste de använda sig av ASCII- eller UTF-16-kodning eftersom bcp inte stöder UTF-8. 

## 1. Skapa en måltabell
Definiera en tabell i SQL Database som måltabellen. Kolumnerna i tabellen måste motsvara data i varje rad i din datafil.

För att skapa en tabell, öppnar du en kommandotolk och använder sqlcmd.exe för att köra följande kommando:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## 2. Skapa en källdatafil
Öppna Anteckningar och kopiera följande datarader till en ny textfil. Spara sedan filen till din lokala temp-katalog, C:\Temp\DimDate2.txt. Den här datan är i ASCII-format.

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

(Valfritt) Om du vill exportera dina egna data från en SQL Server-databas, öppnar du en kommandotolk och kör följande kommando. Ersätt TableName, ServerName, DatabaseName, Username och Password med din egen information.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```

## 3. Läs in data
För att läsa in data, öppnar du en kommandotolk och kör följande kommando, där du ersätter värdena för servernamn, databasnamn, användarnamn och lösenord med din egen information.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Använd det här kommandot för att verifiera att data har lästs in korrekt

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Resultatet borde se ut så här:

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

## Nästa steg
Om du vill migrera en SQL Server-databas hittar du mer information i [SQL Server-databasmigrering](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE-syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Sep16_HO3-->


