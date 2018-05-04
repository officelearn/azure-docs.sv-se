---
title: Integrera Apache Spark med Händelsehubbar i Azure | Microsoft Docs
description: Integrera med Apache Spark strukturerade strömning med Händelsehubbar
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija
ms.openlocfilehash: 09790852d28f2f68e854b37256ca1c8edba992e6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrera Apache Spark med Azure Event Hubs

Händelsehubbar i Azure integreras sömlöst med [Apache Spark](https://spark.apache.org/) att skapa distribuerade program för strömning. Denna integration stöder [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html), [strukturerade strömning](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Händelsehubbar connector för Apache Spark är tillgängligt på [GitHub](https://github.com/Azure/azure-event-hubs-spark). Det här biblioteket är också tillgängligt för användning i Maven-projekt från den [Maven centrallager](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Den här artikeln visar hur du skapar en kontinuerlig program i [Azure Databricks](https://azure.microsoft.com/services/databricks/). När den här artikeln använder [Azure Databricks](https://azure.microsoft.com/services/databricks/), Spark-kluster finns också med [HDInsight](../hdinsight/spark/apache-spark-overview.md).

I följande exempel används två Scala anteckningsböcker: en för strömning händelser från en händelsehubb och en annan för att skicka händelser till den.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon, [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* En instans av Händelsehubbar. Om du inte har någon, [skapar du en](event-hubs-create.md)
* En [Azure Databricks](https://azure.microsoft.com/services/databricks/) instans. Om du inte har någon, [skapar du en](../azure-databricks/quickstart-create-databricks-workspace-portal.md)
* [Skapa ett bibliotek med maven koordinat](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`

Dataströmmen händelser från din Händelsehubb med hjälp av följande kod:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
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
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
Följande exempelkod skickar händelser till din event hub med Spark batch API: er. Du kan också skriva en strömmande fråga för att skicka händelser till händelsehubben.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du ställer in ett skalbart, feltolerant dataström med hjälp av Event Hubs kopplingen för Apache Spark. Lär dig mer om hur du använder Händelsehubbar med strukturerade strömning och Spark Streaming genom att följa dessa länkar:

* [Strukturerade strömning + Azure Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming + Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
