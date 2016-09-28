<properties
   pageTitle="Läs in data från SQL Server i Azure SQL Data Warehouse (bcp) | Microsoft Azure"
   description="För små datastorlekar, använder du bcp för att exportera data från SQL Server till flat-filer som sedan importeras direkt till Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>



# Läs in data från SQL Server till Azure SQL Data Warehouse (flat-filer)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

För små datauppsättningar, kan du använda kommandoradsverktyget bcp för att exportera data från SQL Server och läsa in den direkt i Azure SQL Data Warehouse.

I de här självstudierna kommer du att använda bcp för att:

- Exportera en tabell från SQL Server med hjälp av bcp out-kommandot (eller genom att skapa en enkel exempelfil)
- Importera tabellen från en flat-fil till SQL Data Warehouse.
- Skapa statistik på inlästa data.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Innan du börjar

### Förutsättningar

För att gå igenom de här självstudierna, behöver du:

- En SQL Data Warehouse-databas
- Kommandoradsverktyget bcp installerat
- Kommandoradsverktyget sqlcmd installerat

Du kan hämta verktygen bcp och sqlcmd från [Microsoft Download Center][].

### Data i ASCII- eller UTF-16-format

Om du använder egna data i självstudierna, måste de använda sig av ASCII- eller UTF-16-kodning eftersom bcp inte stöder UTF-8. 

PolyBase stöder UTF-8 men har inte än stöd för UTF-16. Observera att om du vill kombinera bcp med PolyBase, behöver du transformera dina data till UTF-8 efter att de exporterats från SQL Server. 


## 1. Skapa en måltabell

Definiera en tabell i SQL Data Warehouse som kommer att vara måltabell för inläsningen. Kolumnerna i tabellen måste motsvara data i varje rad i din datafil.

För att skapa en tabell, öppnar du en kommandotolk och använder sqlcmd.exe för att köra följande kommando:


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

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

## 4. Skapa statistik

SQL Data Warehouse stöder inte automatiskt skapande eller uppdatering av statistik ännu. För att få bästa möjliga prestanda från dina frågor, är det viktigt att skapa statistik på alla kolumner i alla tabeller efter den första inläsningen eller vid betydande dataändringar. En detaljerad förklaring av statistik finns i [Statistik][]. 

Kör följande kommando för att skapa statistik på din nya inlästa tabell.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## 5. Exportera data från SQL Data Warehouse
För skojs skull kan du exportera de data du just läste in, tillbaks ut ur SQL Data Warehouse.  Kommandot för att exportera är exakt detsamma som för att exportera från SQL Server.

Resultatet är dock något annorlunda. Eftersom data lagras på distribuerade platser inom SQL Data Warehouse så skriver varje Compute-nod sina data till utdatafilen när du exporterar data. Dataordningen i utdatafilen skiljer sig med stor sannolikhet åt från dataordningen i indatafilen.

### Exportera en tabell och jämför exporterade resultat

Om du vill se exporterade data öppnar du en kommandotolk och kör det här kommandot med dina egna parametrar. ServerName är namnet på din logiska Azure SQL Server.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Du kan verifiera att data exporterats korrekt genom att öppna den nya filen. Data i filen bör matcha texten nedan, men kommer antagligen vara sorterad i en annan ordning:

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

### Exportera resultatet av en fråga

Du kan använda bcp-funktionen **queryout** för att exportera resultaten av en fråga istället för att exportera hela tabellen. 

## Nästa steg
Se [Läs in data till SQL Data Warehouse][] för en översikt över inläsning.
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling][].
Se [Tabellöversikt][] eller [CREATE TABLE-syntax][] för ytterligare information om hur man skapar en tabell i SQL Data Warehouse.

<!--Image references-->

<!--Article references-->

[Läs in data till SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Översikt över SQL Data Warehouse-utveckling]: ./sql-data-warehouse-overview-develop.md
[Tabellöversikt]: ./sql-data-warehouse-tables-overview.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE-syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Sep16_HO3-->


