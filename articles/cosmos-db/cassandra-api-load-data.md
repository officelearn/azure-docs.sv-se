---
title: Läsa in exempeldata i en tabell för Cassandra API:et för Azure Cosmos DB med hjälp av ett Java-program | Microsoft Docs
description: Den här artikeln beskriver hur du läser in exempelanvändardata i en tabell i ett konto för Cassandra-API:et för Azure Cosmos DB med hjälp av ett Java-program.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 662d4b8812ca4b92c1130b9c2c38771e7ec30a06
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394009"
---
# <a name="load-sample-data-into-an-azure-cosmos-db-cassandra-api-table"></a>Läsa in exempeldata i en tabell för Cassandra-API:et för Azure Cosmos DB

Den här självstudien beskriver hur du läser in exempelanvändardata i en tabell i ett konto för Cassandra-API:et för Azure Cosmos DB med hjälp av ett Java-program. Java-programmet använder [Java-drivrutinen](https://github.com/datastax/java-driver) och läser in användardata, till exempel användar-ID, användarnamn och användarens stad. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Läsa in data i en Cassandra-tabell
> * Kör appen

## <a name="prerequisites"></a>Nödvändiga komponenter

* Den här artikeln tillhör en självstudiekursen i flera delar. Innan du börjar med det här dokumentet måste du [skapa kontot, nyckelutrymmet och tabellen för Cassandra-API:et](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Läsa in data i tabellen

Öppna filen ”UserRepository.java” under mappen ”src\main\java\com\azure\cosmosdb\cassandra” och lägg till koden för att infoga fälten user_id, user_name och user_bcity i tabellen:

```java
/**
* Insert a row into user table
*
* @param id   user_id
* @param name user_name
* @param city user_bcity
*/
public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
}

/**
* Create a PrepareStatement to insert a row to user table
*
* @return PreparedStatement
*/
public PreparedStatement prepareInsertStatement() {
    final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
    return session.prepare(insertStatement);
}
```
 
Öppna filen ”UserProfile.java” under mappen ”src\main\java\com\azure\cosmosdb\cassandra”. Den här klassen innehåller huvudmetoden som anropar metoderna createKeyspace och createTable som du definierade tidigare. Lägg till följande kod för att infoga lite exempeldata i tabellen för Cassandra-API:et.

```java
//Insert rows into user table
PreparedStatement preparedStatement = repository.prepareInsertStatement();
    repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
    repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
    repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
    repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
    repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
```

## <a name="run-the-app"></a>Kör appen

Öppna kommandotolken eller ett terminalfönster och ändra sökvägen till mappen till den plats där du skapade projektet. Kör kommandot ”mvn clean install” för att generera filen cosmosdb-cassandra-examples.jar i målmappen och kör programmet. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Nu kan du öppna Datautforskaren på Azure-portalen och bekräfta att användarinformationen har lagts till i tabellen.
    
## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du läser in exempeldata till ett konto för Cassandra-API:et för Azure Cosmos DB. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Fråga efter data från kontot för Cassandra-API:et](cassandra-api-query-data.md)
