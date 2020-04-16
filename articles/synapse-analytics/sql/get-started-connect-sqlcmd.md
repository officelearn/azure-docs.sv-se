---
title: Ansluta till Synapse SQL med sqlcmd
description: Använd kommandoradsverktyget sqlcmd för att ansluta till och fråga SQL on-demand (preview) och SQL-pool.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8ff9034e6c31c8d95e862570e3962990dfec8442
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423756"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Ansluta till Synapse SQL med sqlcmd

> [!div class="op_single_selector"]
>
> * [Azure Data Studio (förhandsversion)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Du kan använda kommandoradsverktyget [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att ansluta till och fråga SQL on-demand (förhandsversion) och SQL-pool i Synapse SQL.  

## <a name="1-connect"></a>1. Anslut
För att komma igång med [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)öppnar du kommandotolken och anger **sqlcmd** följt av anslutningssträngen för Synapse SQL-databasen. Anslutningssträngen kräver följande parametrar:

* **Server (-S):** Server i formatet `<`servernamn`>`. database.windows.net
* **Databas (-d):** Databasnamn
* **Aktivera citerade identifierare (-I):** Citerade identifierare måste vara aktiverade för att ansluta till en Synapse SQL-instans

Om du vill använda SQL Server-autentisering måste du lägga till parametrar för användarnamn och lösenord:

* **Användare (-U):** Serveranvändare i formatet `<`Användare`>`
* **Lösenord (-P):** Lösenord som är associerat med användaren

Anslutningssträngen kan se ut som följande exempel:

**SQL på begäran**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**SQL-pool**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Om du vill använda Azure Active Directory-integrerad autentisering måste du lägga till Azure Active Directory-parametrar:

* **Azure Active Directory-autentisering (-G):** använder Azure Active Directory för autentisering

Anslutningssträngen kan se ut som på följande exempel:

**SQL på begäran**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**SQL-pool**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Du måste [aktivera Azure Active Directory-autentisering](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för att kunna autentisera med Active Directory.

## <a name="2-query"></a>2. Fråga

### <a name="use-sql-pool"></a>Använda SQL-pool

Du kan utfärda alla Transact-SQL-uttryck som stöds mot instansen efter anslutning.  I det här exemplet skickas frågor i interaktivt läge:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

För SQL-pool visar följande exempel hur du kör frågor i batch-läge med alternativet -Q eller rör din SQL till sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-sql-on-demand"></a>Använda SQL på begäran

När du har anslutit kan du utfärda alla [Transact-SQL-satser (Transact-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) som stöds mot instansen.  I följande exempel skickas frågor i interaktivt läge:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

För SQL on-demand visar exemplen som följer hur du kör frågor i batch-läge med alternativet -Q eller rör din SQL till sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Nästa steg

Mer information om sqlcmd-alternativ finns i [sqlcmd-dokumentationen](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
