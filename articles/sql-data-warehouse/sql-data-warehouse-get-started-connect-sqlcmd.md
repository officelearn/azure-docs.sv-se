---
title: "Fråga Azure SQL Data Warehouse (sqlcmd)| Microsoft Docs"
description: "Fråga Azure SQL Data Warehouse med kommandoradsverktyget sqlcmd."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 6e2b69e5-4806-4e91-9ea1-e2b63bf28c46
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1f2cf8003e46a1df30810a2594bc1d380bc13bcf


---
# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>Fråga Azure SQL Data Warehouse (sqlcmd)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Den här genomgången använder kommandoradsverktyget [sqlcmd][sqlcmd] för att fråga en Azure SQL Data Warehouse.  

## <a name="1-connect"></a>1. Anslut
Du kommer igång med [sqlcmd][sqlcmd] genom att öppna kommandotolken och ange **sqlcmd** följt av anslutningssträngen för din SQL Data Warehouse-databas. Anslutningssträngen kräver följande parametrar:

* **Server (-S):** Server i formatet `<`servernamn`>`. database.windows.net
* **Databas (-d):** Databasens namn.
* **Aktivera identifierare inom citattecken (-I):** Identifierare inom citattecken måste vara aktiverat för att kunna ansluta till en instans av SQL Data Warehouse.

Om du vill använda SQL Server-autentisering måste du lägga till användarnamn/lösenordsparametrar:

* **Användare (-U):** Serveranvändare i formatet `<`Användare`>`
* **Lösenord (-P):** Lösenord som är associerat med användaren.

Anslutningssträngen kan se ut ungefär så här:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Om du vill använda Azure Active Directory-integrerad autentisering måste du lägga till Azure Active Directory-parametrar:

* **Azure Active Directory-autentisering (-G):** använder Azure Active Directory för autentisering

Anslutningssträngen kan se ut ungefär så här:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Du måste [aktivera Azure Active Directory-autentisering](sql-data-warehouse-authentication.md) för att kunna autentisera med Active Directory.
> 
> 

## <a name="2-query"></a>2. Fråga
Du kan utfärda alla Transact-SQL-uttryck som stöds mot instansen efter anslutning.  I det här exemplet skickas frågor i interaktivt läge.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Dessa nästa exempel visar hur du kan köra frågor i batch-läge med alternativet -Q eller skicka din SQL till sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Nästa steg
Se [sqlcmd-dokumentationen][sqlcmd] för mer information om tillgängliga alternativ i sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure Portal]: https://portal.azure.com

<!--Other Web references-->



<!--HONumber=Nov16_HO2-->


