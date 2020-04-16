---
title: Azure Synapse Analytics delade metadatatabeller
description: Azure Synapse Analytics tillhandahåller en delad metadatamodell där skapandet av en tabell i Apache Spark gör den tillgänglig från sina SQL on-demand (förhandsversion) och SQL-poolmotorer utan att duplicera data.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 7c1951c772dcd2f49f4f7c09021f69193af0a87e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424582"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Delade metadatatabeller för Azure Synapse Analytics

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Azure Synapse Analytics gör det möjligt för olika beräkningsmotorer på arbetsytan att dela databaser och parettstödda tabeller mellan apache spark-pooler (förhandsversion), SQL on-demand-motor (förhandsversion) och SQL-pooler.

När en databas har skapats av ett Spark-jobb kan du skapa tabeller i den med Spark som använder Parkett som lagringsformat. Dessa tabeller blir omedelbart tillgängliga för frågor av någon av Azure Synapse-arbetsytan Spark-pooler. De kan också användas från alla Spark-jobb som omfattas av behörigheter.

De spark-skapade, hanterade och externa tabellerna görs också tillgängliga som externa tabeller med samma namn i `$`motsvarande synkroniserade databas i SQL på begäran och i motsvarande -prefixerade scheman i SQL-poolerna som har sin metadatasynkronisering aktiverad. [Om du exponerar en Spark-tabell i SQL](#exposing-a-spark-table-in-sql) finns mer information om tabellsynkroniseringen.

Eftersom tabellerna synkroniseras med SQL on-demand och SQL-poolerna asynkront, kommer det att finnas en fördröjning tills de visas.

Mappning av tabeller till externa tabeller, datakällor och filformat.

## <a name="manage-a-spark-created-table"></a>Hantera en skapad spark-tabell

Använd Spark för att hantera Spark-skapade databaser. Ta till exempel bort det via ett Spark-pooljobb och skapa tabeller i det från Spark.

Om du skapar objekt i en sådan databas från SQL på begäran eller försöker släppa databasen, kommer åtgärden att lyckas, men den ursprungliga Spark-databasen kommer inte att ändras.

Om du försöker släppa det synkroniserade schemat i en SQL-pool eller försöker skapa en tabell i den returnerar Azure ett fel.

## <a name="exposing-a-spark-table-in-sql"></a>Exponera en Spark-tabell i SQL

### <a name="which-spark-tables-are-shared"></a>Vilka Spark-tabeller delas

Spark innehåller två typer av tabeller som Azure Synapse exponerar i SQL automatiskt:

- Hanterade tabeller

  Spark innehåller många alternativ för hur du lagrar data i hanterade tabeller, till exempel TEXT, CSV, JSON, JDBC, PARKETT, ORC, HIVE, DELTA och LIBSVM. Dessa filer lagras normalt `warehouse` i katalogen där hanterade tabelldata lagras.

- Externa tabeller

  Spark ger också sätt att skapa externa tabeller `LOCATION` över befintliga data, antingen genom att ange alternativet eller använda Hive-formatet. Sådana externa tabeller kan vara över en mängd olika dataformat, inklusive parkett.

Azure Synapse delar för närvarande endast hanterade och externa Spark-tabeller som lagrar sina data i parkettformat med SQL-motorerna. Tabeller som backas upp av andra format synkroniseras inte automatiskt. Du kanske kan synkronisera sådana tabeller uttryckligen själv som en extern tabell i din egen SQL-databas om SQL-motorn stöder tabellens underliggande format.

### <a name="how-are-spark-tables-shared"></a>Hur delas Spark-tabeller

De delbara hanterade och externa Spark-tabellerna exponeras i SQL-motorerna som externa tabeller med följande egenskaper:

- Sql externa tabellens datakälla är datakällan som representerar Spark-tabellens platsmapp.
- Den externa sql-tabellens filformat är Parquet.
- SQL externa tabellens åtkomstautentisering är genomströmning.

Eftersom alla Spark-tabellnamn är giltiga SQL-tabellnamn och alla Spark-kolumnnamn är giltiga SQL-kolumnnamn, används spark-tabell- och kolumnnamnen för den externa SQL-tabellen.

Spark-tabeller ger andra datatyper än Synapse SQL-motorer. I följande tabell mappas Spark-tabelldatatyper mappas till SQL-typerna:

| Utlösa datatyp | SQL-datatyp | Kommentarer |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Med sortering`Latin1_General_CP1_CI_AS_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serialiserar till JSON med sortering`Latin1_General_CP1_CI_AS_UTF8` |
| `map`       |    `varchar(max)`   | Serialiserar till JSON med sortering`Latin1_General_CP1_CI_AS_UTF8` |
| `struct`    |    `varchar(max)`   | Serialiserar till JSON med sortering`Latin1_General_CP1_CI_AS_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Säkerhetsmodell

Spark-databaserna och tabellerna samt deras synkroniserade representationer i SQL-motorerna kommer att skyddas på den underliggande lagringsnivån. Eftersom de för närvarande inte har behörighet för själva objekten kan objekten ses i objektutforskaren.

Säkerhetsobjektet som skapar en hanterad tabell anses vara ägare till den tabellen och har alla rättigheter till tabellen samt underliggande mappar och filer. Dessutom blir databasens ägare automatiskt delägare i tabellen.

Om du skapar en extern Spark- eller SQL-tabell med autentisering genomströmning skyddas data bara på mapp- och filnivå. Om någon frågar den här typen av extern tabell skickas frågelämnarens säkerhetsidentitet till filsystemet, vilket söker efter åtkomsträttigheter.

Mer information om hur du anger behörigheter för mappar och filer finns i [den delade databasen För Azure Synapse Analytics](database.md).

## <a name="examples"></a>Exempel

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Skapa en hanterad tabell som stöds av Parkett i Spark och fråga från SQL on-demand

I det här fallet har `mytestdb`du en Spark-databas med namnet . Se [Skapa & ansluta till Spark-databas - SQL on-demand](database.md#create--connect-to-spark-database---sql-on-demand).

Skapa en hanterad Spark-tabell med SparkSQL genom att köra följande kommando:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Då skapas `myParquetTable` tabellen i `mytestdb`databasen . Efter en kort fördröjning kan du se tabellen i SQL on-demand. Kör till exempel följande uttryck från SQL on-demand.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Kontrollera `myParquetTable` att det ingår i resultatet.

>[!NOTE]
>En tabell som inte använder Parkett som lagringsformat synkroniseras inte.

Infoga sedan några värden i tabellen från Spark, till exempel med följande C# Spark-satser i en C#-anteckningsbok:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

Nu kan du läsa data från SQL on-demand enligt följande:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Du bör få följande rad som resultat:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="creating-an-external-table-backed-by-parquet-in-spark-and-querying-it-from-sql-on-demand"></a>Skapa en extern tabell som stöds av Parkett i Spark och fråga den från SQL on-demand

I det här exemplet skapar du en extern Spark-tabell över de parquetdatafiler som skapades i föregående exempel för den hanterade tabellen.

Till exempel med SparkSQL-körning:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Ersätt platshållaren `<fs>` med filsystemets namn som är standardfilsystemet `<synapse_ws>` för arbetsytan och platshållaren med namnet på den synapsarbetsyta som du använder för att köra det här exemplet.

I föregående exempel skapas `myExtneralParquetTable` tabellen `mytestdb`i databasen . Efter en kort fördröjning kan du se tabellen i SQL on-demand. Kör till exempel följande uttryck från SQL on-demand.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Kontrollera `myExternalParquetTable` att det ingår i resultatet.

Nu kan du läsa data från SQL on-demand enligt följande:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Du bör få följande rad som resultat:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="querying-spark-tables-in-a-sql-pool"></a>Fråga Spark-tabeller i en SQL-pool

Med tabellerna som skapats i föregående exempel skapar du `mysqlpool` nu en SQL-pool på arbetsytan som aktiverar metadatasynkronisering (eller använder den redan skapade poolen från [Att exponera en Spark-databas i en SQL-pool](database.md#exposing-a-spark-database-in-a-sql-pool).

Kör följande uttryck `mysqlpool` mot SQL-poolen:

```sql
SELECT * FROM sys.tables;
```

Kontrollera att `myParquetTable` tabellerna och `myExternalParquetTable` är `$mytestdb`synliga i schemat .

Nu kan du läsa data från SQL on-demand enligt följande:

```sql
SELECT * FROM [$mytestdb].myParquetTable WHERE name = 'Alice';
SELECT * FROM [$mytestdb].myExternalParquetTable WHERE name = 'Alice';
```

Du bör få samma resultat som med SQL on-demand ovan.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Synapse Analytics delade metadata](overview.md)
- [Läs mer om Azure Synapse Analytics delade metadatatabeller](table.md)


