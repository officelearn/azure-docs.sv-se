---
title: Importera och exportera data mellan Spark-pooler (förhandsversion) och SQL-pooler
description: Den här artikeln innehåller information om hur du använder den anpassade kopplingen för att flytta data fram och tillbaka mellan SQL-pooler och Spark-pooler (förhandsversion).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424295"
---
# <a name="introduction"></a>Introduktion

Spark SQL Analytics Connector är utformad för att effektivt överföra data mellan Spark-pool (förhandsversion) och SQL-pooler i Azure Synapse. Spark SQL Analytics Connector fungerar endast på SQL-pooler, det fungerar inte med SQL on-Demand.

## <a name="design"></a>Design

Överföring av data mellan Spark-pooler och SQL-pooler kan göras med JDBC. Med tanke på två distribuerade system som Spark- och SQL-pooler tenderar JDBC att vara en flaskhals med seriella dataöverföring.

Spark-poolerna till SQL Analytics Connector är en implementering av datakällan för Apache Spark. Den använder Azure Data Lake Storage Gen 2 och Polybase i SQL-pooler för att effektivt överföra data mellan Spark-klustret och SQL Analytics-instansen.

![Anslutningsarkitektur](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autentisering i Azure Synapse Analytics

Autentisering mellan system görs sömlöst i Azure Synapse Analytics. Det finns en tokentjänst som ansluter till Azure Active Directory för att hämta säkerhetstoken för användning när du öppnar lagringskontot eller informationslageriet. Därför behöver du inte skapa autentiseringsuppgifter eller ange dem i anslutnings-API:et så länge AAD-Auth har konfigurerats på lagringskontot och informationslaglagerservern. Om inte kan SQL Auth anges. Mer information finns i avsnittet [Användning.](#usage)

## <a name="constraints"></a>Villkor

- Den här kopplingen fungerar bara i Scala.

## <a name="prerequisites"></a>Krav

- Har **db_exporter** roll i den databas/SQL-pool som du vill överföra data till/från.

Om du vill skapa användare ansluter du till databasen och följer följande exempel:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Så här tilldelar du en roll:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Användning

Importsatserna behöver inte tillhandahållas, de är förimporterade för den bärbara datorn.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Överföra data till eller från en SQL-pool i den logiska serverinstansen (DW-instans) som är kopplad till arbetsytan

> [!NOTE]
> **Import behövs inte i notebook experience**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Läs API

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

Ovanstående API fungerar för både interna (hanterade) och externa tabeller i SQL-poolen.

#### <a name="write-api"></a>Skriv API

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

där TableType kan vara Constants.INTERNAL eller Constants.EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Autentiseringen till lagring och SQL Server görs

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Om du överför data till eller från en SQL-pool eller databas i en logisk server utanför arbetsytan

> [!NOTE]
> Import behövs inte i notebook experience

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Läs API

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Skriv API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Använda SQL Auth i stället för AAD

#### <a name="read-api"></a>Läs API

För närvarande stöder kopplingen inte tokenbaserad autentisering till en SQL-pool som ligger utanför arbetsytan. Du måste använda SQL Auth.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Skriv API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Använda PySpark-kontakten

> [!NOTE]
> Det här exemplet ges med endast den anteckningsboksupplevelse som hålls i åtanke.

Anta att du har en dataram "pyspark_df" som du vill skriva till DW.

Skapa en temp-tabell med dataramen i PySpark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Kör en Scala-cell i pysparksanteckningsboken med magi

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
På samma sätt läser du data med Scala och skriver dem i en temporär tabell i lässcenariot och använder Spark SQL i PySpark för att fråga temp-tabellen i en dataram.

## <a name="next-steps"></a>Nästa steg

- [Skapa en SQL-pool]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Skapa en ny Apache Spark-pool för en Azure Synapse Analytics-arbetsyta](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 