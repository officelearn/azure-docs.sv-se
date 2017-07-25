---
title: Skapa en Java-app i Azure Cosmos DB med Graph API | Microsoft Docs
description: "Presenterar ett Java-kodexempel som du kan använda för att ansluta till och ställa frågor mot Azure Cosmos DB via Gremlin."
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: sv-se
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB: Skapa en Java-app med Graph API

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

I den här snabbstarten visas hur du skapar ett Azure Cosmos DB-konto för Graph API (förhandsversion), en databas och en graf med hjälp av Azure Portal. Sedan skapar och kör du en konsolapp med OSS-drivrutinen [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver).  

## <a name="prerequisites"></a>Krav

* Innan du kan köra det här exemplet måste du uppfylla följande krav:
   * JDK 1.7 + (kör `apt-get install default-jdk` om du inte har JDK) och ange miljövariabler som `JAVA_HOME`
   * Maven (Kör `apt-get install maven` om du inte har Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Graph API-app (förhandsversion) från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, till exempel git bash, och `cd` till en arbetskatalog.  

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Vi gör en snabb genomgång av vad som händer i appen. Om du öppnar filen `Program.java` ser du följande rader med kod. 

* Gremlin-`Client`en initieras från konfigurationen i `src/remote.yaml`.

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

1. Öppna filen src/remote.yaml. 

3. Fyll i konfigurationen för *värd*, *port*, *användarnamn*, *lösenord*, *anslutningspool* och *serialiserare* i filen src/remote.yaml:

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Värdar|[***.graphs.azure.com]|Se skärmbilden nedan. Detta är det Gremlin URI-värdet på översiktssidan för Azure-portalen inom hakparenteser utan : 443/ på slutet.<br><br>Det här värdet kan också hämtas från fliken Nycklar med hjälp av URI-värdet utan https://, eller genom att ändra dokument till grafer och ta bort avslutande: 443/.
    Port|443|Ställ in på 443.
    Användarnamn|*Ditt användarnamn*|Resursen i formuläret `/dbs/<db>/colls/<coll>` där `<db>` är databasnamnet och `<coll>` är samlingens namn.
    Lösenord|*Din primära huvudnyckel*|Se andra skärmbilden nedan. Det här är din primärnyckel som du kan hämta från sidan Nycklar i Azure-portalen i rutan Primärnyckel. Använd kopieringsknappen till vänster om rutan för att kopiera värdet.
    ConnectionPool|{enableSsl: true}|Din anslutningspoolinställning för SSL.
    Serialiserare|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Ange detta värde och ta bort alla `\n`-radbrytningar när du klistrar in värdet.

    För värdar, kopiera **Gremlin URI**-värdet från **Översiktssidan**: ![Visa och kopiera Gremlin URI-värdet på översiktssidan i Azure-portalen](./media/create-graph-java/gremlin-uri.png)

    För lösenord, kopiera **Primärnyckeln** från sidan **Nycklar**: ![visa och kopiera primärnyckel i Azure portal, sidan Nycklar](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Kör konsolappen

1. Kör `mvn package` i en terminal för att installera de Java-paket som krävs.

2. Kör `mvn exec:java -D exec.mainClass=GetStarted.Program` i en terminal för att starta Java-programmet.

Du kan nu gå tillbaka till datautforskaren och ställa frågor, ändra och arbeta med dessa nya data. 

## <a name="browse-using-the-data-explorer"></a>Bläddra med datautforskaren

Nu kan du gå tillbaka till datautforskaren i Azure Portal och bläddra bland och ställa frågor mot dina nya grafdata.

* Den nya databasen visas på panelen Samlingar i datautforskaren. Expandera **graphdb**, **graphcoll** och klicka på **Graph**.

    Data som genereras av den här exempelappen visas i fönstret Graphs (Grafer).

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg: 

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en graf med datautforskaren och att köra en app. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)


