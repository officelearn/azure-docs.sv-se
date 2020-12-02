---
title: Anslut med SQLCMD
description: Använd kommando rads verktyget sqlcmd för att ansluta till och fråga en Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f61f2932ad9c6342a81ec05df335e9ea4d659fce
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461987"
---
# <a name="connect-to-sql-pool-in-azure-synapse-analytics-with-sqlcmd"></a>Ansluta till SQL-pool i Azure Synapse Analytics med SQLCMD

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Använd kommando rads verktyget [SQLCMD] [SQLCMD] för att ansluta till och fråga en SQL-pool.  

## <a name="1-connect"></a>1. Anslut

Kom igång med [SQLCMD] [SQLCMD] genom att öppna kommando tolken och ange **SQLCMD** följt av anslutnings STRÄNGEN för SQL-poolen. Anslutningssträngen kräver följande parametrar:

* **Server (-S):** Server i formatet `<`servernamn`>`. database.windows.net
* **Databas (-d):** Namn på SQL-pool.
* **Aktivera citerade identifierare (-I):** Identifierare med citat tecken måste vara aktiverade för att ansluta till en instans i SQL-poolen.

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

## <a name="2-query"></a>2. fråga

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

Mer information om tillgängliga alternativ i SQLCMD finns i [SQLCMD-dokumentationen](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
