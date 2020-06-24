---
title: Synkronisera Apache Spark för externa definitioner av Azure Synapse-tabeller i SQL på begäran (för hands version)
description: Översikt över hur du frågar Spark-tabeller med SQL på begäran (för hands version)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: e36f98a20d1fc6392aef1aebf5fc86e18085cc10
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204906"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>Synkronisera Apache Spark för externa definitioner av Azure Synapse-tabeller i SQL på begäran (för hands version)

SQL på begäran (för hands version) kan automatiskt synkronisera metadata från Apache Spark för Azure Synapse-pooler. En SQL-databas på begäran skapas för varje databas som är befintlig i Spark-pooler (för hands version). 

För varje spark-extern tabell som baseras på Parquet och finns i Azure Storage skapas en extern tabell i SQL-databasen på begäran. Därför kan du stänga av Spark-poolerna och fortfarande fråga Spark-externa tabeller från SQL på begäran.

När en tabell är partitionerad i Spark ordnas filer i lagringen efter mappar. SQL på begäran kommer att använda metadata för partitionen och endast rikta relevanta mappar och filer för frågan.

Synkronisering av metadata konfigureras automatiskt för varje spark-pool som tillhandahålls i Azure dataSynapses-arbetsytan. Du kan börja fråga direkt externa tabeller direkt.

Varje Spark Parquet extern tabell som finns i Azure Storage representeras av en extern tabell i ett dbo-schema som motsvarar en SQL-databas på begäran. 

Kör en fråga som är riktad mot en extern [spark_table] för Spark-frågor för externa tabeller. Kontrol lera att du har rätt [åtkomst till lagrings kontot](develop-storage-files-storage-access-control.md) där filerna finns innan du kör exemplet nedan.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Spark-datatyper till SQL data types-mappning

| Spark-datatyp | SQL-datatyp               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar (max) *               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar (max) * (till JSON) * * |
| MapType         | varchar (max) * (till JSON) * * |
| StructType      | varchar (max) * (till JSON) * * |

\*Den sortering som används är Latin1_General_100_BIN2_UTF8.

* * ArrayType, MapType och StructType representeras som JSON-exempel.



## <a name="next-steps"></a>Nästa steg

Gå vidare till [lagrings Access Controls](develop-storage-files-storage-access-control.md) artikeln om du vill veta mer om åtkomst kontroll för lagring.
