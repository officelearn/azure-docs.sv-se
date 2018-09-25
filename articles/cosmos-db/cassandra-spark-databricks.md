---
title: Åtkomst till Azure Cosmos DB Cassandra-API från Azure Databricks
description: Den här artikeln beskriver hur du arbetar med Azure Cosmos DB Cassandra-API från Azure Databricks.
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: 3f1bdb63253506aee211f3733df2a339824de7a0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994657"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Åtkomst till Cassandra-API för Azure Cosmos DB-data från Azure Databricks

Den här artikeln innehåller information om hur du workwith Azure Cosmos DB Cassandra-API från Spark på [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Förutsättningar

* [Etablera ett Cassandra-API för Azure Cosmos DB-konto](create-cassandra-dotnet.md#create-a-database-account)

* [Gå igenom grunderna för att ansluta till Azure Cosmos DB Cassandra-API](cassandra-spark-generic.md)

* [Etablera en Azure Databricks-kluster](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Granska kodexempel för att arbeta med API för Cassandra](cassandra-spark-generic.md#next-steps)

* [Använd cqlsh för verifiering om du så föredrar](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **API för Cassandra instanskonfiguration för Datastax Cassandra-anslutningen:**

  Datastax-anslutningsappen för Cassandra kräver Cassandra anslutningsinformationen initieras som en del av spark-kontext. När du startar en Databricks notebook spark-kontexten har redan initierats och det rekommenderas inte att stoppa och starta om den. En lösning är att lägga till API för Cassandra-instanskonfiguration på en klusternivå i spark klusterkonfigurationen. Detta inträffar en gång per kluster. Lägg till följande kod i Spark-konfigurationen som kan avgränsade viktiga värde-par:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Lägg till de nödvändiga beroendena

* **Datastax Cassandra Spark-anslutningsappen:** – om du vill integrera med Azure Cosmos DB Cassandra API med Spark, Datastax Cassandra anslutningen ska kopplas till Azure Databricks-klustret. Att koppla klustret:

  * Granska Databricks-körningsversion Spark-version. Hitta den [maven-koordinater](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) som är kompatibla med Datastax Cassandra Spark-anslutningsappen och koppla den till klustret. Se [”ladda upp ett Maven-paketet eller Spark-paket”](https://docs.databricks.com/user-guide/libraries.html) artikeln om du vill bifoga kopplingsbibliotek i klustret. Till exempel maven-koordinat för ”Databricks Runtime version 4.3”, ”Spark 2.3.1” och ”Scala 2.11” är `spark-cassandra-connector_2.11-2.3.1`

* **Azure Cosmos DB Cassandra API-specifika bibliotek:** – en anpassad anslutningsfabrik krävs för att konfigurera återförsöksprincipen från Datastax Spark-anslutningen till Azure Cosmos DB Cassandra-API. Lägg till den `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [maven-koordinater](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) att bifoga i biblioteket i klustret.

## <a name="sample-notebooks"></a>Exempelanteckningsböcker

En lista med Azure Databricks [exempel anteckningsböcker](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) är tillgängliga i Github-lagringsplatsen för hämtning. De här exemplen finns information om hur du ansluter till Azure Cosmos DB Cassandra-API från Spark och utför olika CRUD-åtgärder på data. Du kan också [importera alla anteckningsböcker](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) till din Databricks-klustret arbetsyta och köra den. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Åtkomst till Azure Cosmos DB Cassandra-API från Spark Scala-program

Spark-program som ska köras när automatiserade processer på Azure Databricks skickas till klustret med hjälp av [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) och schemalagda att köras genom Azure Databricks-jobb.

Nedan följer länkar som hjälper dig att komma igång med att skapa Spark Scala-program att interagera med Azure Cosmos DB Cassandra-API.
* [Hur du ansluter till Azure Cosmos DB Cassandra-API från ett Scala Spark-program](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Hur du kör ett Scala Spark-program som en automatiserade jobb på Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Fullständig lista över kodexempel för att arbeta med API för Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Nästa steg

Kom igång med [skapa en Cassandra API-konto, databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program.
