---
title: Skapa ett Azure Cosmos DB Cassandra API-konto, en databas och en tabell med hjälp av ett Java-program
description: Den här artikeln visar hur du skapar ett Cassandra API-konto och lägger till en databas (kallas även ett nyckelutrymme) och en tabell i kontot med hjälp av ett Java-program.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
services: cosmos-db
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: e5b412042d0a872443c0b30c774dfd4926555a5a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221680"
---
# <a name="create-an-azure-cosmos-db-cassandra-api-account-database-and-a-table-by-using-a-java-application"></a>Skapa ett Azure Cosmos DB Cassandra API-konto, en databas och en tabell med hjälp av ett Java-program

Den här självstudien beskriver hur du använder ett Java-program för att skapa ett Cassandra API-konto i Azure Cosmos DB och lägger till en databas (kallas även ett nyckelutrymme) och en tabell. Java-programmet använder [Java-drivrutinen](https://github.com/datastax/java-driver) för att skapa en användardatabas som innehåller information såsom användar-ID, användarnamn och användarens stad.  

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Cassandra-databaskonto
> * Hämta kontoanslutningssträngen
> * Skapa Maven-projekt och beroenden
> * Lägga till en databas och en tabell
> * Kör appen

## <a name="prerequisites"></a>Nödvändiga komponenter 

* Om du inte har en Azure-prenumeration skapar du ett  [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  innan du börjar. Du kan även  [testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)  utan en Azure-prenumeration, utan kostnad och åtaganden. 

* Skaffa den senaste versionen av [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 

* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) [Maven](http://maven.apache.org/)-binärarkivet 
  - I Ubuntu kan du köra  `apt-get install maven`  för att installera Maven. 

## <a name="create-a-database-account"></a>Skapa ett databaskonto 

1. Logga in på  [Azure-portalen](https://portal.azure.com/). 

2. Välj  **Skapa en resurs** > **Databaser** > **Azure Cosmos DB**. 

3. I fönsterrutan  **Nytt konto**  anger du inställningarna för det nya Azure Cosmos DB-kontot. 

   |Inställning   |Föreslaget värde  |Beskrivning  |
   |---------|---------|---------|
   |ID   |   Ange ett unikt namn    | Ange ett unikt namn som identifierar Azure Cosmos DB-kontot. <br/><br/>Eftersom cassandra.cosmosdb.azure.com läggs till det ID du anger för att skapa din kontaktpunkt bör du använda ett unikt men identifierbart ID.         |
   |API    |  Cassandra   |  API:n avgör vilken typ av konto som skapas. <br/> Välj **Cassandra** eftersom du i den här artikeln skapar en databas med breda kolumner som frågor kan köras mot med hjälp av CQL-syntax.  |
   |Prenumeration    |  Din prenumeration        |  Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot.        |
   |Resursgrupp   | Ange ett namn    |  Välj  **Skapa ny** och ange sedan ett nytt resursgruppsnamn för ditt konto. För enkelhetens skull kan du använda samma namn som för ditt ID.    |
   |Plats    |  Välj den region som är närmast dina användare    |  Välj den geografiska plats som ska vara värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.    |

   ![Skapa konto med portalen](./media/create-cassandra-api-account-java/create-account.png)

4. Välj sedan  **Skapa**. <br/>Det tar några minuter att skapa kontot. När resursen har skapats visas meddelandet **Distributionen lyckades** i det högra hörnet i portalen.

## <a name="get-the-connection-details-of-your-account"></a>Hämta anslutningsinformationen för ditt konto  

Hämta information om anslutningssträngen från Azure-portalen och kopiera den till Java-konfigurationsfilen. På så vis kan appen kommunicera med den värdbaserade databasen. 

1. Från  [Azure-portalen](http://portal.azure.com/) navigerar du till ditt Cosmos DB-konto. 

2. Öppna fönsterrutan  **Anslutningssträng**.  

3. Kopiera värdena **KONTAKTPUNKT**, **PORT**, **ANVÄNDARNAMN** och **PRIMÄRT LÖSENORD** för användning i nästa steg.

## <a name="create-maven-project-dependencies-and-utility-classes"></a>Skapa Maven-projekt, beroenden och verktygsklasser 

Det Java-exempelprojekt som du använder i den här artikeln finns i GitHub. Du kan ladda ned det från lagringsplatsen [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started). 

När du har laddat ned filerna uppdaterar informationen om anslutningssträngen i filen `java-examples\src\main\resources\config.properties` och kör den.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_host = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Du kan även skapa exemplet från början.  

1. Från terminalen eller kommandotolken skapar du ett nytt Maven-projekt med namnet Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Leta upp mappen `cassandra-demo`. Använd en textredigerare och öppna den `pom.xml`-fil som genererades. 

   Lägg till dee Cassandra-beroenden och plugin-program som krävs av projektet enligt det som visas i filen [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml).  

3. I mappen `cassandra-demo\src\main` skapar du en ny mapp med namnet `resources`.  I resursmappen lägger du till filerna config.properties och log4j.properties:

   - Filen [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) lagrar värdena för Azure Cosmos DB Cassandra API-anslutningsslutpunkt och nyckel. 
   
   - Filen [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) definierar den loggningsnivå som krävs när du interagerar med API för Cassandra.  

4. Sedan går du till mappen `src/main/java/com/azure/cosmosdb/cassandra/`. I Cassandra-mappen skapar du en till mapp med namnet `utils`. Den nya mappen lagrar de verktygsklasser som krävs för att ansluta till Cassandra API-kontot. 

   Lägg till klassen [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) för att skapa klustret och för att öppna och stänga Cassandra-sessioner. Klustret ansluter till Azure Cosmos DB Cassandra-API och kör en session för åtkomst. Använd klassen [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) (Konfigurationer) för att läsa information om anslutningssträng från filen config.properties. 

5. Java-exemplet skapar en databas med användarinformation såsom användarnamn, användar-ID och användarens stad. Du måste definiera get- och set-metoder för att komma åt användarinformation i main-funktionen.
 
   Skapa en [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java)-klass i mappen `src/main/java/com/azure/cosmosdb/cassandra/` med get- och set-metoder. 

## <a name="add-a-database-and-a-table"></a>Lägga till en databas och en tabell  

Det här avsnittet beskriver hur du lägger till en databas (nyckelutrymme) och en tabell med hjälp av Cassandra Query Language (CQL). Läs mer om CQL-syntax för dessa kommandon i frågesyntaxen för [skapa nyckelutrymme](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateKeyspace.html) och [skapa tabell](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateTable.html#cqlCreateTable). 

1. I mappen `src\main\java\com\azure\cosmosdb\cassandra` skapar du en ny mapp med namnet `repository`. 

2. Skapa sedan Java-klassen `UserRepository` och lägg till följande kod i den: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. Leta upp mappen `src\main\java\com\azure\cosmosdb\cassandra` och skapa en ny undermapp med namnet `examples`.

4. Skapa sedan Java-klassen `UserProfile`. Den här klassen innehåller huvudmetoden som anropar metoderna createKeyspace och createTable som du definierade tidigare: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>Kör appen 

1. Öppna en kommandotolk eller ett terminalfönster. Klistra in följande kodblock. 

   Den här koden ändrar katalogen (cd) till sökvägen till mappen där du skapade projektet. Sedan kör den kommandot `mvn clean install` för att generera filen `cosmosdb-cassandra-examples.jar` i målmappen. Slutligen kör den Java-programmet.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   Terminalfönstret visar aviseringar om att keyspace och tabell har skapats. 
   
2. I Azure-portalen öppnar du nu **Datautforskaren** för att bekräfta att nyckelutrymmet och tabellen skapades.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du skapar ett Azure Cosmos DB Cassandra API-konto, en databas och en tabell med hjälp av ett Java-program. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Läsa in exempeldata i tabellen för Cassandra-API:et](cassandra-api-load-data.md).
