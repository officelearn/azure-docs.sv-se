---
title: Migrera data till Azure Cosmos DB Cassandra API-konto
description: Lär dig hur du använder CQL-kopieringskommandot och Spark för att kopiera data från Apache Cassandra till Cassandra-API:et för Azure Cosmos DB.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: f73a201a25bb2f975e8a261a6c21aa7b066c3a7c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247858"
---
# <a name="migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Migrera data till Azure Cosmos DB Cassandra API-konto

Den här självstudien innehåller instruktioner om hur du migrerar Apache Cassandra-data till Cassandra-API:et för Azure Cosmos DB. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Planera för migrering
> * Förutsättningar för migrering
> * Migrera data med hjälp av COPY-kommandot i cqlsh
> * Migrera data med Spark 

## <a name="plan-for-migration"></a>Planera för migrering

Innan du migrerar data till Cassandra-API:et för Azure Cosmos DB bör du beräkna dataflödesbehovet för din arbetsbelastning. I allmänhet rekommenderar vi att du börjar med ett genomsnittligt dataflöde som krävs av CRUD-åtgärderna och att du sedan lägger till det ytterligare dataflödet som krävs för ETL-åtgärder (Extract Transform Load) eller vid belastningstoppar. Du behöver följande information för att planera för migreringen: 

* **Befintlig datastorlek eller beräknad datastorlek:** Definierar den minsta databasstorleken och det minsta dataflödet som krävs. Om du beräknar datastorleken för ett nytt program kan du anta att data är jämnt fördelade över raderna och beräkna värdet genom att multiplicera med datastorleken. 

* **Nödvändigt dataflöde:** Ungefärlig dataflödeshastighet för läsningar (fråga/hämta) och skrivningar (uppdatera/ta bort/infoga). Det här värdet krävs för att beräkna de nödvändiga enheterna för programbegäran tillsammans med datastorleken för stabilt tillstånd.  

* **Hämta schemat:** Anslut till ditt befintliga Cassandra-kluster via cqlsh och exportera schemat från Cassandra: 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

När du har identifierat kraven för din befintliga arbetsbelastning bör du skapa ett konto, en databas och containrar för Azure Cosmos DB baserat på dataflödesbehovet.  

* **Fastställa RU-kostnaden för en åtgärd:** Du kan fastställa RU:erna med hjälp av valfritt SDK för Cassandra-API:et för Azure Cosmos DB. I det här exemplet används .NET-versionen för att hämta RU-kostnaden.

  ```csharp
  var tableInsertStatement = table.Insert(sampleEntity);
  var insertResult = await tableInsertStatement.ExecuteAsync();

  foreach (string key in insertResult.Info.IncomingPayload)
    {
       byte[] valueInBytes = customPayload[key];
       string value = Encoding.UTF8.GetString(valueInBytes);
       Console.WriteLine($"CustomPayload:  {key}: {value}");
    }
  ```

* **Allokera det nödvändiga dataflödet:** Azure Cosmos DB kan automatiskt skala lagringen och dataflödet allteftersom dina behov ökar. Du kan beräkna dataflödesbehovet med hjälp av [RU-kalkylatorn för Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

## <a name="prerequisites-for-migration"></a>Förutsättningar för migrering

* **Skapa tabeller i kontot för Cassandra-API:et för Azure Cosmos DB:** Innan du börjar migrera data skapar du alla dina tabeller i förväg från Azure-portalen eller från cqlsh. Om du migrerar till ett Azure Cosmos DB-konto som har dataflöde på databasnivå ser du till att tillhandahålla en partitionsnyckel när du skapar Azure Cosmos DB-containrar.

* **Öka dataflödet:** Hur lång tid datamigreringen tar beror på hur stort dataflöde du etablerade för tabellerna i Azure Cosmos DB. Öka dataflödet under migreringen. Med ett högre dataflöde kan du undvika begränsningar och migrera snabbare. När du har slutfört migreringen kan du minska dataflödet för att sänka kostnaderna. Mer information om hur du ökar dataflödet finns i [Ange dataflöde](set-throughput.md) för Azure Cosmos DB-containrar. Vi rekommenderar också att du har ett Azure Cosmos DB-konto i samma region som din källdatabas. 

* **Aktivera SSL:** Azure Cosmos DB har stränga säkerhetskrav och säkerhetsstandarder. Det är viktigt att du aktiverar SSL när du kommunicerar med ditt konto. Du kan ange SSL-information när du använder CQL med SSH.

## <a name="options-to-migrate-data"></a>Alternativ för att migrera data

Du kan flytta data från befintliga Cassandra-arbetsbelastningar till Azure Cosmos DB med hjälp av följande alternativ:

* [COPY-kommandot i Cqlsh](#using-cqlsh-copy-command)  
* [Med hjälp av Spark](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrera data med hjälp av COPY-kommandot i cqlsh

[COPY-kommandot i CQL](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) används för att kopiera lokala data till kontot för Cassandra API:et för Azure Cosmos DB. Använd följande steg för att kopiera data:

1. Hämta information om anslutningssträngen för Cassandra-API:ets konto:

   * Logga in på [Azure-portalen](https://portal.azure.com) och navigera till ditt Azure Cosmos DB-konto.

   * Öppna fönstret **Anslutningssträng** som innehåller all information som du behöver för att ansluta till kontot för Cassandra-API:et från cqlsh.

2. Logga in i cqhsh med hjälp av informationen från portalen.

3. Använd COPY-kommandot i CQL för att kopiera lokala data till kontot för Cassandra-API:et.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrera data med Spark 

Använd följande steg för att migrera data till Cassandra-API:et för Azure Cosmos DB med Spark:

- Etablera ett [Azure Databricks-](cassandra-spark-databricks.md) eller [HDInsight-kluster](cassandra-spark-hdinsight.md) 

- Flytta data till målslutpunkten för Cassandra-API:et med hjälp av [tabellkopieringsåtgärden](cassandra-spark-table-copy-ops.md) 

Vi rekommenderar att du migrerar data med hjälp av Spark-jobb om du har data som finns i ett befintligt kluster på virtuella Azure-datorer eller i ett annat moln. I så fall måste Spark konfigureras som mellanhand för en engångsinmatning eller för regelbunden datainmatning. Du kan påskynda migreringen med hjälp av expressroute-anslutningar mellan den lokala infrastrukturen och Azure. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du migrerar data till ett konto för Cassandra-API:et för Azure Cosmos DB. Om du vill lära dig mer om Azure Cosmos DB fortsätter du till avsnittet om begrepp. 

> [!div class="nextstepaction"]
> [Justerbara datakonsekvensnivåer i Azure Cosmos DB](../cosmos-db/consistency-levels.md)


