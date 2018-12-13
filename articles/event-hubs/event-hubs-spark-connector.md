---
title: Integrera med Apache Spark - Azure Event Hubs | Microsoft Docs
description: Integrera med Apache Spark för att aktivera Structured Streaming med Event Hubs
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
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 194898f658c08a3a5a9d8c1d601ea53c817e7649
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076823"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrera Apache Spark med Azure Event Hubs

Azure Event Hubs integreras sömlöst med [Apache Spark](https://spark.apache.org/) att skapa distribuerade program för strömning. Har stöd för den här integreringen [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html), och [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Event Hubs-anslutningsprogrammet för Apache Spark är tillgängligt på [GitHub](https://github.com/Azure/azure-event-hubs-spark). Det här biblioteket är också tillgängligt för användning i Maven-projekt från den [Maven Central Repository](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Den här artikeln beskrivs hur du skapar en kontinuerlig program i [Azure Databricks](https://azure.microsoft.com/services/databricks/). Även om den här artikeln använder Azure Databricks, Spark-kluster är också tillgängliga med [HDInsight](../hdinsight/spark/apache-spark-overview.md).

I exemplet i den här artikeln används två Scala-anteckningsböcker: en för direktuppspelning av händelser från en händelsehubb och en annan för att skicka händelser till den.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en, [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* En Event Hubs-instans. Om du inte har en, [skapar ett](event-hubs-create.md).
* En [Azure Databricks](https://azure.microsoft.com/services/databricks/) instans. Om du inte har en, [skapar ett](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Skapa ett bibliotek med maven-koordinater](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Stream-händelser från event hub med följande kod:

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
Följande kod skickar händelser till din event hub med Spark batch API: er. Du kan också skriva en strömmande fråga för att skicka händelser till event hub:

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
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du ställer in en ström för skalbar, feltolerant beräkningssystem med Event Hubs-anslutningsprogrammet för Apache Spark. Lär dig mer om hur du använder Event Hubs med Structured Streaming och Spark Streaming genom att följa dessa länkar:

* [Structured Streaming + Integreringsguide för Azure Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark-strömning + Integreringsguide för Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
