---
title: Fråga efter data från ett konto för Cassandra-API:et för Azure Cosmos DB
description: Den här artikeln beskriver hur du frågar efter användardata från ett konto för Cassandra-API:et för Azure Cosmos DB med hjälp av ett Java-program.
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: a06e7e6159953bfeffa966759d29b91bbcbafd37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739217"
---
# <a name="tutorial-query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Självstudie: Fråga efter data från ett konto för Cassandra-API:et för Azure Cosmos DB

Den här självstudien beskriver hur du frågar efter användardata från ett konto för Cassandra-API:et för Azure Cosmos DB med hjälp av ett Java-program. Java-programmet använder [Java-drivrutinen](https://github.com/datastax/java-driver) och frågar efter användardata, till exempel användar-ID, användarnamn och användarens stad. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Fråga efter data från en Cassandra-tabell
> * Kör appen

## <a name="prerequisites"></a>Nödvändiga komponenter

* Den här artikeln tillhör en självstudiekursen i flera delar. Slutför de föregående stegen som beskriver hur du [skapar kontot, nyckelutrymmet och tabellen för Cassandra-API:et och hur du [läser in exempeldata i tabellen](cassandra-api-load-data.md) innan du börjar. 

## <a name="query-data"></a>Söka i data

Använd följande steg för att fråga data från ditt Cassandra API-konto:

1. Öppna filen `UserRepository.java` under mappen `src\main\java\com\azure\cosmosdb\cassandra`. Lägg till följande kodblock. Den här koden har tre metoder: 

   * För att fråga efter alla användare i databasen
   * För att fråga en specifik användare som filtreras efter användar-ID
   * För att ta bort en tabell.

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

## <a name="next-steps"></a>Nästa steg

* I den här självstudien har du lärt dig hur du frågar efter data från ett konto för Cassandra-API:et för Azure Cosmos DB. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Migrera data till ett konto för Cassandra-API:et](cassandra-import-data.md)


