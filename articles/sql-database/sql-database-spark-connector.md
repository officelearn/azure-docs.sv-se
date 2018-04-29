---
title: Väck koppling med Azure SQL Database och SQLServer | Microsoft Docs
description: Lär dig hur du använder Spark-anslutning för Azure SQL Database och SQL Server
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: ''
ms.topic: article
ms.date: 04/23/2018
ms.author: xiwu
ms.openlocfilehash: 393af463c4145e1d865c14f2ace7d5123ab12cfa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Påskynda realtid stordata med Spark connector för Azure SQL Database och SQL Server

Spark-connector för Azure SQL Database och SQL Server kan SQL-databaser, inklusive Azure SQL Database och SQL Server att fungera som mottagare för indata-käll- eller utdata data för Spark-jobb. Det kan du använda transaktionell realtidsdata i stordata och spara resultaten för ad hoc-frågor eller rapportering. Den här anslutningen ger jämfört med den inbyggda JDBC-anslutningen, dig möjlighet att massinfoga data i SQL-databaser. Det kan överträffar rad för rad infogning med 10 x till 20 x snabbare prestanda. Spark-anslutningen till Azure SQL Database och SQL Server stöder också AAD-autentisering. Det gör du på ett säkert sätt ansluta till Azure SQL database från Azure Databricks med AAD-konto. Det ger liknande gränssnitt med den inbyggda JDBC-anslutningen. Det är enkelt att migrera dina befintliga Spark-jobb för att använda den här nya anslutningen.

## <a name="download"></a>Ladda ned
Kom igång genom att ladda ned Spark till SQL DB-anslutningen från den [azure sqldb spark databasen](https://github.com/Azure/azure-sqldb-spark) på GitHub.

## <a name="official-supported-versions"></a>Officiella versioner som stöds

| Komponent                            |Version                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 eller senare           |
| Scala                                |2,10 eller senare            |
| Microsoft JDBC-drivrutin för SQL Server |6.2 eller senare             |
| Microsoft SQL Server                 |SQLServer 2008 eller senare |
| Azure SQL Database                   |Stöds                |

Spark-anslutningen till Azure SQL Database och SQL Server använder Microsoft JDBC-drivrutinen för SQL Server för att flytta data mellan Spark arbetarnoder och SQL-databaser:
 
I dataströmmen är följande:
1. Noden som Spark ansluter till SQL Server eller Azure SQL Database och läser in data från en viss tabell eller med en specifik SQL-fråga
2. Noden som Spark distribuerar data till arbetsnoder för omvandling. 
3. Arbetsnoden ansluter till SQL Server eller Azure SQL-databas och skriver data till databasen. Användaren kan välja att använda rad för rad infogning eller bulk insert.

Följande diagram illustrerar dataflödet.

   ![Arkitektur](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Skapa Spark till SQL DB-koppling
För närvarande använder connector projektet maven. Du kan köra för att skapa anslutningen utan beroenden:

- mvn ren paketet
- Hämta de senaste versionerna av JAR från mappen versionen
- Inkludera SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Ansluta Spark till SQL-databas med hjälp av connector
Du kan ansluta till Azure SQL Database eller SQL Server från Spark jobb, läsa eller skriva data. Du kan också köra en DML- eller DDL-fråga i en Azure SQL database eller SQL Server-databas.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Läsa data från Azure SQL Database eller SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Läser data från Azure SQL Database eller SQL Server med den angivna SQL-fråga
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Skriva data till Azure SQL Database eller SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Kör DML- eller DDL-fråga i Azure SQL Database eller SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Ansluta Spark till Azure SQL Database med AAD-autentisering
Du kan ansluta till Azure SQL Database med Azure Active Directory (AAD)-autentisering. Använd AAD-autentisering för att centralt hantera identiteter för databasanvändare och som ett alternativ till SQL Server-autentisering.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Ansluta med ActiveDirectoryPassword autentiseringsläge
#### <a name="setup-requirement"></a>Konfigurera krav
Om du använder ActiveDirectoryPassword autentiseringsläge, måste du hämta [azure activedirectory-biblioteket-för-java-](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java byggsökväg.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Ansluta med åtkomst-Token
#### <a name="setup-requirement"></a>Konfigurera krav
Om du använder token-baserad autentisering åtkomstläge, måste du hämta [azure activedirectory-biblioteket-för-java-](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java byggsökväg.

Se [Använd Azure Active Directory-autentisering för autentisering med SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) att lära dig hur du får åtkomst till Azure SQL database-token.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Skriva data till Azure SQL database eller SQL Server med Bulk Insert
Den traditionella jdbc-anslutningen skriver data till Azure SQL database eller SQL Server med rad för rad infogning. Du kan använda Spark till SQL DB-anslutningstjänsten för att skriva data till SQL-databas med massinfogning. Skrivprestanda förbättras avsevärt när inläsning av stora datamängder eller läsa in data i tabeller där ett columnstore-index används.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Nästa steg
Om du inte redan gjort hämta Spark-kopplingen för Azure SQL Database och SQL Server från [azure sqldb spark GitHub-lagringsplatsen](https://github.com/Azure/azure-sqldb-spark) och utforska ytterligare resurser i lagringsplatsen:

-   [Exemplet Azure Databricks bärbara datorer](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Exempel på skript (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Du kanske också vill granska den [datauppsättningar Guide, DataFrames och Apache Spark SQL](http://spark.apache.org/docs/latest/sql-programming-guide.html) och [Azure Databricks dokumentationen](https://docs.microsoft.com/azure/azure-databricks/).

