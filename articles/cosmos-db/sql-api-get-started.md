---
title: Skapa en .NET-konsolapp för att hantera data i Azure Cosmos DB SQL API-konto
description: Lär dig hur du skapar Azure Cosmos DB SQL API-resurser med hjälp av en C# -konsolapp.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: kirankk
ms.openlocfilehash: 6fd7efe38aeb1f1094d240cf1675d432f3766229
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985726"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Skapa en .NET-konsolapp för att hantera data i Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Välkommen till att Azure Cosmos DB SQL API-komma igång-Självstudier. När du har genomfört den här självstudiekursen har du ett konsolprogram som skapar och skickar frågor till Azure Cosmos DB-resurser. Den här självstudien används [Version 3.0 +](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) av Azure Cosmos DB .NET SDK, som kan riktas mot [.NET Framework](https://dotnet.microsoft.com/download) eller [.NET Core](https://dotnet.microsoft.com/download).

Den här självstudiekursen omfattar:

> [!div class="checklist"]
> * Skapa och ansluta till ett Azure Cosmos-konto
> * Konfigurera ditt projekt i Visual Studio
> * Skapa en databas och en container
> * Lägga till objekt i containern
> * Fråga containern
> * CRUD-åtgärder på objektet
> * Ta bort databasen

Har du inte tid? Oroa dig inte! Den kompletta lösningen finns på [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). En snabbguide finns i avsnittet [Hämta den fullständiga lösningen till självstudien](#GetSolution).

Nu sätter vi igång!

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto
Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera en lösning i Visual Studio](#SetupVS). Om du använder Azure Cosmos DB-emulatorn följer du stegen i artikeln om [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och går vidare till [konfigurationen av Visual Studio-projektet](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Steg 2: Konfigurera ett Visual Studio-projekt
1. Öppna **Visual Studio 2017** i datorn.
1. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
1. I dialogrutan **Nytt projekt** väljer du **Visual C#**  / **Console App (.NET Framework)** , namnger projektet och klickar på **OK**.
    ![Skärmbild av fönstret nytt projekt](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > För .NET core-mål, i den **nytt projekt** dialogrutan **Visual C#**   /  **Konsolapp (.NET Core)** , namnge projektet och klicka sedan på  **Okej**

1. I **Solution Explorer** högerklickar du på den nya konsolappen, som finns under din Visual Studio-lösning, och klickar sedan på **Hantera NuGet-paket ...**

    ![Skärmdump som visar den högerklickade menyn för projektet](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. På fliken **NuGet** klickar du på **Bläddra** och skriver **Microsoft.Azure.Cosmos** i sökrutan.
1. Leta upp **Microsoft.Azure.Cosmos** i resultatet och klicka på **Installera**.
   Paket-ID:t för Azure Cosmos DB-klientbiblioteket är [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Skärmbild av NuGet-menyn där du hittar Azure Cosmos DB Client SDK](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Om du får ett meddelande om att granska ändringar i lösningen klickar du på **OK**. Om du får ett meddelande om godkännande av licens klickar du på **Jag godkänner**.

Bra! Konfigurationen är slutförd, så vi kan börja skriva kod. Det finns ett färdigt kodprojekt för den här självstudiekursen i [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Steg 3: Ansluta till ett Azure Cosmos DB-konto
1. Ersätt först referenserna i början av C#-programmet, i filen **Program.cs** med dessa referenser:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Lägg nu till dessa konstanter och variabler i den offentliga klassen ``Program``.

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

    Observera att om du är bekant med den tidigare versionen av .NET SDK så är du kanske van vid att se termerna ”samling” och ”dokument”. Eftersom Azure Cosmos DB stöder flera API-modeller använder version 3.0+ av .NET SDK de allmänna termerna ”container” och ”objekt”. En container kan vara en samling, ett diagram eller en tabell. Ett objekt kan vara ett dokument, en kant/ett hörn eller en rad, och är innehållet i en container. [Läs mer om databaser, containrar och objekt.](databases-containers-items.md)

1. Hämta din slutpunkts-URL och primärnyckel från [Azure-portalen](https://portal.azure.com).

    Gå till ditt Azure Cosmos DB-konto på Azure Portal och klicka på **Nycklar**.

    Kopiera URI från portalen och klistra in den i `<your endpoint URL>` i filen ```Program.cs```. Kopiera PRIMÄRNYCKELN från portalen och klistra in den i `<your primary key>`.

   ![Skärmbild för att hämta nycklar för Azure Cosmos DB från Azure-portalen](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Nu skapar vi en ny instans av ```CosmosClient``` och konfigurera ramar för programmet.

    Under metoden **Main** lägger du till en ny asynkron uppgift med namnet **GetStartedDemoAsync**, som instansierar vår nya ```CosmosClient```. Vi använder **GetStartedDemoAsync** som den startpunkt som anropar metoder som behandlar Azure Cosmos DB-resurser.

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

1. Lägg till följande kod för att köra den asynkrona uppgiften **GetStartedDemoAsync** via **Main**-metoden. Metoden **Main** ska fånga upp undantag och skriva dem till konsolen.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Kör appen genom att välja **F5**. Konsolfönstrets utdata visar meddelandet `End of demo, press any key to exit.` som bekräftar att anslutningen till Azure Cosmos DB har gjorts. Därefter kan du stänga konsolfönstret.

Grattis! Du har anslutit till ett Azure Cosmos DB-konto. 

## <a name="step-4-create-a-database"></a>Steg 4: Skapa en databas
En databas kan skapas med hjälp av antingen funktionen [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) eller [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) för klassen ``CosmosClient``. En databas är en logisk container med objekt som är partitionerade över containrar.

1. Kopiera och klistra in den **CreateDatabaseAsync** metod nedan din **GetStartedDemoAsync** metod. **CreateDatabaseAsync** skapas en ny databas med ID ``FamilyDatabase`` om den inte redan finns, med det ID som angetts från den ``databaseId`` fält. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Kopiera och klistra in koden nedan där du instansierats CosmosClient att anropa den **CreateDatabaseAsync** metod som du just lade till.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Nu bör koden se ut så här, med slutpunkten och primärnyckeln ifyllda.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
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

Kör appen genom att välja **F5**.

Grattis! Du har skapat en Azure Cosmos DB-databas.  

## <a id="CreateColl"></a>Steg 5: Skapa en container
> [!WARNING]
> När du anropar metoden **CreateContainerIfNotExistsAsync** skapas en ny container, vilket får konsekvenser för priset. Mer information finns på vår [sida med priser](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

En behållare kan skapas genom att använda antingen den [ **CreateContainerIfNotExistsAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) eller [ **CreateContainerAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) fungera i den **CosmosDatabase** klass. En behållare som består av objekt (JSON-dokument om SQL-API) och tillhörande programlogiken på serversidan i JavaScript, till exempel lagrade procedurer, användardefinierade funktioner och utlösare.

1. Kopiera och klistra in den **CreateContainerAsync** metod nedan din **CreateDatabaseAsync** metod. **CreateContainerAsync** skapar en ny behållare med ID ``FamilyContainer`` om den inte redan finns, med det ID som angetts från den ``containerId`` fältet partitioneras efter ``LastName`` egenskapen.

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

   Kör appen genom att välja **F5**.

Grattis! Du har skapat en Azure Cosmos DB-container.  

## <a id="CreateDoc"></a>Steg 6: Lägga till objekt i containern
Ett objekt kan skapas med hjälp av den [ **CreateItemAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) funktion av den **CosmosContainer** klass. När du använder SQL-API:et projiceras objekt som dokument, som är användardefinierat (godtyckligt), JSON-innehåll. Nu kan du infoga ett objekt i Azure Cosmos DB-containern.

Först ska vi skapa en **familj** klass som ska representera objekt som lagras i Azure Cosmos DB i det här exemplet. Vi kommer även att skapa underklasserna **Förälder**, **Barn**, **Husdjur** och **Adress** som används inom **Familj**. Obs objekt måste ha en **Id** egenskap serialiserad som **id** i JSON.

1. Välj **Ctrl+Skift+A** för att öppna dialogrutan **Lägg till nytt objekt**. Lägg till en ny klass, **Family.cs**, i projektet.

    ![Skärmbild av att lägga till en ny Family.cs klass i projektet](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Kopiera och klistra in klasserna **Family** (Familj), **Parent** (Förälder), **Child** (Barn), **Pet** (Husdjur) och **Address** (Adress) i **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Gå tillbaka till **Program.cs** och lägga till den **AddItemsToContainerAsync** metod under din **CreateContainerAsync** metod.
Kod som kontrollerar om ett objekt med samma ID får inte finnas innan du skapar den. Vi Infogar även två objekt, ett för familjen Andersen och ett för familjen Wakefield.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Lägg till ett anrop till ``AddItemsToContainerAsync`` i metoden ``GetStartedDemoAsync``.

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

Kör appen genom att välja **F5**.

Grattis! Du har skapat två Azure Cosmos DB-objekt.  

## <a id="Query"></a>Steg 7: Köra frågor mot Azure Cosmos DB-resurser
Azure Cosmos DB stöder [komplexa frågor](sql-api-sql-query.md) mot JSON-dokument som lagras i varje samling. I följande exempelkod visas hur du kör en fråga mot de objekt som vi infogade i det föregående steget.

1. Kopiera och klistra in den **QueryItemsAsync** metod nedan din **AddItemsToContainerAsync** metod.

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

Kör appen genom att välja **F5**.

Grattis! Du har skickat frågor mot en Azure Cosmos DB-container.

## <a id="ReplaceItem"></a>Steg 8: Ersätta ett JSON-objekt
Vi uppdaterar nu ett objekt i Azure Cosmos DB.

1. Kopiera och klistra in den **ReplaceFamilyItemAsync** metod nedan din **QueryItemsAsync** metod. Observera att vi ändrar egenskapen ``IsRegistered`` för familjen och ``Grade`` för ett av de underordnade objekten.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Lägg till ett anrop till ``ReplaceFamilyItemAsync`` i metoden ``GetStartedDemoAsync``.

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

   Kör appen genom att välja **F5**.

Grattis! Du har ersatt ett Azure Cosmos DB-objekt.

## <a id="DeleteDocument"></a>Steg 9: Ta bort objekt
Vi tar nu bort ett objekt i Azure Cosmos DB.

1. Kopiera och klistra in den **DeleteFamilyItemAsync** metod nedan din **ReplaceFamilyItemAsync** metod.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Lägg till ett anrop till ``DeleteFamilyItemAsync`` i metoden ``GetStartedDemoAsync``.

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

Kör appen genom att välja **F5**.

Grattis! Du har tagit bort ett Azure Cosmos DB-objekt.

## <a id="DeleteDatabase"></a>Steg 10: Ta bort databasen
Nu tar vi bort databasen. Om du tar bort den skapade databasen tas databasen och alla underordnade resurser bort (containrar, objekt samt lagrade procedurer, användardefinierade funktioner och utlösare). Vi tar även bort **CosmosClient**-instansen.

1. Kopiera och klistra in den **DeleteDatabaseAndCleanupAsync** metod nedan din **DeleteFamilyItemAsync** metod.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Lägg till ett anrop till ``DeleteDatabaseAndCleanupAsync`` i metoden ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Kör appen genom att välja **F5**.

Grattis! Du har tagit bort en Azure Cosmos DB-databas.

## <a id="Run"></a>Steg 11: Köra C#-konsolappen i dess helhet!
Välj F5 i Visual Studio för att bygga och köra appen i felsökningsläge.

Du bör nu se utdata från hela appen i ett konsolfönster. Dessa utdata visar resultaten för de frågor som vi har lagt till och bör motsvara exempeltexten nedan.

```
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

## <a id="GetSolution"></a>Hämta den fullständiga lösningen för självstudiekursen
Om du inte har tid att slutföra stegen i den här självstudien eller bara vill ladda ned kodexemplen kan du hämta den från [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). 

För att bygga GetStarted-lösningen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En [Azure Cosmos DB-konto][cosmos-db-create-account].
* [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)-lösningen som finns på GitHub.

Om du vill återställa referenser till Azure Cosmos DB .NET SDK i Visual Studio högerklickar du på **GetStarted**-lösningen i Solution Explorer och klickar sedan på **Återställ NuGet-paket**. I filen App.config uppdaterar du sedan värdena EndPointUri och PrimaryKey enligt anvisningarna i [Ansluta till ett Azure Cosmos DB-konto](#Connect).

Då är det bara att bygga den, så är du på väg!

## <a name="next-steps"></a>Nästa steg
* Behöver du en mer komplex ASP.NET MVC-självstudiekurs? Se [Självstudie om ASP.NET MVC: Utveckling av webbprogram med Azure Cosmos DB](sql-api-dotnet-application-preview.md).
* Vill du testa skalning och prestanda med Azure Cosmos DB? Mer information finns i avsnittet om hur du [testar prestanda och skalning med Azure Cosmos DB](performance-testing.md)
* Lär dig hur du [övervakar förfrågningar, användning och lagring för Azure Cosmos DB](monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).
* Läs mer om Azure Cosmos DB i [Välkommen till Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
