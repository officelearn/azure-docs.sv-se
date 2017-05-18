---
title: Skapa en Java-app i Azure Cosmos DB med Graph API | Microsoft Docs
description: "Presenterar ett Java-kodexempel som du kan använda för att ansluta till och ställa frågor mot Azure Cosmos DB via Gremlin."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8279ffc8dc69f0899ad7b5d3a528393fc2165b77
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB: Skapa en Java-app med Graph API

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

I den här snabbstarten visas hur du skapar ett Azure Cosmos DB-konto för Graph API (förhandsversion), en databas och en graf med hjälp av Azure Portal. Sedan skapar och kör du en konsolapp med OSS-drivrutinen [Gremlin Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java).  

## <a name="prerequisites"></a>Krav

* Innan du kan köra det här exemplet måste du uppfylla följande krav:
   * JDK 1.7+ (Kör `apt-get install default-jdk` om du inte har JDK)
   * Maven (Kör `apt-get install maven` om du inte har Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Graph API-app (förhandsversion) från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, till exempel git bash, och `cd` till en arbetskatalog.  

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Vi gör en snabb genomgång av vad som händer i appen. Om du öppnar filen `Program.java` ser du följande rader med kod. 

* Gremlin-`Client`en initieras från den konfiguration du ställde in tidigare i `src/remote-secure.yaml`.

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* En mängd Gremlin-steg utförs med metoden `client.submit`.

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. Öppna ditt Azure Cosmos DB-konto i [Azure Portal](http://portal.azure.com/), klicka på **Nycklar** och därefter på **Läs- och skrivnycklar**. Använd kopieringsknapparna till höger på skärmen och kopiera URI:n och primärnyckeln till filen `Program.java` i nästa steg.

    ![Visa och kopiera en åtkomstnyckel i Azure Portal, bladet Nycklar](./media/create-documentdb-dotnet/keys.png)

2. Öppna filen `src/remote-secure.yaml`. 

3. Fyll i konfigurationen för *värd*, *port*, *användarnamn*, *lösenord*, *anslutningspool* och *serialiserare* i filen `src/remote-secure.yaml`:

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Värdar|***.graphs.azure.com|Din URI till Graph-tjänsten som du kan hämta från Azure Portal
    Port|443|Ställ in på 443
    Användarnamn|*Ditt användarnamn*|Resursen i formuläret `/dbs/<db>/colls/<coll>`.
    Lösenord|*Din primära huvudnyckel*|Din primära huvudnyckel för Azure Cosmos DB
    ConnectionPool|{enableSsl: true}|Din anslutningspoolinställning för SSL
    Serialiserare|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Ange detta värde

## <a name="run-the-console-app"></a>Kör konsolappen

1. Kör `mvn package` i en terminal för att installera de npm-moduler som krävs

2. Kör `mvn exec:java -D exec.mainClass=GetStarted.Program` i en terminal för att starta Java-programmet.

Du kan nu gå tillbaka till datautforskaren och ställa frågor, ändra och arbeta med dessa nya data. 

## <a name="browse-using-the-data-explorer"></a>Bläddra med datautforskaren

Nu kan du gå tillbaka till datautforskaren i Azure Portal och bläddra bland och ställa frågor mot dina nya grafdata.

* Den nya databasen visas i panelen Samlingar i datautforskaren. Expandera **graphdb**, **graphcoll** och klicka på **Graph**.

    Data som genereras av den här exempelappen visas i fönstret Graphs (Grafer).

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg: 

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. Klicka på **Ta bort** på sidan med resursgrupper, ange namnet på resursen du vill ta bort i textrutan och klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en graf med datautforskaren och att köra en app. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)


