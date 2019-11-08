---
title: Spark-anslutning med Azure SQL Database och SQL Server
description: Lär dig hur du använder Spark-anslutningen för Azure SQL Database och SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: d398019b62078da6943cb98cbafc3ac39640513f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820879"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Påskynda real tids analys med Spark-anslutning för Azure SQL Database och SQL Server

Spark-anslutningen för Azure SQL Database och SQL Server aktiverar SQL-databaser, inklusive Azure SQL Database och SQL Server, för att fungera som indata-datakälla eller utgående data mottagare för Spark-jobb. Det gör att du kan använda transaktions data i real tid i stor data analys och bevara resultat för adhoc-frågor eller rapporter. Jämfört med den inbyggda JDBC-anslutningen ger den här anslutningen möjlighet att massredigera data i SQL-databaser. Det kan avsevärt rad-till-rad-infogning med 10X för att 20x snabbare prestanda. Spark-anslutningen för Azure SQL Database och SQL Server stöder även AAD-autentisering. Det gör att du på ett säkert sätt kan ansluta till din Azure SQL-databas från Azure Databricks med ditt AAD-konto. Den innehåller liknande gränssnitt med den inbyggda JDBC-anslutningen. Det är enkelt att migrera dina befintliga Spark-jobb till att använda den här nya anslutningen.

## <a name="download"></a>Ladda ned
Kom igång genom att hämta Spark till SQL DB Connector från [Azure-SQLDB-Spark-lagringsplatsen](https://github.com/Azure/azure-sqldb-spark) på GitHub.

## <a name="official-supported-versions"></a>Officiella versioner som stöds

| Komponent                            |Version                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 eller senare           |
| Scala                                |2,10 eller senare            |
| Microsoft JDBC-drivrutin för SQL Server |6,2 eller senare             |
| Microsoft SQL Server                 |SQL Server 2008 eller senare |
| Azure SQL Database                   |Stöds                |

Spark-anslutningen för Azure SQL Database och SQL Server använder Microsoft JDBC-drivrutinen för SQL Server för att flytta data mellan Spark-arbetsnoder och SQL-databaser:
 
Data flödet är följande:
1. Spark-huvudnoden ansluter till SQL Server eller Azure SQL Database och läser in data från en speciell tabell eller använder en speciell SQL-fråga
2. Spark-huvudnoden distribuerar data till arbetsnoder för omvandling. 
3. Worker-noden ansluter till SQL Server eller Azure SQL Database och skriver data till databasen. Användaren kan välja att använda rad-för-rad-infogning eller Mass infogning.

Följande diagram illustrerar data flödet.

   ![Arkitektur](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Bygg Spark till SQL DB Connector
För närvarande använder kopplings projektet Maven. Om du vill skapa kopplingen utan beroenden kan du köra:

- mvn Clean-paket
- Hämta de senaste versionerna av JAR från mappen release
- Ta med SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Ansluta Spark till SQL DB med anslutnings tjänsten
Du kan ansluta till Azure SQL Database eller SQL Server från Spark-jobb, läsa eller skriva data. Du kan också köra en DML-eller DDL-fråga i en Azure SQL-databas eller SQL Server databas.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Läs data från Azure SQL Database eller SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Läser data från Azure SQL Database eller SQL Server med angiven SQL-fråga
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
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Köra DML-eller DDL-fråga i Azure SQL Database eller SQL Server
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
Du kan ansluta till Azure SQL Database med hjälp av autentisering med Azure Active Directory (AAD). Använd AAD-autentisering för att centralt hantera identiteter för databas användare och som ett alternativ till att SQL Server autentisering.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Ansluta med ActiveDirectoryPassword-autentiseringsläge
#### <a name="setup-requirement"></a>Krav för installation
Om du använder ActiveDirectoryPassword-autentiseringsläget måste du ladda ned [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java-build-sökvägen.

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

### <a name="connecting-using-access-token"></a>Ansluter med åtkomsttoken
#### <a name="setup-requirement"></a>Krav för installation
Om du använder det åtkomst-token-baserade autentiseringsläget måste du ladda ned [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java-build-sökvägen.

Se [använda Azure Active Directory autentisering för autentisering med SQL Database](sql-database-aad-authentication.md) för att lära dig hur du får åtkomsttoken till din Azure SQL-databas.

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

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Skriva data till Azure SQL Database eller SQL Server med Mass infogning
Den traditionella JDBC-anslutningen skriver data till Azure SQL Database eller SQL Server med hjälp av rad-för-rad-infogning. Du kan använda Spark till SQL DB Connector för att skriva data till SQL Database med hjälp av Mass infogning. Det förbättrar markant skriv prestanda vid inläsning av stora data uppsättningar eller inläsning av data i tabeller där ett kolumn lagrings index används.

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
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Nästa steg
Om du inte redan har gjort det kan du hämta Spark-anslutaren för Azure SQL Database och SQL Server från [Azure-SQLDB-Spark GitHub-lagringsplatsen](https://github.com/Azure/azure-sqldb-spark) och utforska de ytterligare resurserna i lagrings platsen:

-   [Exempel på Azure Databricks antecknings böcker](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Exempel skript (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Du kanske också vill läsa [guiden Apache Spark SQL, DataFrames och data uppsättningar](https://spark.apache.org/docs/latest/sql-programming-guide.html) och [Azure Databricks-dokumentationen](https://docs.microsoft.com/azure/azure-databricks/).

