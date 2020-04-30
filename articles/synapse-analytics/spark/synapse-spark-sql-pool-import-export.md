---
title: Importera och exportera data mellan Spark-pooler (för hands version) och SQL-pooler
description: Den här artikeln innehåller information om hur du använder det anpassade anslutnings programmet för att flytta data fram och tillbaka mellan SQL-pooler och Spark-pooler (för hands version).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424295"
---
# <a name="introduction"></a>Introduktion

Spark SQL Analytics-anslutningen är utformad för att effektivt överföra data mellan Spark-pool (för hands version) och SQL-pooler i Azure Synapse. Spark SQL Analytics-anslutaren fungerar bara på SQL-pooler, den fungerar inte med SQL på begäran.

## <a name="design"></a>Design

Överföring av data mellan Spark-pooler och SQL-pooler kan göras med JDBC. Men med tanke på två distribuerade system som Spark-och SQL-pooler, är JDBC att vara en Flask hals med seriell data överföring.

Spark-poolerna till SQL Analytics Connector är en implementering av data källor för Apache Spark. Den använder Azure Data Lake Storage gen 2 och PolyBase i SQL-pooler för att effektivt överföra data mellan Spark-klustret och SQL Analytics-instansen.

![Kopplings arkitektur](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autentisering i Azure Synapse Analytics

Autentisering mellan system sker sömlöst i Azure Synapse Analytics. Det finns en token-tjänst som ansluter med Azure Active Directory för att hämta säkerhetstoken som ska användas vid åtkomst till lagrings kontot eller data lager servern. Därför behöver du inte skapa autentiseringsuppgifter eller ange dem i anslutnings-API: et så länge AAD-auth är konfigurerat på lagrings kontot och på data lager servern. Annars kan SQL-autentisering anges. Mer information finns i [användnings](#usage) avsnittet.

## <a name="constraints"></a>Villkor

- Den här kopplingen fungerar bara i Scala.

## <a name="prerequisites"></a>Krav

- Ha **db_exporter** -rollen i databasen/SQL-poolen som du vill överföra data till/från.

Om du vill skapa användare ansluter du till databasen och följer de här exemplen:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Så här tilldelar du en roll:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Användning

Import instruktionerna behöver inte tillhandahållas, de är redan importerade för den bärbara datorn.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Överföring av data till eller från en SQL-pool på den logiska servern (DW-instans) som är kopplad till arbets ytan

> [!NOTE]
> **Importer som inte behövs i Notebook-upplevelsen**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Läs-API

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

Ovanstående API fungerar både för interna (hanterade) och externa tabeller i SQL-poolen.

#### <a name="write-api"></a>Skriv-API

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

där TableType kan vara konstanter. INTERNAL eller constants. EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Autentiseringen till lagringen och SQL Server görs

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Om du överför data till eller från en SQL-pool eller databas på en logisk server utanför arbets ytan

> [!NOTE]
> Importer som inte behövs i Notebook-upplevelsen

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Läs-API

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Skriv-API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Använda SQL-autentisering i stället för AAD

#### <a name="read-api"></a>Läs-API

För närvarande stöder anslutningen inte tokenbaserad autentisering till en SQL-pool utanför arbets ytan. Du måste använda SQL-autentisering.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Skriv-API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Använda PySpark-anslutningen

> [!NOTE]
> I det här exemplet får du bara den bärbara dator upplevelsen som är i åtanke.

Anta att du har en dataframe "pyspark_df" som du vill skriva till DW.

Skapa en temporär tabell med hjälp av dataframe i PySpark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Kör en Scala-cell i PySpark Notebook med MAGICS

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
På samma sätt kan du i Läs scenariot läsa data med Scala och skriva till en temporär tabell och använda Spark SQL i PySpark för att fråga Temp-tabellen till en dataframe.

## <a name="next-steps"></a>Nästa steg

- [Skapa en SQL-pool]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Skapa en ny Apache Spark pool för en Azure Synapse Analytics-arbetsyta](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 