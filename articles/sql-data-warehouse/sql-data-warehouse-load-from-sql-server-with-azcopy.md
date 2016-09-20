<properties
   pageTitle="Läs in data från SQL Server till Azure SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Använder sig av bcp för att exportera data från SQL Server till flat-filer, AZCopy för att importera data till Azure blobblagring och PolyBase för att mata in data i Azure SQL Data Warehouse."
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


# Läs in data från SQL Server till Azure SQL Data Warehouse (AZCopy)

Använd kommandoradsverktygen bcp och AZCopy för att läsa in data från SQL Server till Azure blobblagring. Använd sedan PolyBase eller Azure Data Factory för att läsa in data till Azure SQL Data Warehouse. 


## Krav

För att gå igenom de här självstudierna, behöver du:

- En SQL Data Warehouse-databas
- Kommandoradsverktyget bcp installerat
- Kommandoradsverktyget SQLCMD installerat

>[AZURE.NOTE] Du kan hämta verktygen bcp och sqlcmd från [Microsoft Download Center][].

## Importera data till SQL Data Warehouse

I de här självstudierna kommer du att skapa en tabell i Azure SQL Data Warehouse och importera data till tabellen.

### Steg 1: Skapa en tabell i Azure SQL Data Warehouse

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

>[AZURE.NOTE] Se [Tabellöversikt][] eller [CREATE TABLE-syntax][] för ytterligare information om hur man skapar en tabell i SQL Data Warehouse och alternativen som finns i WITH-satsen.

### Steg 2: Skapa en källdatafil

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

> [AZURE.NOTE] Det är viktigt att komma ihåg att bcp.exe inte har stöd för UTF-8 filkodning. Använd ASCII-filer eller UTF-16-kodade filer när du använder bcp.exe.

### Steg 3: Anslut och importera data
Med bcp kan du ansluta och importera data med hjälp av följande kommando, där du ersätter värdena med lämpliga sådana:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Du kan verifiera att data lästs in genom att köra följande fråga med hjälp av sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Det ska returnera följande resultat:

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

### Steg 4: Skapa statistik på dina nyinlästa data

SQL Data Warehouse stöder inte än automatiskt skapande eller uppdatering av statistik. För att få bästa möjliga prestanda från dina frågor, är det viktigt att statistik skapas på alla kolumner i alla tabeller efter den första inläsningen eller vid betydande dataändringar. En detaljerad förklaring av statistik finns i ämnet [Statistik][] i ämnesgruppen Utveckla. Nedan följer ett enkelt exempel på hur du skapar statistik på tabellerna som lästs in i det här exemplet

Kör följande CREATE STATISTICS-uttryck från en sqlcmd-kommandotolk:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## Exportera data från SQL Data Warehouse
I de här självstudierna kommer du att skapa en datafil från en tabell i SQL Data Warehouse. Vi kommer att exportera de data vi skapade ovan till en ny datafil som heter DimDate2_export.txt.

### Steg 1: Exportera data

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

>[AZURE.NOTE] På grund av hur distribuerade system fungerar, är det möjligt att dataordningen inte är samma i alla SQL Data Warehouse-databaser. Ett annat alternativ är att använda **queryout**-funktionen i bcp för att skriva ett fråge-extrakt istället för att exportera hela tabellen.

## Nästa steg
Se [Läs in data till SQL Data Warehouse][] för en översikt över inläsning.
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling][].

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



<!--HONumber=sep16_HO1-->


