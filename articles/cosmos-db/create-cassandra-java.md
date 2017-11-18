---
title: 'Snabbstart: Cassandra API med Java - Cosmos Azure DB | Microsoft Docs'
description: "Den här snabbstarten visar hur du använder Azure Cosmos DB Cassandra API för att skapa en profil program med Azure-portalen och Java"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: ef611081-0195-4ad8-9b54-b313588e5754
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 2097aa1c158f88a06ab93123f4e374b4245430d6
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="quickstart-build-a-cassandra-app-with-java-and-azure-cosmos-db"></a>Snabbstart: Skapa en Cassandra app med Java och Azure Cosmos DB

Den här snabbstarten visar hur du använder Java och Azure Cosmos DB [Cassandra API](cassandra-introduction.md) att skapa en app för profilen genom att klona ett exempel från GitHub. Denna Snabbstart också vägleder dig genom att skapa ett Azure DB som Cosmos-konto med hjälp av den webbaserade Azure-portalen.

Azure Cosmos-DB är Microsofts globalt distribuerade flera modellen database-tjänsten. Du kan snabbt skapa och fråga dokumentet, tabell, nyckel / värde- och graph-databaser som dra nytta av de globala distribution och skala horisontellt kärnan i Azure Cosmos DB. 

## <a name="prerequisites"></a>Krav

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]Du kan också [försök Azure Cosmos-DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, helt kostnadsfritt och åtaganden.

Åtkomst till förhandsgranskningsprogrammet Azure Cosmos DB Cassandra API. Om du inte använder för åtkomst till ännu, [registrera nu](cassandra-introduction.md#sign-up-now).

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

    ![Visa och kopiera ett användarnamn från Azure portal, anslutningssträngen-sida](./media/create-cassandra-java/keys.png)

2. Använd den ![Kopieringsknapp](./media/create-cassandra-java/copy.png) knappen på höger sida av skärmen för att kopiera värdet KONTAKTA punkt.

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

    Tryck på CTRL + C om du vill stoppa exection av programmet och Stäng fönstret. 
    
    Nu kan du öppna Data Explorer i Azure portal för att se fråga, ändra och arbeta med dessa nya data. 

    ![Visa data i Data Explorer](./media/create-cassandra-java/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du skapar ett Azure Cosmos DB konto, Cassandra databasen och samlingen med hjälp av Data Explorer och kör en app för att göra det via programmering i denna Snabbstart. Nu kan du importera ytterligare data i Azure DB som Cosmos-samlingen. 

> [!div class="nextstepaction"]
> [Importera Cassandra data till Azure Cosmos DB](cassandra-import-data.md)
