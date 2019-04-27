---
title: 'Självstudier: Fråga efter data från ett konto för Cassandra-API:et i Azure Cosmos DB'
description: Den här självstudien beskriver hur du frågar efter användardata från ett konto för Cassandra-API:t för Azure Cosmos DB med hjälp av ett Java-program.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
Customer intent: As a developer, I want to build a Java application to query data stored in a Cassandra API account of Azure Cosmos DB so that customers can manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 69a9bc912f2cd52e52ca6403187f993413539ecd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899907"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Självstudier: Fråga efter data från ett konto för Cassandra-API:et i Azure Cosmos DB

Som utvecklare kan du ha program som använder nyckel/värde-par. Du kan använda ett konto för Cassandra-API:t i Azure Cosmos DB för att lagra och fråga efter nyckel/värde-data. Den här självstudien beskriver hur du frågar efter användardata från ett konto för Cassandra-API:t i Azure Cosmos DB med hjälp av ett Java-program. Java-programmet använder [Java-drivrutinen](https://github.com/datastax/java-driver) och frågar efter användardata, till exempel användar-ID, användarnamn och användarens stad. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Fråga efter data från en Cassandra-tabell
> * Kör appen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Den här artikeln tillhör en självstudiekursen i flera delar. Slutför de föregående stegen som beskriver hur du skapar kontot, nyckelutrymmet och tabellen för Cassandra-API:t och hur du [läser in exempeldata i tabellen](cassandra-api-load-data.md) innan du börjar. 

## <a name="query-data"></a>Söka i data

Använd följande steg för att fråga data från ditt Cassandra API-konto:

1. Öppna filen `UserRepository.java` under mappen `src\main\java\com\azure\cosmosdb\cassandra`. Lägg till följande kodblock. Den här koden har tre metoder: 

   * För att fråga efter alla användare i databasen
   * För att fråga en specifik användare som filtreras efter användar-ID
   * För att ta bort en tabell

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Öppna filen `UserProfile.java` under mappen `src\main\java\com\azure\cosmosdb\cassandra`. Den här klassen innehåller huvudmetoden som anropar datainmatningsmetoderna createKeyspace och createTable som du definierade tidigare. Lägg nu till följande kod som frågar efter alla användare eller en specifik användare:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Kör Java-appen
1. Öppna en kommandotolk eller ett terminalfönster. Klistra in följande kodblock. 

   Den här koden ändrar katalogen (cd) till sökvägen till mappen där du skapade projektet. Sedan kör den kommandot `mvn clean install` för att generera filen `cosmosdb-cassandra-examples.jar` i målmappen. Slutligen kör den Java-programmet.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Öppna nu **Datautforskaren** på Azure-portalen och bekräfta att användartabellen har tagits bort.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, Azure Cosmos-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du frågar efter data från ett konto för Cassandra-API:t i Azure Cosmos DB. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Migrera data till ett konto för Cassandra-API:et](cassandra-import-data.md)


