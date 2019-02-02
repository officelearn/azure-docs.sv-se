---
title: Spark-anslutningen med Azure SQL Database och SQLServer | Microsoft Docs
description: Lär dig hur du använder Spark-anslutning för Azure SQL Database och SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 927b887dbe9214867c0a694a611ca9fd9dc647a2
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565300"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Påskynda i realtid stordataanalyser med Spark-anslutningsapp för Azure SQL Database och SQL Server

Spark-anslutningsapp för Azure SQL Database och SQL Server gör det möjligt för SQL-databaser, inklusive Azure SQL Database och SQL Server att fungera som indata käll- eller utdata mellanlagringsplats för Spark-jobb. Det kan du använda transaktionell data i analyser av stordata i realtid och bevara resultat för ad hoc-frågor eller rapporter. Den här anslutningen ger jämfört med den inbyggda JDBC-anslutningen, möjlighet att massinfoga data i SQL-databaser. Det kan överträffar rad för rad infogning med 10 x till 20 gånger snabbare prestanda. Spark-anslutningsapp för Azure SQL Database och SQL Server har även stöd för AAD-autentisering. Det gör du på ett säkert sätt ansluta till Azure SQL database från Azure Databricks med AAD-konto. Det ger liknande gränssnitt med den inbyggda JDBC-anslutningen. Det är enkelt att migrera dina befintliga Spark-jobb för att använda den här nya kopplingen.

## <a name="download"></a>Ladda ned
Kom igång genom att ladda ned Spark till SQL DB-anslutningen från den [lagringsplatsen för azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) på GitHub.

## <a name="official-supported-versions"></a>Official-versioner som stöds

| Komponent                            |Version                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 eller senare           |
| Scala                                |2,10 eller senare            |
| Microsoft JDBC-drivrutin för SQL Server |6.2 eller senare             |
| Microsoft SQL Server                 |SQLServer 2008 eller senare |
| Azure SQL Database                   |Stöds                |

Spark-anslutningsapp för Azure SQL Database och SQL Server använder Microsoft JDBC-drivrutinen för SQL Server för att flytta data mellan Spark arbetsnoder och SQL-databaser:
 
Dataflödet är följande:
1. Spark-huvudnoden ansluter till SQL Server eller Azure SQL Database och läser in data från en viss tabell eller använda en specifik SQL-fråga
2. Spark-huvudnoden distribuerar data till arbetsnoder för omvandling. 
3. Underordnad nod ansluter till SQL Server eller Azure SQL-databas och skriver data till databasen. Användaren kan välja att använda rad för rad infogning eller bulk insert.

Följande diagram illustrerar dataflödet.

   ![Arkitektur](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Skapa Spark kan SQL DB-anslutningsapp
För närvarande använder connector projektet maven. Du kan köra för att skapa anslutningen utan beroenden:

- mvn ren paket
- Ladda ned de senaste versionerna av den JAR-filen från mappen versionen
- Inkludera SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Anslut Spark till SQL DB med anslutningen
Du kan ansluta till Azure SQL Database eller SQL Server från Spark-jobb, läsa eller skriva data. Du kan också köra en DML- eller DDL-fråga i en Azure SQL database eller SQL Server-databas.

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
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Läsa data från Azure SQL Database eller SQL Server med angivna SQL-fråga
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
Om du använder ActiveDirectoryPassword autentiseringsläge, måste du ladda ned [azure-activedirectory-biblioteket-för-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java build path.

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

### <a name="connecting-using-access-token"></a>Ansluta med åtkomsttoken
#### <a name="setup-requirement"></a>Konfigurera krav
Om du använder åtkomstläge för tokenbaserad autentisering, måste du ladda ned [azure-activedirectory-biblioteket-för-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java build path.

Se [Använd Azure Active Directory-autentisering för autentisering med SQL Database](sql-database-aad-authentication.md) och lär dig att hämta åtkomsttoken till din Azure SQL-databas.

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
Den traditionella jdbc-anslutningen skriver data till Azure SQL database eller SQL Server med rad för rad infogning. Du kan använda Spark till SQL DB-anslutningsapp för att skriva data till SQL-databas med massinfogning. Avsevärt förbättrar prestanda för skrivåtgärder vid inläsning av stora datamängder eller läsa in data i tabeller där ett columnstore-index används.

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
Om du inte redan gjort hämta Spark-anslutningsapp för Azure SQL Database och SQL Server från [GitHub-lagringsplatsen för azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) och utforska ytterligare resurser i lagringsplatsen:

-   [Exemplet Azure Databricks-anteckningsböcker](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Exempel på skript (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Du kan också gå igenom den [Apache Spark SQL och DataFrames datauppsättningar Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) och [dokumentation för Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

