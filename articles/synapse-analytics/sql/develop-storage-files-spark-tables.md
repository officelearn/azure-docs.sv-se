---
title: Fråga Spark-tabeller med SQL på begäran (för hands version)
description: Översikt över hur du frågar Spark-tabeller med SQL på begäran (för hands version)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424050"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Fråga Spark-tabeller med Azure Synapse Analytics med SQL på begäran (för hands version)

SQL on-demand (för hands version) kan automatiskt synkronisera metadata från Spark-pooler i Synapse-arbetsytan (för hands version). En SQL-databas på begäran skapas för varje databas som är befintlig i Spark-pooler (för hands version). För varje spark-tabell som baseras på Parquet eller CSV skapas en extern tabell i SQL-databasen på begäran. Därför kan du stänga av Spark-poolerna och fortfarande fråga Spark-tabeller från SQL på begäran.

När en tabell är partitionerad i Spark ordnas filer i lagringen efter mappar. SQL på begäran kommer att använda metadata för partitionen och endast rikta relevanta mappar och filer för frågan.

Synkronisering av metadata konfigureras automatiskt för varje spark-pool som tillhandahålls i Azure dataSynapses-arbetsytan. Du kan börja fråga Spark-tabeller direkt.

Varje Spark-tabell representeras med en extern tabell i ett dbo-schema som motsvarar en SQL-databas på begäran. Kör en fråga som är riktad mot en extern [spark_table] för Spark Table-frågor. Kontrol lera att du har rätt [åtkomst till lagrings kontot](develop-storage-files-storage-access-control.md) där filerna finns innan du kör exemplet nedan.

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
