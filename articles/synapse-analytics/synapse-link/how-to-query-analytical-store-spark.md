---
title: Fråga Azure Cosmos DB analytisk lagring (för hands version) med Apache Spark för Azure Synapse Analytics
description: Så här frågar du Azure Cosmos DB analys med Apache Spark för Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 399aa00be3d585b1d37bab95d8108ee8a09a8791
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85195019"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>Fråga Azure Cosmos DB analytisk lagring (för hands version) med Apache Spark för Azure Synapse Analytics

Den här artikeln innehåller exempel på hur du kan interagera med analys lagret från Synapse-gester. Gester visas när du högerklickar på en container. Med gester kan du snabbt generera kod och skräddarsy den efter dina behov. Gester passar också perfekt för att upptäcka data med ett enda klick.

## <a name="load-to-dataframe"></a>Läs in till DataFrame

I det här steget ska du läsa data från Azure Cosmos DB Analytical Store i en spark-DataFrame. Då visas 10 rader från DataFrame som kallas ***DF***. När dina data är i dataframe kan du utföra ytterligare analyser.

Den här åtgärden påverkar inte transaktions arkivet.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

Motsvarande kod gest i **Scala** skulle vara följande kod:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Skapa Spark-tabell

I den här gesten skapar du en spark-tabell som pekar på den behållare som du har valt. Den här åtgärden medför ingen data förflyttning. Om du bestämmer dig för att ta bort tabellen påverkas inte den underliggande behållaren (och motsvarande analys lager). 

Det här scenariot är praktiskt att återanvända tabeller via verktyg från tredje part och ge åtkomst till data för körnings tillfället.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Skriv DataFrame till container

I den här gesten skriver du en dataframe i en behållare. Den här åtgärden påverkar transaktions prestanda och använder enheter för programbegäran. Att använda Azure Cosmos DB transaktions prestanda är perfekt för Skriv transaktioner. Se till att ersätta **YOURDATAFRAME** med dataframe som du vill skriva tillbaka till.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Motsvarande kod gest i **Scala** skulle vara följande kod:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Läs in strömmande DataFrame från behållare
I den här gesten använder du Spark streaming-funktioner för att läsa in data från en behållare till en dataframe. Data lagras i det primära data Lake-konto (och fil systemet) som du anslöt till arbets ytan. 

Om mappen */localReadCheckpointFolder* inte skapas skapas den automatiskt. Den här åtgärden påverkar transaktions prestandan för Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Motsvarande kod gest i **Scala** skulle vara följande kod:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Skriv strömmande DataFrame till behållare
I den här gesten skriver du en strömmande dataframe till den Azure Cosmos DB-behållare som du har valt. Om mappen */localReadCheckpointFolder* inte skapas skapas den automatiskt. Den här åtgärden påverkar transaktions prestandan för Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Motsvarande kod gest i **Scala** skulle vara följande kod:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>Nästa steg

* [Lär dig vad som stöds mellan Synapse och Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Anslut till Synapse-länk för Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
