---
title: 'Azure Cosmos DB: Utveckla med API för Cassandra i Java | Microsoft Docs'
description: Lär dig utveckla med Azure Cosmos DB:s API för Cassandra med Java
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
editor: ''
tags: ''
ms.assetid: 6732d883-835c-481f-98e1-287893530948
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 11/15/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 8a4b26227b162268afd965f89ab2806b530e75be
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmosdb-develop-with-the-cassandra-api-in-java"></a>Azure Cosmos DB: Utveckla med API för Cassandra i Java

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här självstudien visar hur du skapar ett Azure Cosmos DB-konto med Azure Portal och sedan skapar en Cassandra-tabell (sql-api-partition-data.md#partition-keys) med hjälp av [API för Cassandra](cassandra-introduction.md). Genom att definiera en primärnyckel när du skapar en tabell, är ditt program förberett att skala utan problem allteftersom dina data växer. 

Den här självstudien beskriver följande uppgifter som använder API för Cassandra:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto
> * Skapa ett Keyspace och en tabell med en primärnyckel
> * Infoga data
> * Frågedata
> * Granska serviceavtal

## <a name="prerequisites"></a>Nödvändiga komponenter

Åtkomst till förhandsgranskningsprogrammet för Azure Cosmos DB Cassandra-API. Om du inte har ansökt om åtkomst än kan du [registrera dig nu](https://aka.ms/cosmosdb-cassandra-signup).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Du kan även [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden.

Följande gäller också: 

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett [Maven](http://maven.apache.org/)-binärarkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en dokumentdatabas måste du skapa ett Cassandra-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Vi ska klona en Cassandra API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till en mapp där du vill installera exempelappen. 

    ```bash
    cd "C:\git-samples"
    ```

2. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). Alla de här kodavsnitten hämtas från src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.  

* Cassandra host, port, user name, password, and SSL options are set. Information om anslutningssträngen kommer från sidan med anslutningssträngen i Azure-portalen.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* `cluster` ansluter till Azure Cosmos DB Cassandra-API och kör en session för åtkomst.

    ```java
    return cluster.connect();
    ```

Följande kodavsnitt kommer från filen src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.

* Skapa ett nytt keyspace.

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

* Infoga användarentiteter med ett förberett instruktionsobjekt.

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

* Fråga för att hämta all användarinformation.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Fråga för att hämta en användares information.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. På så vis kan appen kommunicera med den värdbaserade databasen.

1. I [Azure-portalen](http://portal.azure.com/) klickar du på **Anslutningssträng**. 

    ![Visa och kopiera ett användarnamn från Azure-portalen, sidan Anslutningssträng](./media/tutorial-develop-cassandra-java/keys.png)

2. Använd ![Knappen Kopiera](./media/tutorial-develop-cassandra-java/copy.png) knappen på höger sida av skärmen för att kopiera värdet KONTAKTPUNKT.

3. Öppna filen `config.properties` från mappen C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources. 

3. Klistra in KONTAKTPUNKT-värdet från portalen över `<Cassandra endpoint host>` på rad 2.

    Rad 2 i config.properties bör nu se ut ungefär så här 

    `cassandra_host=cosmos-db-quickstarts.documents.azure.com`

3. Gå tillbaka till portalen och kopiera värdet ANVÄNDARNAMN. Klistra in värdet för ANVÄNDARNAMN från portalen över `<cassandra endpoint username>` på rad 4.

    Rad 4 i config.properties bör nu se ut ungefär så här 

    `cassandra_username=cosmos-db-quickstart`

4. Gå tillbaka till portalen och kopiera värdet LÖSENORD. Klistra in värdet för LÖSENORD från portalen över `<cassandra endpoint password>` på rad 5.

    Rad 5 i config.properties bör nu se ut ungefär så här 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Om du vill använda ett visst SSL-certifikat ersätter du `<SSL key store file location>` på rad 6 med platsen för SSL-certifikatet. Om inget värde anges kommer JDK-certifikatet som är installerat på <JAVA_HOME>/jre/lib/security/cacerts att användas. 

6. Om du har ändrat rad 6 så att ett visst SSL-certifikat ska användas uppdaterar du rad 7 för att använda lösenordet för det certifikatet. 

7. Spara filen config.properties.

## <a name="run-the-app"></a>Kör appen

1. I git-terminalfönstret `cd` till mappen azure-cosmosdb-cassandra-java-getting-started\java-examples.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. I git-terminalfönstret använder du följande kommando för att generera filen cosmosdb-cassandra-examples.jar.

    ```git
    mvn clean install
    ```

3. I git-terminalfönstret kör du följande kommando för att starta Java-programmet.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    Terminalfönstret visar aviseringar om att keyspace och tabell har skapats. Det markerar och returnerar sedan alla användare i tabellen och visar utdata, och väljer sedan ett rad-id och visar värdet.  
    
    Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att göra följande:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto
> * Skapa ett Keyspace och en tabell med en primärnyckel
> * Infoga data
> * Frågedata
> * Granska serviceavtal

Du kan nu importera ytterligare data till Azure Cosmos DB-samlingen. 

> [!div class="nextstepaction"]
> [Importera Cassandra-data till Azure Cosmos DB](cassandra-import-data.md)
