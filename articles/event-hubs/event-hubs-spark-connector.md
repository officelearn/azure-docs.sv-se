---
title: Integrera med Apache Spark – Azure-händelsehubbar | Microsoft-dokument
description: Den här artikeln visar hur du integrerar med Apache Spark för att aktivera strukturerad strömning med eventhubbar.
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
ms.openlocfilehash: 4c4fd74e9123e1310be297a15090433d365d24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311690"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrera Apache Spark med Azure Event Hubs

Azure Event Hubs integreras sömlöst med [Apache Spark](https://spark.apache.org/) för att aktivera bygga distribuerade strömmande program. Den här integreringen stöder [Spark Core,](https://spark.apache.org/docs/latest/rdd-programming-guide.html) [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)och Structured [Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Event Hubs-kopplingen för Apache Spark är tillgänglig på [GitHub](https://github.com/Azure/azure-event-hubs-spark). Det här biblioteket är också tillgängligt för användning i Maven-projekt från [Mavens centrala arkiv](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

I den här artikeln beskrivs hur du skapar ett kontinuerligt program i [Azure Databricks](https://azure.microsoft.com/services/databricks/). Den här artikeln använder Azure Databricks, men Spark-kluster är också tillgängliga med [HDInsight](../hdinsight/spark/apache-spark-overview.md).

I exemplet i den här artikeln används två Scala-anteckningsböcker: en för strömmande händelser från en händelsehubb och en annan för att skicka tillbaka händelser till den.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har ett, [skapa ett gratis konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* En händelsehubbar-instans. Om du inte har en, [skapa en](event-hubs-create.md).
* En [Azure Databricks-instans.](https://azure.microsoft.com/services/databricks/) Om du inte har en, [skapa en](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Skapa ett bibliotek med maven-koordinater:](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package) `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Strömma händelser från händelsehubben med hjälp av följande kod:

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
Följande kod skickar händelser till din händelsehubb med Spark-batch-API:erna. Du kan också skriva en direktuppspelningsfråga för att skicka händelser till händelsehubben:

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

Nu vet du hur du konfigurerar en skalbar, feltolerant ström med eventhubbarharna för Apache Spark. Läs mer om hur du använder eventhubbar med strukturerad strömning och sparkströmning genom att följa dessa länkar:

* [Integrationsguide för strukturerad strömning + Azure-händelsehubbar](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Integreringsguide för Spark Streaming + Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
