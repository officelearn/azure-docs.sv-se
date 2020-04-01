---
title: 'Självstudiekurs: Skapa en .NET-konsolapp för att hantera data i Azure Cosmos DB SQL API-konto'
description: 'Självstudiekurs: Lär dig hur du skapar Azure Cosmos DB SQL API-resurser med hjälp av ett C#-konsolprogram.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.openlocfilehash: 2681b2199f321f695bc621ed5580319a5e907b34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274017"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Självstudiekurs: Skapa en .NET-konsolapp för att hantera data i Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Välkommen till Azure Cosmos DB SQL API komma igång självstudiekurs. När du har genomfört den här självstudiekursen har du ett konsolprogram som skapar och skickar frågor till Azure Cosmos DB-resurser.

Den här självstudien använder version 3.0 eller senare av [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). Du kan arbeta med [.NET Framework eller .NET Core](https://dotnet.microsoft.com/download).

Den här självstudiekursen omfattar:

> [!div class="checklist"]
>
> * Skapa och ansluta till ett Azure Cosmos-konto
> * Konfigurera ditt projekt i Visual Studio
> * Skapa en databas och en container
> * Lägga till objekt i containern
> * Fråga containern
> * Utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på objektet
> * Ta bort databasen

Har du inte tid? Oroa dig inte! Den kompletta lösningen finns på [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). En snabbguide finns i avsnittet [Hämta den fullständiga lösningen till självstudien](#GetSolution).

Nu sätter vi igång!

## <a name="prerequisites"></a>Krav

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto

Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda hoppar du över det här avsnittet. Om du vill använda Azure Cosmos DB Emulator följer du stegen på [Azure Cosmos DB Emulator](local-emulator.md) för att konfigurera emulatorn. Hoppa sedan vidare till [steg 2: Konfigurera visual studio-projektet](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Steg 2: Konfigurera Visual Studio-projektet

1. Öppna Visual Studio och välj **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt**väljer du Console App **(.NET Framework)** för C#och väljer sedan **Nästa**.
1. Namnge projektet *CosmosGettingStartedTutorial*och välj sedan **Skapa**.

    ![Konfigurera projektet](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. Högerklicka på det nya konsolprogrammet, som finns under Visual Studio-lösningen i **Solution Explorer**och välj **Hantera NuGet-paket**.
1. I **NuGet Package Manager**väljer du **Bläddra** och sök efter *Microsoft.Azure.Cosmos*. Välj **Microsoft.Azure.Cosmos** och välj **Installera**.

   ![Installera NuGet för Azure Cosmos DB Client SDK](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

   Paket-ID:t för Azure Cosmos DB-klientbiblioteket är [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Bra! Konfigurationen är slutförd, så vi kan börja skriva kod. Det färdiga projektet för den här självstudien finns i [Utveckla en .NET-konsolapp med Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Steg 3: Ansluta till ett Azure Cosmos DB-konto

1. Ersätt referenserna i början av C#-programmet i *Program.cs-filen* med följande referenser:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Lägg till dessa konstanter `Program` och variabler i klassen.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Om du är bekant med den tidigare versionen av .NET SDK kan du känna till *termsamlingen* och *dokumentet*. Eftersom Azure Cosmos DB stöder flera API-modeller använder version 3.0 av .NET SDK behållaren *och* *objektet*för generiska termer . En *behållare* kan vara en samling, ett diagram eller en tabell. Ett *objekt* kan vara ett dokument, kant/hörn eller en rad och är innehållet i en behållare. Mer information finns i [Arbeta med databaser, behållare och objekt i Azure Cosmos DB](databases-containers-items.md).

1. Öppna [Azure-portalen](https://portal.azure.com). Hitta ditt Azure Cosmos DB-konto och välj sedan **Nycklar**.

   ![Hämta Azure Cosmos DB-nycklar från Azure Portal](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. Ersätt *med*värdet `<your endpoint URL>` **URI**i Program.cs . Ersätt `<your primary key>` med värdet **för PRIMÄRNYCKEL**.

1. Under **huvudmetoden** lägger du till en ny asynkron uppgift som heter **GetStartedDemoAsync**, som instansierar vår nya `CosmosClient`.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    Vi använder **GetStartedDemoAsync** som startpunkt som anropar metoder som fungerar på Azure Cosmos DB-resurser.

1. Lägg till följande kod för att köra den asynkrona uppgiften **GetStartedDemoAsync** via **Main**-metoden. Metoden **Main** fångar upp undantag och skriver dem till konsolen.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Välj F5 för att köra programmet.

    Konsolen visar meddelandet: **Slutet av demo, tryck på valfri tangent för att avsluta.** Det här meddelandet bekräftar att ditt program har gjort en anslutning till Azure Cosmos DB. Därefter kan du stänga konsolfönstret.

Grattis! Du har anslutit till ett Azure Cosmos DB-konto.

## <a name="step-4-create-a-database"></a>Steg 4: Skapa en databas

En databas är en logisk container med objekt som är partitionerade över containrar. Antingen `CreateDatabaseIfNotExistsAsync` kan `CreateDatabaseAsync` metoden eller metoden för klassen [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) skapa en databas.

1. Kopiera och `CreateDatabaseAsync` klistra in `GetStartedDemoAsync` metoden under metoden.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync`skapar en ny databas `FamilyDatabase` med ID om det inte redan finns, som `databaseId` har det ID som angetts från fältet.

1. Kopiera och klistra in koden nedan där du instansierar CosmosClient för att anropa **metoden CreateDatabaseAsync** som du just har lagt till.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Din *Program.cs* ska nu se ut så här, med slutpunkten och primärnyckeln ifylld.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Välj F5 för att köra programmet.

   > [!NOTE]
   > Om du får felmeddelandet "503-tjänsten är inte tillgänglig" är det möjligt att de [portar](performance-tips.md#networking) som krävs för direktanslutningsläge blockeras av en brandvägg. Lös problemet genom att antingen öppna de portar som krävs eller använda gateway-lägesanslutningen enligt följande kod:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Grattis! Du har skapat en Azure Cosmos-databas.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Steg 5: Skapa en container

> [!WARNING]
> Metoden `CreateContainerIfNotExistsAsync` skapar en ny behållare, som har prissättning konsekvenser. För mer information, besök vår [prissida.](https://azure.microsoft.com/pricing/details/cosmos-db/)
>
>

En behållare kan skapas med hjälp av metoden [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) eller `CosmosDatabase` [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) i klassen. En behållare består av objekt (JSON-dokument om SQL API) och tillhörande programlogik på serversidan i JavaScript, till exempel lagrade procedurer, användardefinierade funktioner och utlösare.

1. Kopiera och `CreateContainerAsync` klistra in `CreateDatabaseAsync` metoden under metoden. `CreateContainerAsync`skapar en ny behållare med `FamilyContainer` ID:t om det inte redan finns, `containerId` med hjälp `LastName` av det ID som angetts från fältet som partitioneras efter egenskap.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Kopiera och klistra in koden nedan där du instansierade CosmosClient för att anropa metoden **CreateContainer** som du precis lade till.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har skapat en Azure Cosmos-behållare.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Steg 6: Lägga till objekt i behållaren

[**Metoden CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) för `CosmosContainer` klassen kan skapa ett objekt. När SQL API-API:et med hjälp av SQL API visas som dokument, som är användardefinierat godtyckligt JSON-innehåll. Du kan nu infoga ett objekt i din Azure Cosmos-behållare.

Låt oss först skapa `Family` en klass som representerar objekt som lagras i Azure Cosmos DB i det här exemplet. Vi ska också `Parent` `Child`skapa `Pet` `Address` underklasser som används `Family`i . Artikeln måste ha `Id` en egenskap `id` serialiserad som i JSON.

1. Välj Ctrl+Skift+A om du vill öppna **Lägg till nytt objekt**. Lägg till `Family.cs` en ny klass i projektet.

    ![Skärmbild av hur du lägger till en ny Family.cs-klass i projektet](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. Kopiera och `Family`klistra `Parent` `Child`in `Pet`klassen `Address` , `Family.cs`, , och till .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Tillbaka i *Program.cs*lägger `AddItemsToContainerAsync` du till `CreateContainerAsync` metoden efter metoden.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    Koden kontrollerar att en artikel med samma ID inte redan finns. Vi sätter in två objekt, en vardera för *Familjen Andersen* och *Wakefield Family.*

1. Lägg till ett anrop till `AddItemsToContainerAsync` i metoden `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har skapat två Azure Cosmos-objekt.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Steg 7: Skicka frågor mot Azure Cosmos DB-resurser

Azure Cosmos DB stöder komplexa frågor mot JSON-dokument som lagras i varje container. Mer information finns i [Komma igång med SQL-frågor](sql-api-sql-query.md). I följande exempelkod visas hur du kör en fråga mot de objekt som vi infogade i det föregående steget.

1. Kopiera och `QueryItemsAsync` klistra in `AddItemsToContainerAsync` metoden efter metoden.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Lägg till ett anrop till ``QueryItemsAsync`` i metoden ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har frågat en Azure Cosmos-behållare.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Steg 8: Ersätta ett JSON-objekt

Nu uppdaterar vi ett objekt i Azure Cosmos DB. Vi ändrar `IsRegistered` egendomen `Family` hos `Grade` ett av barnen.

1. Kopiera och `ReplaceFamilyItemAsync` klistra in `QueryItemsAsync` metoden efter metoden.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Lägg till ett anrop till `ReplaceFamilyItemAsync` i metoden `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har ersatt ett Azure Cosmos-objekt.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Steg 9: Ta bort objekt

Nu tar vi bort ett objekt i Azure Cosmos DB.

1. Kopiera och `DeleteFamilyItemAsync` klistra in `ReplaceFamilyItemAsync` metoden efter metoden.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Lägg till ett anrop till `DeleteFamilyItemAsync` i metoden `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Välj F5 för att köra programmet.

Grattis! Du har tagit bort ett Azure Cosmos-objekt.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Steg 10: Ta bort databasen

Nu tar vi bort vår databas. Om du tar bort den skapade databasen tas databasen och alla underordnade resurser bort. Resurserna omfattar behållare, artiklar och alla lagrade procedurer, användardefinierade funktioner och utlösare. Vi gör oss `CosmosClient` också av med fallet.

1. Kopiera och `DeleteDatabaseAndCleanupAsync` klistra in `DeleteFamilyItemAsync` metoden efter metoden.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Lägg till ett anrop till ``DeleteDatabaseAndCleanupAsync`` i metoden ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Välj F5 för att köra programmet.

Grattis! Du har tagit bort en Azure Cosmos-databas.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Steg 11: Kör C#-konsolappen i sin helhet!

Välj F5 i Visual Studio för att bygga och köra appen i felsökningsläge.

Du bör nu se utdata från hela appen i ett konsolfönster. Utdata visar resultatet av de frågor vi lagt till. Den ska matcha exempeltexten nedan.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Grattis! Du har slutfört självstudiekursen och har ett fungerande C#-konsolprogram!

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a>Hämta den fullständiga lösningen för självstudiekursen

Om du inte hade tid att slutföra stegen i den här självstudien, eller bara vill ladda ner kodexemplen, kan du ladda ner den.

För att `GetStarted` kunna skapa lösningen behöver du följande förutsättningar:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
* Ett [Azure Cosmos DB-konto][cosmos-db-create-account].
* [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)-lösningen som finns på GitHub.

Om du vill återställa referenserna till Azure Cosmos DB .NET SDK i Visual Studio högerklickar du på lösningen i **Solution Explorer**och väljer sedan **Återställ NuGet-paket**. Uppdatera sedan värdena `EndPointUri` och `PrimaryKey` värdena enligt beskrivningen i steg [3](#Connect)i filen *App.config.*

Då är det bara att bygga den, så är du på väg!

## <a name="next-steps"></a>Nästa steg

* Behöver du en mer komplex ASP.NET MVC-självstudiekurs? Se [självstudiekurs: Utveckla ett ASP.NET Core MVC-webbprogram med Azure Cosmos DB med hjälp av .NET SDK](sql-api-dotnet-application.md).
* Vill du göra skalnings- och prestandatester med Azure Cosmos DB? Se [Prestanda- och skalningstestning med Azure Cosmos DB](performance-testing.md).
* Mer information om hur du övervakar Azure Cosmos DB-begäranden, användning och lagring finns [i Övervaka prestanda- och lagringsmått i Azure Cosmos DB](monitor-accounts.md).
* Information om hur du kör frågor mot vår exempeldatauppsättning finns i [Frågelekplatsen](https://www.documentdb.com/sql/demo).
* Läs mer om Azure Cosmos DB i [Välkommen till Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
