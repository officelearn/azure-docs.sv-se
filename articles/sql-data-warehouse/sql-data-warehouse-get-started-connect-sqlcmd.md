---
title: Ansluta till Azure SQL Data Warehouse sqlcmd | Microsoft Docs
description: Använd kommando rads verktyget sqlcmd för att ansluta till och fråga en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f3b93660fb9f8f3b0bfdddc37105b9e998ed9eee
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479510"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Anslut till SQL Data Warehouse med sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Använd kommando rads verktyget [SQLCMD][sqlcmd] för att ansluta till och fråga en Azure SQL Data Warehouse.  

## <a name="1-connect"></a>1. Anslut
Du kommer igång med [sqlcmd][sqlcmd] genom att öppna kommandotolken och ange **sqlcmd** följt av anslutningssträngen för din SQL Data Warehouse-databas. Anslutningssträngen kräver följande parametrar:

* **Server (-S):** Server i formatet `<`Server namn`>`. Database.Windows.net
* **Databas (-d):** Databas namn.
* **Aktivera citerade identifierare (-I):** Identifierare med citat tecken måste vara aktiverade för att ansluta till en SQL Data Warehouse-instans.

Om du vill använda SQL Server-autentisering måste du lägga till användarnamn/lösenordsparametrar:

* **Användare (-U):** Server användare i formulär `<`användaren`>`
* **Lösen ord (-P):** Lösen ordet som är kopplat till användaren.

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

## <a name="2-query"></a>2. Söka i data
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
Mer information om tillgängliga alternativ i SQLCMD finns i [SQLCMD-dokumentationen][sqlcmd] .

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
