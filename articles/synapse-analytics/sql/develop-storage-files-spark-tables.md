---
title: Fråge spark-tabeller med SQL på begäran (förhandsgranskning)
description: Översikt över hur du frågar Spark-tabeller med SQL på begäran (förhandsgranskning)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424050"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Fråga Spark-tabeller med Azure Synapse Analytics med SQL on-demand (förhandsgranskning)

SQL on-demand (preview) kan automatiskt synkronisera metadata från Spark-pooler i Synapse-arbetsytan (förhandsversion). En SQL-databas på begäran skapas för varje databas som finns i Spark-pooler (förhandsversion). För varje Spark-tabell baserad på Parkett eller CSV skapas en extern tabell i SQL-databasen på begäran. Därför kan du stänga av Spark-poolerna och fortfarande fråga Spark-tabeller från SQL on-demand.

När en tabell är partitionerad i Spark ordnas filer i lagring efter mappar. SQL on-demand använder partitionsmetadata och riktar endast relevanta mappar och filer för din fråga.

Metadatasynkronisering konfigureras automatiskt för varje Spark-pool som etablerats på Azure Synapse-arbetsytan. Du kan börja fråga Spark-tabeller direkt.

Varje Spark-tabell representeras med en extern tabell i ett dbo-schema som motsvarar en SQL-databas på begäran. För Spark-tabellfrågor kör du en fråga som riktar sig till en extern [spark_table]. Innan du kör exemplet nedan kontrollerar du att du har rätt [åtkomst till lagringskontot](develop-storage-files-storage-access-control.md) där filerna finns.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Utlösa datatyper till SQL-datatyper mappning

| Utlösa datatyp | SQL-datatyp               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| Kortfilm       | smallint                    |
| Heltalstyp     | int                         |
| LongType (långtyp)        | bigint                      |
| FloatType (Olikartade)       | real                        |
| DoubleType (dubbeltyp)      | float                       |
| Decimaltyp     | decimal                     |
| Tidsstämpel   | datetime2                   |
| Datumtyp        | date                        |
| StringType (StringType)      | varchar(max)*               |
| BinaryType (binärtyp)      | varbinary                   |
| BooleanType     | bit                         |
| Matristyp       | varchar(max)* (till JSON)** |
| MapType (Olika)         | varchar(max)* (till JSON)** |
| StructType (olikartade)      | varchar(max)* (till JSON)** |

\*Sortering som används är Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType och StructType representeras som JSON.



## <a name="next-steps"></a>Nästa steg

Gå vidare till artikeln [Storage Access Control](develop-storage-files-storage-access-control.md) om du vill veta mer om åtkomstkontroll för lagring.
