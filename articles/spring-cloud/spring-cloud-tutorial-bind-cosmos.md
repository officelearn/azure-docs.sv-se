---
title: Självstudiekurs - Bind en Azure Cosmos DB till ditt Azure Spring Cloud-program
description: I den här självstudien kan du lära dig hur du binder Azure Cosmos DB till ditt Azure Spring Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 1566b6ab59e858217adcf6818e1d62f851f37eb1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277570"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Binda en Azure Cosmos DB-databas till ditt Azure Spring Cloud-program

I stället för att konfigurera dina Spring Boot-program manuellt kan du automatiskt binda utvalda Azure-tjänster till dina program med hjälp av Azure Spring Cloud. Den här artikeln visar hur du binder ditt program till en Azure Cosmos DB-databas.

Krav:

* En distribuerad Azure Spring Cloud-instans. Följ vår [snabbstart om distribution via Azure CLI](spring-cloud-quickstart-launch-app-cli.md) för att komma igång.
* Ett Azure Cosmos DB-konto med en lägsta behörighetsnivå för Deltagare.

## <a name="bind-azure-cosmos-db"></a>Bind Azure Cosmos DB

Azure Cosmos DB har fem olika API-typer som stöder bindning. Följande procedur visar hur du använder dem:

1. Skapa en Azure Cosmos DB-databas. Mer hjälp finns i snabbstarten för [att skapa en databas.](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) 

1. Registrera namnet på databasen. För den här proceduren **testas databasnamnetb**.

1. Lägg till något av följande beroenden i Azure Spring Cloud-programmets pom.xml-fil. Välj det beroende som är lämpligt för din API-typ.

    * API-typ: Kärna (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * API-typ: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API-typ: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API-typ: Gremlin (diagram)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API-typ: Azure-tabell

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Används `az spring-cloud app update` för att uppdatera den `az spring-cloud app deployment create` aktuella distributionen eller använda för att skapa en ny distribution. Dessa kommandon kommer antingen att uppdatera eller skapa programmet med det nya beroendet.

1. Gå till din Azure Spring Cloud-tjänstsida i Azure-portalen. Gå till **Programinstrumentpanelen** och välj programmet som ska bindas till Azure Cosmos DB. Det här programmet är samma som du har uppdaterat eller distribuerat i föregående steg.

1. Välj **Tjänstbindning**och välj **Skapa tjänstbindning**. Om du vill fylla i formuläret väljer du:
   * **Värdet för bindningstypen** **Azure Cosmos DB**.
   * API-typen.
   * Ditt databasnamn.
   * Azure Cosmos DB-konto.

    > [!NOTE]
    > Om du använder Cassandra använder du ett nyckelutrymme för databasnamnet.

1. Starta om programmet genom att välja **Starta om** på programsidan.

1. Om du vill vara säkra på att tjänsten är korrekt bunden markerar du bindningsnamnet och verifierar dess information. Fältet `property` bör likna det här exemplet:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du binder ditt Azure Spring Cloud-program till en Azure Cosmos DB-databas. Om du vill veta hur du binder ditt program till en Azure Cache för Redis-cache fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Lär dig hur du binder till en Azure Cache för Redis-cache](spring-cloud-tutorial-bind-redis.md)
