---
title: Få tillgång till Azure Cosmos DB Cassandra API från Azure Databricks
description: Den här artikeln beskriver hur du arbetar med Azure Cosmos DB Cassandra API från Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60894019"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Få tillgång till Azure Cosmos DB Cassandra API-data från Azure Databricks

Den här artikeln beskriver hur du arbetar med Azure Cosmos DB Cassandra API från Spark på [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Krav

* [Etablera ett Azure Cosmos DB Cassandra API-konto](create-cassandra-dotnet.md#create-a-database-account)

* [Granska grunderna för att ansluta till Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Etablera ett Azure Databricks-kluster](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Granska kodexemplen för att arbeta med Cassandra API](cassandra-spark-generic.md#next-steps)

* [Använd cqlsh för validering om du så föredrar](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API-instanskonfiguration för Cassandra-anslutning:**

  Kopplingen för Cassandra API kräver att Cassandra-anslutningsinformationen initieras som en del av gnistkontexten. När du startar en Databricks-anteckningsbok initieras redan miniatyrkontexten och det är inte tillrådligt att stoppa och initiera om den. En lösning är att lägga till Cassandra API-instanskonfigurationen på klusternivå i klustergnistanskonfigurationen. Detta är en engångsaktivitet per kluster. Lägg till följande kod i Spark-konfigurationen som ett utrymmesavgränsat nyckelvärdepar:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

* **Cassandra Spark-anslutning:** - Om du vill integrera Azure Cosmos DB Cassandra API med Spark ska Cassandra-kopplingen kopplas till Azure Databricks-klustret. Så här bifogar du klustret:

  * Granska Databricks runtime-versionen, Spark-versionen. Leta sedan reda på [maven-koordinaterna](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) som är kompatibla med Cassandra Spark-kontakten och koppla den till klustret. Se artikeln ["Ladda upp ett Maven-paket eller Spark-paket"](https://docs.databricks.com/user-guide/libraries.html) för att koppla anslutningsbiblioteket till klustret. Maven-koordinaten för "Databricks Runtime version 4.3", "Spark 2.3.1" och "Scala 2.11" är till exempel`spark-cassandra-connector_2.11-2.3.1`

* **Azure Cosmos DB Cassandra API-specifikt bibliotek:** - En anpassad anslutningsfabrik krävs för att konfigurera återförsöksprincipen från Cassandra Spark-kopplingen till Azure Cosmos DB Cassandra API. `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`Lägg till [maven-koordinaterna](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) för att koppla biblioteket till klustret.

## <a name="sample-notebooks"></a>Exempel på notebook-filer

En lista över Azure Databricks [exempel anteckningsböcker](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) finns i GitHub repo för dig att ladda ner. Dessa exempel inkluderar hur du ansluter till Azure Cosmos DB Cassandra API från Spark och utför olika CRUD-åtgärder på data. Du kan också [importera alla anteckningsböcker](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) till din Databricks-klusterarbetsyta och köra den. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Komma åt Azure Cosmos DB Cassandra API från Spark Scala-program

Spark-program som ska köras som automatiserade processer på Azure Databricks skickas till klustret med hjälp av [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) och schemaläggs för att köras via Azure Databricks-jobben.

Följande är länkar som hjälper dig att komma igång med att skapa Spark Scala-program för att interagera med Azure Cosmos DB Cassandra API.
* [Så här ansluter du till Azure Cosmos DB Cassandra API från ett Spark Scala-program](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Så här kör du ett Spark Scala-program som ett automatiserat jobb på Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Fullständig lista över kodexempel för arbete med Cassandra API](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Nästa steg

Kom igång med [att skapa ett Cassandra API-konto, databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program.
