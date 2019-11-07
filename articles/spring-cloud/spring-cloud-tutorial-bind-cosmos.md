---
title: Binda en Azure Cosmos DB till ditt Azure våren Cloud-program | Microsoft Docs
description: Lär dig hur du binder Azure Cosmos DB till ditt Azure våren Cloud-program
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 76a2b9f305f041a19b8d7ace8234a804825f6a0e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607137"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Självstudie: bind en Azure Cosmos DB till ditt Azure våren Cloud-program

Med Azure våren Cloud kan du binda utvalda Azure-tjänster till dina program automatiskt, i stället för att manuellt konfigurera ditt våren Boot-program. Den här artikeln visar hur du binder ditt program till en Azure Cosmos DB.

Krav:
* En distribuerad Azure våren Cloud-instans.  Följ vår [snabb start](spring-cloud-quickstart-launch-app-cli.md) för att komma igång.
* Ett Azure Cosmos DB konto med lägsta behörighets nivå för deltagare.

## <a name="bind-azure-cosmos-db"></a>Bind Azure Cosmos DB

Azure Cosmos DB har fem olika API-typer som stöder bindning:

1. Skapa en Azure Cosmos DB-databas. I [den här artikeln hittar du](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) hjälp med att skapa databasen. Anteckna namnet på databasen. Vi heter oss `testdb`.

1. Lägg till något av följande beroenden i ditt våren Cloud programs `pom.xml` enligt din API-typ.
    
    #### <a name="api-type-core-sql"></a>API-typ: Core (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>API-typ: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>API-typ: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>API-typ: Gremlin (graf)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>API-typ: Azure-tabell

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Uppdatera den aktuella distributionen med `az spring-cloud app update` eller skapa en ny distribution för den här ändringen med `az spring-cloud app deployment create`.  Dessa kommandon antingen uppdaterar eller skapar programmet med det nya beroendet.

1. Gå till sidan Azure våren Cloud service i Azure Portal. Det här är samma program som du uppdaterade eller distribuerade i föregående steg. Hitta **program instrument panelen** och välj det program som du vill binda till Cosmos dB. Välj sedan `Service binding` och välj knappen `Create service binding`. Fyll i formuläret, Välj **bindnings typ** `Azure Cosmos DB`, API-typ, ditt databas namn och Azure Cosmos DB kontot.

    > [!NOTE]
    > Använd ett nyckel utrymme för databas namnet om du använder Cassandra.

1. Starta om programmet genom att välja knappen **Starta** om på program sidan.

1. För att säkerställa att tjänsten är korrekt bunden väljer du bindnings namnet och kontrollerar dess information. Fältet `property` bör likna följande:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du binder ditt Azure våren Cloud-program till en CosmosDB.  Fortsätt till nästa självstudie om du vill lära dig hur du binder ditt program till ett Azure Redis Cache.

> [!div class="nextstepaction"]
> [BIND ett Azure våren Cloud-program till en Azure Redis cache](spring-cloud-tutorial-bind-redis.md).
