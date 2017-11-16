---
title: 'Azure Cosmos DB: Utveckla med Cassandra API i Java | Microsoft Docs'
description: "Lär dig att utveckla med Azure Cosmos DB Cassandra API: et med Java"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 6732d883-835c-481f-98e1-287893530948
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 11/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 7e1a17517890f8ed738b6afdcbe073a2bf1ebc6f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmosdb-develop-with-the-cassandra-api-in-java"></a>Azure CosmosDB: Utveckla med Cassandra API i Java

Azure Cosmos-DB är Microsofts globalt distribuerade flera modellen database-tjänsten. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här kursen visar hur du skapar ett Azure DB som Cosmos-konto med Azure-portalen och sedan skapa en Cassandra Table(documentdb-partition-data.md#partition-keys) med hjälp av den [Cassandra API](cassandra-introduction.md). Genom att definiera en primär nyckel när du skapar en tabell kan är ditt program beredd att skala utan problem när dina data växer. 

Den här kursen ingår följande uppgifter med hjälp av Cassandra-API:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto
> * Skapa en keyspace och en tabell med en primärnyckel
> * Infoga data
> * Frågedata
> * Granska servicenivåavtal

## <a name="prerequisites"></a>Krav

Åtkomst till förhandsgranskningsprogrammet Azure Cosmos DB Cassandra API. Om du inte använder för åtkomst till ännu, [registrera nu](https://aka.ms/cosmosdb-cassandra-signup).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]Du kan också [försök Azure Cosmos-DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, helt kostnadsfritt och åtaganden.

Följande gäller också: 

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett [Maven](http://maven.apache.org/)-binärarkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan skapa en databas för dokument, måste du skapa en Cassandra-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Vi ska klona en Cassandra app från GitHub, ange anslutningssträngen och kör den. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en git-terminalfönster, till exempel git bash och använda den `cd` kommando för att ändra till en mapp att installera sample-appen. 

    ```bash
    cd "C:\git-samples"
    ```

2. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av sample-appen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du är intresserad av att lära sig hur databasresurser skapas i koden kan granska du följande kodavsnitt. Annars kan du gå vidare till [uppdatera anslutningssträngen](#update-your-connection-string). Dessa kodavsnitt hämtas från src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.  

* Cassandra värd, port, användarnamn, lösenord och SSL-alternativ har angetts. Informationen i anslutningssträngen hämtas från sidan sträng i Azure-portalen.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* Den `cluster` ansluter Azure Cosmos DB Cassandra-API: et och returnerar en session för att komma åt.

    ```java
    return cluster.connect();
    ```

Följande kodavsnitt är från filen src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.

* Skapa en ny keyspace.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Skapa en ny tabell.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Infoga användaren entiteter med ett förberedd instruktion-objekt.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Frågan att hämta användarinformation om alla.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Fråga om du vill hämta information om en enskild användare.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. Detta gör att din app för att kommunicera med databasen värdbaserade.

1. I den [Azure-portalen](http://portal.azure.com/), klickar du på **anslutningssträngen**. 

    ![Visa och kopiera ett användarnamn från Azure portal, anslutningssträngen-sida](./media/tutorial-develop-cassandra-java/keys.png)

2. Använd den ![Kopieringsknappen](./media/tutorial-develop-cassandra-java/copy.png) knappen på höger sida av skärmen för att kopiera värdet KONTAKTA punkt.

3. Öppna den `config.properties` fil från C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources mapp. 

3. Klistra in KONTAKTA punktvärdet från portalen över `<Cassandra endpoint host>` på rad 2.

    Rad 2 i config.properties bör nu se ut 

    `cassandra_host=cosmos-db-quickstarts.documents.azure.com`

3. Gå tillbaka till portalen och kopiera värdet för användarnamn. Efter användarnamn värdet från portalen via `<cassandra endpoint username>` på rad 4.

    4 branschspecifika config.properties bör nu se ut 

    `cassandra_username=cosmos-db-quickstart`

4. Gå tillbaka till portalen och kopiera värdet lösenord. Klistra in lösenordsvärdet från portalen över `<cassandra endpoint password>` på rad 5.

    Rad 5 i config.properties bör nu se ut 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. På rad 6, om du vill använda ett visst SSL-certifikat, Ersätt `<SSL key store file location>` med platsen för SSL-certifikatet. Om värdet inte anges, används JDK-certifikat installeras på < JAVA_HOME > eller jre/lib/security/cacerts. 

6. Om du har ändrat rad 6 för att använda en specifik SSL-certifikat, Uppdatera rad 7 att använda lösenordet för certifikatet. 

7. Spara filen config.properties.

## <a name="run-the-app"></a>Kör appen

1. I fönstret git terminal `cd` till mappen azure-cosmosdb-cassandra-java-getting-started\java-examples.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. I fönstret git terminal att använda följande kommando för att generera cosmosdb-cassandra-examples.jar-filen.

    ```git
    mvn clean install
    ```

3. Kör följande kommando för att starta Java-programmet i fönstret git terminal.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    Fönstret terminal visar meddelanden keyspace och tabellen har skapats. Sedan markerar och returnerar alla användare i tabellen och visar utdata, och sedan väljer en rad-ID: t och visar värdet.  
    
    Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du gör följande i denna Snabbstart:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto
> * Skapa en keyspace och en tabell med en primärnyckel
> * Infoga data
> * Frågedata
> * Reivew servicenivåavtal

Nu kan du importera ytterligare data i Azure DB som Cosmos-samlingen. 

> [!div class="nextstepaction"]
> [Importera Cassandra data till Azure Cosmos DB](cassandra-import-data.md)
