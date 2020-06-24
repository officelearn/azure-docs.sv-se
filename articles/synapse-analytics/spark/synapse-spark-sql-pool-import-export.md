---
title: Importera och exportera data mellan Spark-pooler (för hands version) och SQL-pooler
description: Den här artikeln innehåller information om hur du använder det anpassade anslutnings programmet för att flytta data fram och tillbaka mellan SQL-pooler och Spark-pooler (för hands version).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 515fd9bfedc5bc5d3cefda2a357c351f515fb5f5
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194679"
---
# <a name="introduction"></a>Introduktion

Azure Synapse-Apache Spark till Synapse SQL Connector är utformad för att effektivt överföra data mellan Spark-pooler (för hands version) och SQL-pooler i Azure Synapse. Azure-Synapse Apache Spark till Synapse SQL Connector fungerar bara på SQL-pooler, utan fungerar med SQL på begäran.

## <a name="design"></a>Design

Överföring av data mellan Spark-pooler och SQL-pooler kan göras med JDBC. Men med tanke på två distribuerade system som Spark-och SQL-pooler, är JDBC att vara en Flask hals med seriell data överföring.

Azure Synapse Apache Spark-poolen till Synapse SQL Connector är en implementering av data källa för Apache Spark. Den använder Azure Data Lake Storage Gen2 och PolyBase i SQL-pooler för att effektivt överföra data mellan Spark-klustret och Synapse SQL-instansen.

![Kopplings arkitektur](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autentisering i Azure Synapse Analytics

Autentisering mellan system sker sömlöst i Azure Synapse Analytics. Det finns en token-tjänst som ansluter med Azure Active Directory för att hämta säkerhetstoken som ska användas vid åtkomst till lagrings kontot eller data lager servern. 

Därför behöver du inte skapa autentiseringsuppgifter eller ange dem i anslutnings-API: et så länge AAD-auth är konfigurerat på lagrings kontot och på data lager servern. Annars kan SQL-autentisering anges. Mer information finns i [användnings](#usage) avsnittet.

## <a name="constraints"></a>Villkor

- Den här kopplingen fungerar bara i Scala.

## <a name="prerequisites"></a>Krav

- Ha **db_exporter** -rollen i databasen/SQL-poolen som du vill överföra data till/från.

Om du vill skapa användare ansluter du till databasen och följer de här exemplen:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Så här tilldelar du en roll:

```sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Användning

Import instruktionerna krävs inte, de är i förväg importerade för den bärbara datorn.

### <a name="transferring-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Överföra data till eller från en SQL-pool som är kopplad till arbets ytan

> [!NOTE]
> **Importer som inte behövs i Notebook-upplevelsen**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Läs-API

```scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

Ovanstående API fungerar både för interna (hanterade) och externa tabeller i SQL-poolen.

#### <a name="write-api"></a>Skriv-API

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

där TableType kan vara konstanter. INTERNAL eller constants. EXTERNAL

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Autentiseringen till lagringen och SQL Server görs

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>Om du överför data till eller från en SQL-pool eller databas utanför arbets ytan

> [!NOTE]
> Importer som inte behövs i Notebook-upplevelsen

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Läs-API

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Skriv-API

```scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Använda SQL-autentisering i stället för AAD

#### <a name="read-api"></a>Läs-API

För närvarande har kopplingen inte stöd för tokenbaserad autentisering till en SQL-pool utanför arbets ytan. Du måste använda SQL-autentisering.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Skriv-API

```scala
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

Skapa en temporär tabell med hjälp av dataframe i PySpark:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Kör en Scala-cell i den PySpark Notebook med MAGICS:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

På samma sätt kan du i Läs scenariot läsa data med Scala och skriva till en temporär tabell och använda Spark SQL i PySpark för att fråga Temp-tabellen till en dataframe.

## <a name="allowing-other-users-to-use-the-dw-connector-in-your-workspace"></a>Låta andra användare använda DW-anslutningen i din arbets yta

Du måste vara ägare av Storage BLOB-data på ADLS Gen2 lagrings konto som är anslutet till arbets ytan för att ändra behörigheter som saknas för andra. Se till att användaren har åtkomst till arbets ytan och behörigheterna för att köra antecknings böcker.

### <a name="option-1"></a>Alternativ 1

- Gör användaren till en Storage BLOB data-deltagare/-ägare

### <a name="option-2"></a>Alternativ 2

- Ange följande ACL: er i mappstrukturen:

| Mapp | / | synapse | arbetsytor  | <workspacename> | sparkpools | <sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Åtkomst behörigheter | --X | --X | --X | --X | --X | --X | -WX |
| Standard behörigheter | ---| ---| ---| ---| ---| ---| ---|

- Du bör kunna ACL: er alla mappar från "Synapse" och nedåt från Azure Portal. Om du vill ACL-rotmappen, följer du anvisningarna nedan.

- Ansluta till lagrings kontot som är anslutet till arbets ytan från Storage Explorer med AAD
- Välj ditt konto och ange ADLS Gen2-URL och standard fil system för arbets ytan
- När du kan se det lagrings konto som visas i listan högerklickar du på arbets ytan lista och väljer Hantera åtkomst
- Lägg till användaren i mappen/-mappen med åtkomst behörigheten "kör". Välj OK

> [!IMPORTANT]
> Se till att du inte väljer standard om du inte vill ha det.

## <a name="next-steps"></a>Nästa steg

- [Skapa en SQL-pool med hjälp av Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Skapa en ny Apache Spark pool med hjälp av Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
