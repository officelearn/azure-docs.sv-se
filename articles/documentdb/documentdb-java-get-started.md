---
title: "Självstudiekurs om NoSQL: Azure DocumentDB Java SDK | Microsoft Docs"
description: "En självstudiekurs om NoSQL som skapar en onlinedatabas och ett Java-konsolprogram med DocumentDB Java SDK:n. Azure DocumentDB är en NoSQL-databas för JSON."
keywords: nosql tutorial, online database, java console application
services: documentdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: da7907ffc515ea2e3040075c93bcd53840cf3ff5
ms.lasthandoff: 03/28/2017


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>Självstudiekurs om NoSQL: skapa ett DocumentDB Java-konsolprogram
> [!div class="op_single_selector"]
> * [NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js för MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Välkommen till NoSQL-självstudier för Azure DocumentDB Java SDK! När du har genomfört den här självstudiekursen har du en konsolapp som skapar och skickar frågor till DocumentDB-resurser.

Vi går igenom:

* Skapa och ansluta till ett DocumentDB-konto
* Konfigurera en Visual Studio-lösning
* Skapa en onlinedatabas
* Skapa en samling
* Skapa JSON-dokument
* Skicka frågor till samlingen
* Skapa JSON-dokument
* Skicka frågor till samlingen
* Ersätta ett dokument
* Ta bort ett dokument
* Ta bort databasen

Nu sätter vi igång!

## <a name="prerequisites"></a>Krav
Se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). Du kan också använda [Azure DocumentDB-emulatorn](documentdb-nosql-local-emulator.md) för den här självstudien.
* [Git](https://git-scm.com/downloads)
* [Java Development Kit (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-a-documentdb-account"></a>Steg 1: Skapa ett DocumentDB-konto
Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Klona GitHub-projektet](#GitClone). Om du använder DocumentDB-emulatorn följer du stegen i [Azure DocumentDB-emulatorn](documentdb-nosql-local-emulator.md) för att konfigurera emulatorn och gå vidare till [Klona GitHub-projektet](#GitClone).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="GitClone"></a>Steg 2: Klona GitHub-projekt
Kom igång genom att klona GitHub-lagringsplatsen. Gå till [Komma igång med DocumentDB och Java](https://github.com/Azure-Samples/documentdb-java-getting-started). Från en lokal katalog kör du till exempel följande för att hämta exempelprojektet lokalt.

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

Katalogen innehåller en `pom.xml` för projektet och en `src`-mapp som innehåller Java-källkod inklusive `Program.java` som visar hur du utför enkla åtgärder med Azure DocumentDB som att skapa dokument och fråga data inom en samling. `pom.xml` innehåller ett beroende på [DocumentDB Java SDK på Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Steg 3: Anslut till ett DocumentDB-konto
Gå sedan tillbaka till [Azure Portal](https://portal.azure.com) för att hämta din slutpunkt och primära huvudnyckel. DocumentDB-slutpunkten och primärnyckeln behövs för att appen ska veta var ditt program ska ansluta till och för att DocumentDB ska lita på ditt programs anslutning.

Navigera till ditt DocumentDB-konto i Azure Portal och klicka sedan på **Nycklar**. Kopiera URI:n från portalen och klistra in den i `<your endpoint URI>` i Program.java-filen. Kopiera sedan PRIMÄRNYCKELN från portalen och klistra in den i `<your key>`.

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Skärmdump av Azure Portal som används i NoSQL-självstudiekursen för att skapa ett Java-konsolprogram. Visar DocumentDB-konto med hubben AKTIV markerad, knappen NYCKLAR markerad i bladet DocumentDB-konto och värdena URI, PRIMÄRNYCKEL och SEKUNDÄRNYCKEL markerade i bladet Nycklar][keys]

## <a name="step-4-create-a-database"></a>Steg 4: Skapa en databas
Din DocumentDB-[databas](documentdb-resources.md#databases) kan skapas med hjälp av metoden [createDatabase](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) för **DocumentClient**-klassen. En databas är en logisk behållare för JSON-dokumentlagring, partitionerad över samlingarna.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Steg 5: Skapa en samling
> [!WARNING]
> **createCollection** skapar en ny samling med reserverat dataflöde, vilket får priskonsekvenser. Mer information finns på vår [prissättningssida](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

En [samling](documentdb-resources.md#collections) kan skapas med metoden [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) för **DocumentClient**-klassen. En samling är en behållare för JSON-dokument och associerad JavaScript-applogik.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // DocumentDB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Steg 6: Skapa JSON-dokument
Ett [dokument](documentdb-resources.md#documents) kan skapas med hjälp av [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-)-metoden för klassen **DocumentClient**. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Vi kan nu infoga ett eller flera dokument. Om du redan har data som du vill lagra i databasen kan du använda [datamigreringsverktyget](documentdb-import-data.md) för DocumentDB för att importera datan till en databas.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagram som illustrerar den hierarkiska relationen mellan kontot, onlinedatabasen, samlingen och dokumenten som används i NoSQL-självstudiekursen för att skapa ett Java-konsolprogram](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Steg 7: Skicka frågor till DocumentDB-resurser
DocumentDB stöder omfattande [frågor](documentdb-sql-query.md) mot JSON-dokument som lagras i varje samling.  Följande exempelkod visar hur du frågar dokument i DocumentDB med hjälp av SQL-syntaxen med [queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-)-metoden.

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Steg 8: Ersätta JSON-dokument
DocumentDB stöder uppdatering av JSON-dokument med hjälp av [replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-)-metoden.

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Steg 9: Ta bort JSON-dokument
DocumentDB stöder borttagning av JSON-dokument med hjälp av [deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-)-metoden.  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Steg 10: Ta bort databasen
Om du tar bort databasen du skapade försvinner databasen och alla underordnade resurser (t.ex. samlingar och dokument).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Steg 11: Kör ditt Java-konsolprogram i sin helhet!
Om du vill köra programmet från konsolen, kompilerar du det först med Maven:
    
    mvn package

Om du kör `mvn package` så hämtas det senaste DocumentDB-biblioteket från Maven och `GetStarted-0.0.1-SNAPSHOT.jar` skapas. Kör sedan appen genom att köra:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Grattis! Du har slutfört den här självstudiekursen om NoSQL och har ett fungerande Java-konsolprogram!

## <a name="next-steps"></a>Nästa steg
* Vill du ha en självstudie om Java-webbappar? Se [Skapa en webbapp med Java via DocumentDB](documentdb-java-application.md).
* Mer information om hur du [övervakar ett DocumentDB-konto](documentdb-monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).
* Mer information om programmeringsmiljön finns i avsnittet Utveckla på [dokumentationssidan för DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

