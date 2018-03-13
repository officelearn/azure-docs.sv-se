---
title: "Integrera Apache Spark med Händelsehubbar i Azure | Microsoft Docs"
description: "Integrera med Apache Spark strukturerade strömning med Händelsehubbar"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija
ms.openlocfilehash: 2dcf390b80c119ab21948b982c0812395e45bc01
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrera Apache Spark med Azure Event Hubs

Händelsehubbar i Azure integreras sömlöst med [Apache Spark](https://spark.apache.org/) för att skapa _slutpunkt till slutpunkt_ distribuerade program enkelt. Denna integration stöder [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html), [strukturerade strömning](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Event Hubs Connector för Apache Spark är tillgängligt på [GitHub](https://github.com/Azure/azure-event-hubs-spark). Det här biblioteket är också tillgängligt för användning i Maven-projekt från den [Maven centrallager](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)

Den här artikeln visar hur du gör en kontinuerlig ansökan i [Azure Databrick](https://azure.microsoft.com/services/databricks/). När den här artikeln använder [Azure Databricks](https://azure.microsoft.com/services/databricks/), Spark-kluster finns också med [HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-overview).

I följande exempel används två Scala bärbara datorer, en för strömning händelser från en Händelsehubb och en annan för att skicka händelser till den.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon, [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* En instans av Händelsehubbar. Om du inte har någon, [skapar du en](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)
* En [Azure Databricks](https://azure.microsoft.com/services/databricks/) instans. Om du inte har någon, [skapar du en](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)
* [Skapa ett bibliotek med maven koordinat](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.0`

Använd följande kodavsnitt i anteckningsboken dataströmmen händelser från Event Hub.

```scala
import org.apache.spark.eventhubs._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()

// Select the body column and cast it to a string.
val eventhubs = reader
  .select("CAST (body AS STRING)")
  .as[String]
```
Följande kodavsnitt används för att skicka händelser till din Event Hub med Sparks batch API. Du kan också skriva en strömmande fråga för att skicka händelser till Händelsehubben.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build

val eventHubsConf = EventHubsConf(connectionString)

// Create a column representing the partitionKey.
val partitionKeyColumn = (col("id") % 5).cast("string").as("partitionKey")
// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified Event Hub.
val df = spark.range(200).select(partitionKeyColumn, bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 

```

Den här artikeln ger dig en uppfattning om hur Event Hubs kopplingen fungerar för skapa realtid, fel feltoleranta strömmande lösningar. Läs mer om strukturerade strömning och Händelsehubbar integrerad koppling genom att följa nästa steg.

## <a name="next-steps"></a>Nästa steg

* [Strukturerade strömning + Azure Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming + Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
