---
title: Spark Connector med Azure SQL Database och SQL Server
description: Lär dig hur du använder Spark Connector för Azure SQL Database och SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b22ec475c0281a54d65921bc450b35723aa23219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471660"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Accelerera stordataanalys i realtid med Spark-anslutning för Azure SQL Database och SQL Server

Spark-anslutningen för Azure SQL Database och SQL Server gör det möjligt för SQL-databaser, inklusive Azure SQL Database och SQL Server, att fungera som indatakälla eller utdatamottagare för Spark-jobb. Det gör att du kan använda transaktionsdata i realtid i stordataanalys och spara resultat för adhoc-frågor eller rapportering. Jämfört med den inbyggda JDBC-kopplingen ger den här anslutningen möjlighet att massinskära data i SQL-databaser. Det kan överträffa rad för rad insättning med 10x till 20x snabbare prestanda. Spark-anslutningen för Azure SQL Database och SQL Server stöder också AAD-autentisering. Det gör att du säkert ansluter till din Azure SQL-databas från Azure Databricks med ditt AAD-konto. Det ger liknande gränssnitt med den inbyggda JDBC-kontakten. Det är enkelt att migrera dina befintliga Spark-jobb för att använda den här nya kopplingen.

## <a name="download"></a>Ladda ned
För att komma igång, ladda ner Spark till SQL [DB-anslutningen från azure-sqldb-spark-databasen](https://github.com/Azure/azure-sqldb-spark) på GitHub.

## <a name="official-supported-versions"></a>Officiella versioner som stöds

| Komponent                            | Version                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 eller senare           |
| Scala                                | 2.10 eller senare            |
| Microsoft JDBC-drivrutin för SQL Server | 6.2 eller senare             |
| Microsoft SQL Server                 | SQL Server 2008 eller senare |
| Azure SQL Database                   | Stöds                |

Spark-anslutningen för Azure SQL Database och SQL Server använder Microsoft JDBC Driver för SQL Server för att flytta data mellan Spark-arbetsnoder och SQL-databaser:
 
Dataflödet är följande:
1. Spark-huvudnoden ansluter till SQL Server eller Azure SQL Database och läser in data från en viss tabell eller med hjälp av en specifik SQL-fråga
2. Spark-huvudnoden distribuerar data till arbetsnoder för omvandling. 
3. Arbetarnoden ansluter till SQL Server eller Azure SQL Database och skriver data till databasen. Användaren kan välja att använda infogning från rad eller bulkinfogning.

Följande diagram illustrerar dataflödet.

   ![Arkitektur](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Skapa Spark till SQL DB-anslutning
För närvarande använder anslutningsprojektet maven. Om du vill skapa kopplingen utan beroenden kan du köra:

- mvn ren förpackning
- Ladda ner de senaste versionerna av JAR från releasemappen
- Inkludera SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Anslut Spark till SQL DB med hjälp av kopplingen
Du kan ansluta till Azure SQL Database eller SQL Server från Spark-jobb, läsa eller skriva data. Du kan också köra en DML- eller DDL-fråga i en Azure SQL-databas eller SQL Server-databas.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Läsa data från Azure SQL Database eller SQL Server

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
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Läsa data från Azure SQL Database eller SQL Server med angiven SQL-fråga
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
val collection = sqlContext.read.sqlDB(config)
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

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Köra DML- eller DDL-fråga i Azure SQL Database eller SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Ansluta Spark till Azure SQL Database med AAD-autentisering
Du kan ansluta till Azure SQL Database med Azure Active Directory (AAD) autentisering. Använd AAD-autentisering för att centralt hantera identiteter för databasanvändare och som ett alternativ till SQL Server-autentisering.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Ansluta med ActiveDirectoryPassword-autentiseringsläge
#### <a name="setup-requirement"></a>Krav på inställning
Om du använder autentiseringsläget ActiveDirectoryPassword måste du hämta [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java-byggsökvägen.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Ansluta med åtkomsttoken
#### <a name="setup-requirement"></a>Krav på inställning
Om du använder åtkomsttokenbaserade autentiseringsläge måste du hämta [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) och dess beroenden och inkludera dem i Java-byggsökvägen.

Se [Använda Azure Active Directory-autentisering för autentisering med SQL Database för](sql-database-aad-authentication.md) att lära dig hur du får åtkomsttoken till din Azure SQL-databas.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Skriva data till Azure SQL-databas eller SQL Server med massinfogning
Den traditionella jdbc-anslutningen skriver data till Azure SQL-databas eller SQL Server med hjälp av infogning rad för rad. Du kan använda Spark till SQL DB-anslutning för att skriva data till SQL-databasen med massinfogning. Det förbättrar skrivprestanda avsevärt när stora datauppsättningar läses in eller läses in data i tabeller där ett kolumnarkivindex används.

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
Om du inte redan har gjort det hämtar du Spark-anslutningen för Azure SQL Database och SQL Server från [Azure-sqldb-spark GitHub-databasen](https://github.com/Azure/azure-sqldb-spark) och utforskar ytterligare resurser i repo:

- [Exempel på Azure Databricks-anteckningsböcker](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Exempel på skript (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Du kanske också vill granska [Apache Spark SQL, DataFrames och Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) och Azure [Databricks-dokumentationen](https://docs.microsoft.com/azure/azure-databricks/).

