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
ms.openlocfilehash: c1fb4c27f897e3c0952ed6419e167613ac8204f7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223499"
---
# <a name="query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Fråga efter data från ett konto för Cassandra-API:et för Azure Cosmos DB

Den här självstudien beskriver hur du frågar efter användardata från ett konto för Cassandra-API:et för Azure Cosmos DB med hjälp av ett Java-program. Java-programmet använder [Java-drivrutinen](https://github.com/datastax/java-driver) och frågar efter användardata, till exempel användar-ID, användarnamn och användarens stad. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Fråga efter data från en Cassandra-tabell
> * Kör appen

## <a name="prerequisites"></a>Nödvändiga komponenter

* Den här artikeln tillhör en självstudiekursen i flera delar. Slutför de föregående stegen som beskriver hur du [skapar kontot, nyckelutrymmet och tabellen för Cassandra-API:et](create-cassandra-api-account-java.md) och hur du [läser in exempeldata i tabellen](cassandra-api-load-data.md) innan du börjar. 

## <a name="query-data"></a>Frågedata

Öppna filen `UserRepository.java` under mappen `src\main\java\com\azure\cosmosdb\cassandra`. Lägg till följande kodblock. Den här koden tillhandahåller tre funktioner: en som frågar efter alla användare i databasen, en som frågar efter en specifik användare filtrerat efter användar-ID och en som tar bort en tabell. 

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

Öppna filen `UserProfile.java` under mappen `src\main\java\com\azure\cosmosdb\cassandra`. Den här klassen innehåller huvudmetoden som anropar datainmatningsmetoderna createKeyspace och createTable som du definierade tidigare. Lägg nu till följande kod som frågar efter alla användare eller en specifik användare:

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


