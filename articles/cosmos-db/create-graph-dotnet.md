---
title: Skapa ett Azure Cosmos DB .NET Framework, Core-program med Gremlin-API:et
description: Anger ett .NET Framework/Core-kodexempel som du kan använda för att ansluta till och fråga en Azure Cosmos DB
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: lbosq
ms.openlocfilehash: bf453587b354b5db3f3ef1a80f974bcb8f8f4e14
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730020"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Snabbstart: Skapa ett .NET Framework- eller Core-program med Azure Cosmos DB Gremlin API-konto

> [!div class="op_single_selector"]
> * [Gremlin-konsol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabbstarten visar hur du skapar ett Azure Cosmos DB [Gremlin API-konto,](graph-introduction.md) databas och diagram (behållare) med Hjälp av Azure-portalen. Sedan skapar och kör du en konsolapp med drivrutinen [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) (öppen källkod).  

## <a name="prerequisites"></a>Krav

Om du inte redan har Installerat Visual Studio 2019 kan du ladda ned och använda den **kostnadsfria** [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Gremlin-API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Öppna sedan Visual Studio och öppna lösningsfilen.

5. Återställ NuGet-paketen i projektet. Det bör inkludera Gremlin.Net-drivrutinen och Newtonsoft.Json-paketet.


6. Du kan också installera Gremlin.Net-drivrutinen manuellt med Nuget-pakethanteraren eller [nuget-kommandoradsverktyget](https://docs.microsoft.com/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

Följande kodfragment är alla hämtade från filen Program.cs.

* Ange anslutningsparametrar baserat på kontot som skapats ovan: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* De Gremlin-kommandon som ska köras visas i en ordlista:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Skapa ett `GremlinServer` `GremlinClient` nytt objekt och anslutningsobjekt med hjälp av parametrarna ovan:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Kör varje Gremlin-fråga med `GremlinClient` objektet med en asynkron uppgift. Du kan läsa Gremlin-frågorna från ordlistan som definierats i föregående steg och köra dem. Senare få resultatet och läsa värdena, som är formaterade `JsonSerializer` som en ordbok, med hjälp av klassen från Newtonsoft.Json paket:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. Öppna [Azure Portal](https://portal.azure.com/) och navigera till ditt Graph-databaskonto. På fliken **Översikt** ser du två slutpunkter: 
 
   **.NET SDK URI** - Det här värdet används när du ansluter till grafkontot med hjälp av Microsoft.Azure.Graphs-biblioteket. 

   **Gremlin-slutpunkt** – Det här värdet används när du ansluter till Graph-kontot via biblioteket Gremlin.Net.

    ![Kopiera slutpunkten](./media/create-graph-dotnet/endpoint.png)

   Om du vill köra det här exemplet kopierar du **gremlin-slutpunktsvärdet,** tar bort portnumret i slutet, det vill än uri blir `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`. Slutpunktsvärdet ska se ut`testgraphacct.gremlin.cosmosdb.azure.com`

1. Gå sedan till fliken **Nycklar** och kopiera primary **key-värdet** från Azure-portalen. 

1. När du har kopierat URI och PRIMÄRNYCKEL för ditt konto sparar du dem i en ny miljövariabel på den lokala datorn som kör programmet. Om du vill ange miljövariabeln öppnar du ett kommandotolksfönster och kör följande kommando. Se till att ersätta <Your_Azure_Cosmos_account_URI> och <Your_Azure_Cosmos_account_PRIMARY_KEY> värden.

   ```console
   setx EndpointUrl "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Öppna *Program.cs* filen och uppdatera variablerna "databas och "behållare" med databas- och behållaren (som också är diagramnamnet) som skapats ovan.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Spara filen Program.cs. 

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Kör konsolappen

Tryck på Ctrl + F5 för att köra programmet. Programmet skriver ut både Gremlin-frågekommandona och resultatet i konsolen.

   Hörn och gränser som läggs till i tabellen visas i konsolfönstret. Stäng konsolfönstret genom att trycka på RETUR när skriptet har körts.

## <a name="browse-using-the-data-explorer"></a>Bläddra med datautforskaren

Nu kan du gå tillbaka till datautforskaren i Azure Portal och bläddra bland och ställa frågor mot dina nya grafdata.

1. Den nya databasen visas i fönstret Graphs (Diagram) i Datautforskaren. Expandera databasen och containernoderna och klicka sedan på **Diagram**.

2. Klicka på knappen **Använd filter** för att använda standardfrågan och visa alla hörn i diagrammet. Data som genereras av den här exempelappen visas i fönstret Graphs (Grafer).

    Du kan zooma in och ut i diagrammet, du kan expandera diagrammets skärmutrymme, lägga till ytterligare hörn och flytta hörn på skärmytan.

    ![Visa diagrammet i Datautforskaren på Azure-portalen](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en graf med datautforskaren och att köra en app. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)

