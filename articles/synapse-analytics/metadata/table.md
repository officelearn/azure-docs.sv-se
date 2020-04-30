---
title: Delade metadata-tabeller för Azure Synapse Analytics
description: Azure Synapse Analytics tillhandahåller en delad metadata modell där en tabell skapas i Apache Spark, vilket gör den tillgänglig från SQL on-demand (för hands version) och SQL-pooler utan att duplicera data.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 7c1951c772dcd2f49f4f7c09021f69193af0a87e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424582"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Tabeller för delade metadata i Azure Synapse Analytics

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Med Azure Synapse Analytics kan olika beräknings motorer för arbets ytan dela databaser och Parquet tabeller mellan Apache Spark pooler (för hands version), motorn SQL on-demand (för hands version) och SQL-pooler.

När en databas har skapats av ett Spark-jobb kan du skapa tabeller i den med Spark som använder Parquet som lagrings format. Tabellerna blir omedelbart tillgängliga för frågor från någon av Azure Synapse-arbetsytans Spark-pooler. De kan också användas från alla Spark-jobb som omfattas av behörigheter.

Spark created-, Managed-och external-tabellerna görs också tillgängliga som externa tabeller med samma namn i motsvarande synkroniserade databas i SQL på begäran och i motsvarande `$`fasta scheman i de SQL-pooler som har metadata-synkronisering aktive rad. Att [exponera en spark-tabell i SQL](#exposing-a-spark-table-in-sql) innehåller mer information om Table-synkroniseringen.

Eftersom tabellerna synkroniseras till SQL på begäran och SQL-pooler asynkront, kommer det att finnas en fördröjning tills de visas.

Mappning av tabeller till externa tabeller, data källor och fil format.

## <a name="manage-a-spark-created-table"></a>Hantera en spark-skapad tabell

Använd Spark för att hantera Spark-skapade databaser. Du kan t. ex. ta bort den via ett Spark-jobb och skapa tabeller i det från Spark.

Om du skapar objekt i en sådan databas från SQL på begäran eller försöker släppa databasen kommer åtgärden att lyckas, men den ursprungliga Spark-databasen kommer inte att ändras.

Om du försöker ta bort det synkroniserade schemat i en SQL-pool eller försöker skapa en tabell i den, returnerar Azure ett fel.

## <a name="exposing-a-spark-table-in-sql"></a>Exponerar en spark-tabell i SQL

### <a name="which-spark-tables-are-shared"></a>Vilka Spark-tabeller som delas

Spark innehåller två typer av tabeller som Azure Synapse visar i SQL automatiskt:

- Hanterade tabeller

  Spark innehåller många alternativ för att lagra data i hanterade tabeller, till exempel TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA och LIBSVM. De här filerna lagras vanligt vis i `warehouse` den katalog där hanterade tabell data lagras.

- Externa tabeller

  Spark ger också olika sätt att skapa externa tabeller över befintliga data, antingen genom att `LOCATION` tillhandahålla alternativet eller använda Hive-formatet. Sådana externa tabeller kan vara över en mängd olika data format, inklusive Parquet.

Azure-Synapse delar för närvarande bara hanterade och externa Spark-tabeller som lagrar sina data i Parquet-format med SQL-motorerna. Tabeller som backas upp av andra format synkroniseras inte automatiskt. Du kanske kan synkronisera sådana tabeller manuellt som en extern tabell i din egen SQL-databas om SQL-motorn stöder tabellens underliggande format.

### <a name="how-are-spark-tables-shared"></a>Hur är Spark-tabeller delade

De shareable-hanterade och externa Spark-tabellerna som visas i SQL-motorerna som externa tabeller med följande egenskaper:

- Den externa SQL-tabellens data källa är data källan som representerar Spark-tabellens plats-mapp.
- Den externa SQL-tabellens fil format är Parquet.
- Den externa SQL-tabellens åtkomst-autentiseringsuppgifter är direkt.

Eftersom alla Spark-tabell namn är giltiga SQL-tabell namn och alla Spark kolumn namn är giltiga, används Spark-tabellen och kolumn namnen för den externa SQL-tabellen i SQL-kolumn.

Spark-tabeller tillhandahåller olika data typer än Synapse SQL-motorer. Följande tabell mappar Spark-tabellens data typer mappar till SQL-typerna:

| Spark-datatyp | SQL-datatyp | Kommentarer |
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

Spark-databaser och-tabeller, samt deras synkroniserade representationer i SQL-motorerna kommer att skyddas på den underliggande lagrings nivån. Eftersom de för närvarande inte har behörighet till själva objekten kan objekten visas i Object Explorer.

Det säkerhets objekt som skapar en hanterad tabell anses vara ägare till den tabellen och har alla rättigheter till tabellen och de underliggande mapparna och filerna. Dessutom blir databasens ägare automatiskt ägare till tabellen.

Om du skapar en spark-eller SQL-tabell med autentisering genom strömning, skyddas data endast på mapp-och fil nivå. Om någon frågar den här typen av extern tabell, skickas säkerhets identiteten för frågans sändning till fil systemet, vilket kommer att kontrol lera åtkomst behörigheterna.

Mer information om hur du anger behörigheter för mappar och filer finns i [Azure Synapse Analytics Shared Database](database.md).

## <a name="examples"></a>Exempel

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Skapa en hanterad tabell som backas upp av Parquet i Spark och fråga från SQL på begäran

I det här scenariot har du en spark- `mytestdb`databas med namnet. Se [skapa & ansluta till Spark Database-SQL på begäran](database.md#create--connect-to-spark-database---sql-on-demand).

Skapa en hanterad Spark-tabell med SparkSQL genom att köra följande kommando:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Detta skapar tabellen `myParquetTable` i-databasen `mytestdb`. Efter en kort fördröjning kan du se tabellen i SQL på begäran. Kör till exempel följande uttryck från SQL på begäran.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Kontrol lera `myParquetTable` att ingår i resultaten.

>[!NOTE]
>En tabell som inte använder Parquet som lagrings format kommer inte att synkroniseras.

Infoga sedan några värden i tabellen från Spark, till exempel med följande C# Spark-uttryck i en C#-anteckningsbok:

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

Nu kan du läsa data från SQL på begäran på följande sätt:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Du bör hämta följande rad som resultat:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="creating-an-external-table-backed-by-parquet-in-spark-and-querying-it-from-sql-on-demand"></a>Skapa en extern tabell som backas upp av Parquet i Spark och fråga den från SQL på begäran

I det här exemplet skapar du en extern Spark-tabell över de Parquet-datafiler som skapades i föregående exempel för den hanterade tabellen.

Till exempel när SparkSQL körs:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Ersätt plats hållaren `<fs>` med fil system namnet som är standard fil systemet för arbets ytan och plats `<synapse_ws>` hållaren med namnet på Synapse-arbetsytan som du använder för att köra det här exemplet.

I föregående exempel skapas tabellen `myExtneralParquetTable` i-databasen. `mytestdb` Efter en kort fördröjning kan du se tabellen i SQL på begäran. Kör till exempel följande uttryck från SQL på begäran.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Kontrol lera `myExternalParquetTable` att ingår i resultaten.

Nu kan du läsa data från SQL på begäran på följande sätt:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Du bör hämta följande rad som resultat:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="querying-spark-tables-in-a-sql-pool"></a>Fråga Spark-tabeller i en SQL-pool

Med tabellerna som skapats i föregående exempel skapar du nu en SQL-pool på din arbets yta `mysqlpool` med namnet som aktiverar synkronisering av metadata (eller använder den redan skapade poolen från att [exponera en spark-databas i en SQL-pool](database.md#exposing-a-spark-database-in-a-sql-pool).

Kör följande instruktion mot `mysqlpool` SQL-poolen:

```sql
SELECT * FROM sys.tables;
```

Kontrol lera att tabellerna `myParquetTable` och `myExternalParquetTable` är synliga i schemat `$mytestdb`.

Nu kan du läsa data från SQL på begäran på följande sätt:

```sql
SELECT * FROM [$mytestdb].myParquetTable WHERE name = 'Alice';
SELECT * FROM [$mytestdb].myExternalParquetTable WHERE name = 'Alice';
```

Du bör få samma resultat som med SQL på begäran ovan.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om delade metadata för Azure Synapse Analytics](overview.md)
- [Läs mer om delade metadata-tabeller för Azure Synapse Analytics](table.md)


