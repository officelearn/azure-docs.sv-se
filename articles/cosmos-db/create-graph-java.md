---
title: Skapa en Azure Cosmos DB-grafdatabas med Java | Microsoft Docs
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
ms.date: 08/07/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: afa4fe6cdef298e4504ddcf3e344ee6a5c181653
ms.contentlocale: sv-se
ms.lasthandoff: 08/08/2017

---
# <a name="azure-cosmos-db-create-a-graph-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Skapa en grafdatabas med Java och Azure Portal

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

I den här snabbstarten skapar vi en grafdatabas med hjälp av Azure Portal-verktyg för Azure Cosmos DB. Här visas också hur du snabbt skapar en Java-konsolapp med hjälp av en grafdatabas med OSS-drivrutinen [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver). Anvisningarna i den här snabbstartsguiden gäller alla operativsystem som kan köra Java. När du har slutfört den här snabbstarten kommer du kunna skapa och ändra grafresurser i antingen användargränssnittet eller programmässigt, beroende på vad du föredrar. 

## <a name="prerequisites"></a>Krav

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett [Maven](http://maven.apache.org/)-binärarkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en grafdatabas måste du skapa ett Gremlin-databaskonto (Graph) med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en grafdatabas. 

1. Klicka på **Data Explorer (förhandsversion)** på den vänstra navigeringsmenyn i Azure Portal. 
2. På bladet **Datautforskaren (förhandsversion)** klickar du på **New Graph** (Ny graf) och fyller sedan i sidan med följande information.

    ![Datautforskaren i Azure-portalen](./media/create-graph-java/azure-cosmosdb-data-explorer.png)

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|sample-database|ID för din nya databas. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla `/ \ # ?` eller avslutande blanksteg.
    Graf-id|sample-graph|ID för din nya graf. Samma teckenkrav gäller för grafnamn som databas-id.
    Lagringskapacitet| 10 GB|Låt standardvärdet vara kvar. Det här är databasens lagringskapacitet.
    Dataflöde|400 RU:er|Låt standardvärdet vara kvar. Du kan skala upp dataflödet senare om du vill minska svarstiden.
    RU/m|Av|Låt standardvärdet vara kvar. Om du behöver hantera toppar i arbetsbelastningar senare kan du aktivera funktionen [RU/m](request-units-per-minute.md) då.
    Partitionsnyckeln|Lämna tomt|Lämna partitionsnyckelfältet tomt i den här snabbstarten.

3. När formuläret är ifyllt klickar du på **OK**.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en grafapp från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, till exempel git bash, och `cd` till en arbetskatalog.  

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Vi gör en snabb genomgång av vad som händer i appen. Öppna filen`Program.java` från mappen \src\GetStarted folder och leta upp följande kodrader. 

* Gremlin-`Client`en initieras från konfigurationen i `src/remote.yaml`.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

* En mängd Gremlin-steg utförs med metoden `client.submit`.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

1. Öppna filen src/remote.yaml. 

3. Fyll i värden för *värdar*, *användarnamn* och *lösenord* i filen src/remote.yaml. Resterande inställningar behöver inte ändras.

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Värdar|[***.graphs.azure.com]|Se skärmbilden under tabellen. Det här är värdet för Gremlin-URI på översiktssidan för Azure Portal, inom hakparenteser och utan : 443/ på slutet.<br><br>Det här värdet kan också hämtas från fliken Nycklar med hjälp av URI-värdet utan https://, eller genom att ändra dokument till grafer och ta bort avslutande: 443/.
    Användarnamn|/dbs/sample-database/colls/sample-graph|Resursen i formuläret `/dbs/<db>/colls/<coll>` där `<db>` är det befintliga databasnamnet och `<coll>` är den befintliga samlingens namn.
    Lösenord|*Din primära huvudnyckel*|Se den andra skärmbilden under tabellen. Det här värdet är din primärnyckel som du kan hämta från sidan Nycklar i Azure Portal i rutan Primärnyckel. Kopiera värdet med kopieringsknappen till höger om rutan.

    För värdet Värdar kopierar du värdet **Gremlin-URI** från **översiktssidan**. Om det är tomt läser du instruktionerna på raden Värdar i föregående tabell om hur du skapar Gremlin-URI från bladet Nycklar.
![Visa och kopiera värdet för Gremlin-URI på översiktssidan i Azure Portal](./media/create-graph-java/gremlin-uri.png)

    För lösenordsvärdet, kopierar du **Primärnyckel** från sidan **Nycklar**: ![Visa och kopiera primärnyckel i Azure Portal, sidan Nycklar](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Kör konsolappen

1. I git-terminalfönstret `cd` till mappen azure-cosmos-db-graph-java-getting-started.

2. I git-terminalfönstret skriver du `mvn package` för att installera de Java-paket som krävs.

3. I git-terminalfönstret kör du `mvn exec:java -D exec.mainClass=GetStarted.Program` i terminalfönstret för att starta ditt Java-program.

I terminalfönstret visas de hörn som läggs till i tabellen. När programmet har slutförts kan du växla tillbaka till Azure Portal i webbläsaren. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Granska och lägg till exempeldata

Nu kan du gå tillbaka till datautforskaren och se de hörn som lagts till i grafen, och lägga till ytterligare datapunkter.

1. I datautforskaren expanderar du **exempeldatabasen**/**exempelgrafen**, klickar på **Graf** och sedan på **Använd filter**. 

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. I listan **Resultat** kan du se nya användare som har lagts till i grafen. Välj **Ben** och lägg märke till att han är kopplad till Robin. Du kan flytta runt hörnen i grafutforskaren, zooma in och ut och öka storleken på grafutforskaren. 

   ![Nya hörn i grafen i datautforskaren på Azure Portal](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Nu ska vi lägga till några nya användare i grafen med hjälp av datautforskaren. Klicka på **Nytt hörn** om du vill lägga till data i grafen.

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. Ange en etikett för *person* och ange sedan följande nycklar och värden för att skapa det första hörnet i grafen. Tänk på att du kan skapa unika egenskaper för varje person i grafen. Endast id-nyckeln krävs.

    key|värde|Anteckningar
    ----|----|----
    id|ashley|Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    kön|kvinna| 
    teknik | Java | 

    > [!NOTE]
    > I den här snabbstartsguiden skapar vi en icke-partitionerad samling. Men om du skapar en partitionerad samling genom att ange en partitionsnyckel när samlingen skapas, måste du lägga till partitionsnyckeln som nyckel i varje nytt hörn. 

5. Klicka på **OK**. Du kan behöva expandera skärmen för att se **OK** längst ned på skärmen.

6. Klicka på **Nytt hörn** igen och lägg till ytterligare en ny användare. Ange en etikett för *person* och ange sedan följande nycklar och värden:

    key|värde|Anteckningar
    ----|----|----
    id|rakesh|Den unika identifieraren för hörnet. Om du inte anger något id skapas ett automatiskt.
    kön|man| 
    skola|MIT| 

7. Klicka på **OK**. 

8. Klicka på **Använd filter** med standardfiltret `g.V()`. Nu visas alla användare i listan **Resultat**. Allteftersom du lägger till data kan du använda filter för att begränsa resultaten. I datautforskaren används som standard `g.V()` för att hämta alla hörn i en graf, men du kan ändra till en annan [graffråga](tutorial-query-graph.md), till exempel `g.V().count()`, för att returnera det totala antalet hörn i grafen i JSON-format.

9. Nu kan vi koppla ihop Rakesh och Ashley. Se till att **ashley** är markerat i listan**Resultat** och klicka sedan på redigeringsknappen bredvid **Mål** nere till höger. Du kan behöva bredda fönstret för att se området **Egenskaper**.

   ![Ändra mål för ett hörn i en graf](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

10. I rutan **Mål** skriver du *rakesh* och i rutan **Edge label**  (Kantetikett) skriver du *känner* och markerar sedan kryssrutan.

   ![Lägg till en anslutning mellan Ashley och Rakesh i datautforskaren](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

11. Markera nu **rakesh** i resultatlistan och se att Ashley och Rakesh är anslutna. 

   ![Två hörn anslutna i datautforskaren](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

    Du kan även använda datautforskaren för att skapa lagrade procedurer, UDF:er och utlösare för att utföra affärslogik på serversidan såväl som att skala genomflödet. Datautforskaren visar all den inbyggda programmässiga dataåtkomsten som finns tillgänglig i API:erna, men ger enkel åtkomst till dina data i Azure-portalen.



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


