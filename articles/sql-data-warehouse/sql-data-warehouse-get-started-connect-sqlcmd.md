<properties
   pageTitle="Komma igång: Anslut till Azure SQL Data Warehouse | Microsoft Azure"
   description="Kom igång med att ansluta till SQL Data Warehouse och köra några frågor."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/16/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Anslut och fråga med SQLCMD

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)


Den här genomgången visar hur du ansluter och frågar en Azure SQL Data Warehouse-databas på bara några minuter med hjälp av verktyget sqlcmd.exe. I den här genomgången kommer du att:

+ Installera nödvändig programvara
+ Ansluta till en databas som har AdventureWorksDW som exempeldatabas
+ Köra en fråga mot exempeldatabasen  

## Förutsättningar

+ Information om att hämta [sqlcmd.exe][], finns i [Microsoft Command Line Utilities 11 för SQL Server][].

## Hämta det fullständigt kvalificerade Azure SQL-servernamnet

För att ansluta till din databas måste du ha det fullständiga namnet på servern (***servernamn**.database.windows.net*) som innehåller den databas du vill ansluta till.

1. Gå till [Azure-portalen][].
2. Bläddra till den databas du vill ansluta till.
3. Leta upp det fullständiga servernamnet (vi använder denna information i stegen nedan):

![][1]


## Anslut till SQL Data Warehouse med sqlcmd

För att ansluta till en specifik instans av SQL Data Warehouse när du använder sqlcmd du behöver öppna kommandotolken och ange **sqlcmd** följt av anslutningssträngen för SQL Data Warehouse-databasen. Anslutningssträngen måste ha följande parametrar:

+ **Server (-S):** Server i formatet `<`servernamn`>`. database.windows.net
+ **Databas (-d):** Databasens namn.
+ **Användare (-U):** Serveranvändare i formatet `<`Användare`>`
+ **Lösenord (-P):** Lösenord som är associerat med användaren.
+ **Aktivera identifierare inom citattecken (-I):** Identifierare inom citattecken måste vara aktiverat för att kunna ansluta till en instans av SQL Data Warehouse.

För att ansluta till en instans av SQL Data Warehouse, anger du därför följande:

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Kör exempelfrågor

Du kan utfärda alla Transact-SQL-uttryck som stöds mot instansen efter anslutning.

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Mer information om sqlcmd finns i [sqlcmd-dokumentationen][sqlcmd.exe].


## Nästa steg

Du när du kan ansluta och fråga, kan du prova [anslutning med PowerBI][].

För att konfigurera din miljö för Windows-autentisering, se [Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering][].

<!--Articles-->
[Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[anslutning med PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/en-us/library/ms162773.aspx
[Microsoft Command Line Utilities 11 för SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure-portalen]: https://portal.azure.com

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png



<!--HONumber=Jun16_HO2-->


