---
title: Skapa en .NET Core-konsolapp för att hantera data på Azure Cosmos DB SQL API-konto (SDK version 3 – förhandsversion)
description: En självstudiekurs som beskriver hur du skapar en onlinedatabas och ett C#-konsolprogram med hjälp av .NET Core SDK för Azure Cosmos DB SQL API:et.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 828d0ced775d46b0e966969acb3220d2a678c57b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440054"
---
# <a name="build-a-net-core-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account-sdk-version-3-preview"></a>Skapa en .NET Core-konsolapp för att hantera data på Azure Cosmos DB SQL API-konto (SDK version 3 – förhandsversion)

> [!div class="op_single_selector"]
>
> - [.NET Core (förhandsversion)](sql-api-dotnet-core-get-started-preview.md)
> - [.NET Core](sql-api-dotnetcore-get-started.md)
> - [.NET (förhandsversion)](sql-api-dotnet-get-started-preview.md)
> - [NET](sql-api-get-started.md)
> - [Java](sql-api-java-get-started.md)
> - [Async Java](sql-api-async-java-get-started.md)
> - [Node.js](sql-api-nodejs-get-started.md)

Välkommen till självstudien om att komma igång med Azure Cosmos DB SQL API med .NET Core! När du har genomfört den här självstudien har du ett .NET Core-konsolprogram som skapar och skickar frågor mot Azure Cosmos DB-resurser. I den här självstudien används [version 3.0 +](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) av Azure Cosmos DB .NET SDK, som är inriktad på [.NET Standard 2.0.](https://docs.microsoft.com/dotnet/standard/net-standard)

Den här självstudiekursen omfattar:

> [!div class="checklist"]
>
> - Skapa och ansluta till ett Azure Cosmos-konto
> - Konfigurera ditt projekt i Visual Studio
> - Skapa en databas och en container
> - Lägga till objekt i containern
> - Fråga containern
> - CRUD-åtgärder på objektet
> - Ta bort databasen

Har du inte tid att skapa programmet? Oroa dig inte! Den kompletta lösningen finns på [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started). En snabbguide finns i avsnittet [Hämta den kompletta lösningen](#GetSolution).

Vill du skapa en Xamarin iOS-, Android- eller Forms-app med hjälp av SQL API och .NET Core SDK? Läs mer i [Bygga mobilappar med Xamarin och Azure Cosmos DB](mobile-apps-with-xamarin.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

- Om du inte har Visual Studio 2017 installerat kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) utan kostnad. Om du utvecklar en UWP-app (Universell Windows-plattform) bör du använda **Visual Studio 2017 med version 15.4** eller högre. Se till att du aktiverar arbetsbelastningen **Azure-utveckling** under installationen av Visual Studio.

  - Om du använder MacOS eller Linux kan du utveckla .NET Core-appar från kommandoraden genom att installera [.NET Core SDK](https://www.microsoft.com/net/core#macos) för valfri plattform.

  - Om du använder Windows kan du utveckla .NET Core-appar från kommandoraden genom att installera [.NET Core SDK](https://www.microsoft.com/net/core#windows).

  - Du kan använda din egen redigerare eller ladda ned [Visual Studio Code](https://code.visualstudio.com/) som är kostnadsfritt och fungerar i Windows, Linux och MacOS.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto

Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera en lösning i Visual Studio](#SetupVS). Om du använder Azure Cosmos DB-emulatorn följer du stegen i artikeln om [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och går vidare till [konfigurationen av Visual Studio-projektet](#SetupVS).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Steg 2: Konfigurera ett Visual Studio-projekt

1. Öppna **Visual Studio 2017** i datorn.
1. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
1. I dialogrutan **Nytt projekt** väljer du **Visual C#** / **Konsolapp (.NET Core)**, namnger projektet och klickar på **OK**.
   ![Skärmbild av fönstret nytt projekt](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-new-project.png)
1. I **Solution Explorer** högerklickar du på den nya konsolappen, som finns under din Visual Studio-lösning, och klickar sedan på **Hantera NuGet-paket ...**

   ![Skärmdump som visar den högerklickade menyn för projektet](./media/sql-api-dotnetcore-get-started/dotnetcore-tutorial-visual-studio-manage-nuget.png)

1. På fliken **NuGet** klickar du på **Bläddra** och skriver **Microsoft.Azure.Cosmos** i sökrutan.
1. Leta upp **Microsoft.Azure.Cosmos** i resultatet och klicka på **Installera**.
   Paket-ID:t för Azure Cosmos DB-klientbiblioteket är [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Skärmbild av NuGet-menyn där du hittar Azure Cosmos DB Client SDK](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

   Om du får ett meddelande om att granska ändringar i lösningen klickar du på **OK**. Om du får ett meddelande om godkännande av licens klickar du på **Jag godkänner**.

Bra! Konfigurationen är slutförd, så vi kan börja skriva kod. Det finns ett färdigt kodprojekt för den här självstudiekursen i [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started/).

## <a id="Connect"></a>Steg 3: Ansluta till ett Azure Cosmos DB-konto

1. Ersätt först referenserna i början av C#-programmet, i filen **Program.cs** med dessa referenser:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;
   ```

1. Lägg nu till dessa konstanter och variabler i den offentliga klassen `Program`.

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
       private CosmosDatabase database;

       // The container we will create.
       private CosmosContainer container;

       // The name of the database and container we will create
       private string databaseId = "FamilyDatabase";
       private string containerId = "FamilyContainer";
   }
   ```

   Observera att om du är bekant med den tidigare versionen av .NET SDK så är du kanske van vid att se termerna ”samling” och ”dokument”. Eftersom Azure Cosmos DB stöder flera API-modeller använder version 3.0+ av .NET SDK de allmänna termerna ”container” och ”objekt”. En container kan vara en samling, ett diagram eller en tabell. Ett objekt kan vara ett dokument, en kant/ett hörn eller en rad, och är innehållet i en container. [Läs mer om databaser, containrar och objekt.](databases-containers-items.md)

1. Hämta din slutpunkts-URL och primärnyckel från [Azure-portalen](https://portal.azure.com).

   Gå till ditt Azure Cosmos DB-konto på Azure Portal och klicka på **Nycklar**.

   Kopiera URI från portalen och klistra in den i `<your endpoint URL>` i filen `Program.cs`. Kopiera PRIMÄRNYCKELN från portalen och klistra in den i `<your primary key>`.

   ![Skärmbild för att hämta nycklar för Azure Cosmos DB från Azure-portalen](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Nu skapar vi en ny instans av `CosmosClient` och konfigurera ramar för programmet.

   Under metoden **Main** lägger du till en ny asynkron uppgift med namnet **GetStartedDemoAsync**, som instansierar vår nya `CosmosClient`. Vi använder **GetStartedDemoAsync** som den startpunkt som anropar metoder som behandlar Azure Cosmos DB-resurser.

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

   ```csharp
   public static async Task Main(string[] args)
   {
       // ADD THIS PART TO YOUR CODE
       try
       {
           Console.WriteLine("Beginning operations...\n");
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
   ```

1. Kör appen genom att välja **F5**. Konsolfönstrets utdata visar meddelandet `End of demo, press any key to exit.` som bekräftar att anslutningen till Azure Cosmos DB har gjorts. Därefter kan du stänga konsolfönstret.

Grattis! Du har anslutit till ett Azure Cosmos DB-konto.

## <a name="step-4-create-a-database"></a>Steg 4: Skapa en databas

En databas kan skapas med hjälp av antingen funktionen [**CreateDatabaseIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs) eller [**CreateDatabaseAsync**](https://aka.ms/CosmosDotnetAPIDocs) för klassen **CosmosDatabases**. En databas är en logisk container med objekt som är partitionerade över containrar.

1. Kopiera och klistra in metoden **CreateDatabase** nedanför metoden **GetStartedDemoAsync**. **CreateDatabase** skapar en ny databas med ID:t `FamilyDatabase` om det inte redan finns, med det ID som anges från fältet `databaseId`.

   ```csharp
   /*
       Create the database if it does not exist
   */
   private async Task CreateDatabase()
   {
       // Create a new database
       this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
       Console.WriteLine("Created Database: {0}\n", this.database.Id);
   }
   ```

1. Kopiera och klistra in koden nedan där du instansierade CosmosClient för att anropa metoden **CreateDatabase** som du precis lade till.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

       //ADD THIS PART TO YOUR CODE
       await this.CreateDatabase();
   }
   ```

   Nu bör koden se ut så här, med slutpunkten och primärnyckeln ifyllda. Observera att din namnrymd skiljer sig baserat på namnet på ditt projekt.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;
   using System.Collections.Generic;
   using System.Net;

   namespace CosmosGettingStartedDotnetCoreTutorial
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
           private CosmosDatabase database;

           // The container we will create.
           private CosmosContainer container;

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

           /*
               Entry point to call methods that operate on Azure Cosmos DB resources in this sample
           */
           public async Task GetStartedDemoAsync()
           {
               // Create a new instance of the Cosmos Client
               this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
               await this.CreateDatabase();
           }

           /*
               Create the database if it does not exist
           */
           private async Task CreateDatabase()
           {
               // Create a new database
               this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
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

En container kan skapas med [**CreateContainerIfNotExistsAsync**](https://aka.ms/CosmosDotnetAPIDocs)- eller [**CreateContainerAsync**](https://aka.ms/CosmosDotnetAPIDocs)-funktionen i klassen **CosmosContainers**. En container består av objekt (som i fallet med SQL-API:et är JSON-dokument) och tillhörande JavaScript-logik på serversidan, t.ex. lagrade procedurer, användardefinierade funktioner och utlösare.

1. Kopiera och klistra in metoden **CreateContainer** under metoden **CreateDatabase**. **CreateContainer** skapar en ny container med ID:t `FamilyContainer` om den inte redan finns, med det ID som angetts från fältet `containerId`.

   ```csharp
   /*
       Create the container if it does not exist.
       Specify "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
   */
   private async Task CreateContainer()
   {
       // Create a new container
       this.container = await this.database.Containers.CreateContainerIfNotExistsAsync(containerId, "/LastName");
       Console.WriteLine("Created Container: {0}\n", this.container.Id);
   }
   ```

1. Kopiera och klistra in koden nedan där du instansierade CosmosClient för att anropa metoden **CreateContainer** som du precis lade till.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();

       //ADD THIS PART TO YOUR CODE
       await this.CreateContainer();
   }
   ```

Kör appen genom att välja **F5**.

Grattis! Du har skapat en Azure Cosmos DB-container.

## <a id="CreateDoc"></a>Steg 6: Lägga till objekt i containern

Ett objekt kan skapas med hjälp av funktionen [**CreateItemAsync**](https://aka.ms/CosmosDotnetAPIDocs) i klassen **CosmosItems**. När du använder SQL-API:et projiceras objekt som dokument, som är användardefinierat (godtyckligt), JSON-innehåll. Nu kan du infoga ett objekt i Azure Cosmos DB-containern.

Först måste vi skapa klassen **Familj** som ska representera objekt som lagras i Azure Cosmos DB i det här exemplet. Vi kommer även att skapa underklasserna **Förälder**, **Barn**, **Husdjur** och **Adress** som används inom **Familj**. Observera att dokument måste ha en **id**-egenskap serialiserad som **id** i JSON.

1.  Välj **Ctrl+Skift+A** för att öppna dialogrutan **Lägg till nytt objekt**. Lägg till en ny klass, **Family.cs**, i projektet.

    ![Skärmbild av att lägga till en ny Family.cs klass i projektet](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1.  Kopiera och klistra in klasserna **Family** (Familj), **Parent** (Förälder), **Child** (Barn), **Pet** (Husdjur) och **Address** (Adress) i **Family.cs**. Observera att din namnrymd skiljer sig baserat på namnet på ditt projekt.

    ```csharp
    using Newtonsoft.Json;

    namespace CosmosGettingStartedDotnetCoreTutorial
    {
        public class Family
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
            public string LastName { get; set; }
            public Parent[] Parents { get; set; }
            public Child[] Children { get; set; }
            public Address Address { get; set; }
            public bool IsRegistered { get; set; }
            public override string ToString()
            {
                return JsonConvert.SerializeObject(this);
            }
        }

        public class Parent
        {
            public string FamilyName { get; set; }
            public string FirstName { get; set; }
        }

        public class Child
        {
            public string FamilyName { get; set; }
            public string FirstName { get; set; }
            public string Gender { get; set; }
            public int Grade { get; set; }
            public Pet[] Pets { get; set; }
        }

        public class Pet
        {
            public string GivenName { get; set; }
        }

        public class Address
        {
            public string State { get; set; }
            public string County { get; set; }
            public string City { get; set; }
        }
    }
    ```

1.  Gå tillbaka till **Program.cs** och lägg till metoden **AddItemsToContainer** under metoden **CreateContainer**.
    Koden kontrollerar att det inte redan finns ett objekt med samma ID innan det skapas. Vi Infogar även två objekt, ett för familjen Andersen och ett för familjen Wakefield.

    ```csharp
     /*
         Add Family items to the container
     */
     private async Task AddItemsToContainer()
     {
         // Create a family object for the Andersen family
         Family andersenFamily = new Family
         {
             Id = "Andersen.1",
             LastName = "Andersen",
             Parents = new Parent[]
             {
                 new Parent { FirstName = "Thomas" },
                 new Parent { FirstName = "Mary Kay" }
             },
             Children = new Child[]
             {
                 new Child
                 {
                     FirstName = "Henriette Thaulow",
                     Gender = "female",
                     Grade = 5,
                     Pets = new Pet[]
                     {
                         new Pet { GivenName = "Fluffy" }
                     }
                 }
             },
             Address = new Address { State = "WA", County = "King", City = "Seattle" },
             IsRegistered = true
         };

         // Read the item to see if it exists. Note ReadItemAsync will not throw an exception if an item does not exist. Instead, we check the StatusCode property off the response object.
         CosmosItemResponse<Family> andersenFamilyResponse = await this.container.Items.ReadItemAsync<Family>(andersenFamily.LastName, andersenFamily.Id);

         if (andersenFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
             andersenFamilyResponse = await this.container.Items.CreateItemAsync<Family>(andersenFamily.LastName, andersenFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
         }

         // Create a family object for the Wakefield family
         Family wakefieldFamily = new Family
         {
             Id = "Wakefield.7",
             LastName = "Wakefield",
             Parents = new Parent[]
             {
                 new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                 new Parent { FamilyName = "Miller", FirstName = "Ben" }
             },
             Children = new Child[]
             {
                 new Child
                 {
                     FamilyName = "Merriam",
                     FirstName = "Jesse",
                     Gender = "female",
                     Grade = 8,
                     Pets = new Pet[]
                     {
                         new Pet { GivenName = "Goofy" },
                         new Pet { GivenName = "Shadow" }
                     }
                 },
                 new Child
                 {
                     FamilyName = "Miller",
                     FirstName = "Lisa",
                     Gender = "female",
                     Grade = 1
                 }
             },
             Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
             IsRegistered = false
         };

         // Read the item to see if it exists
         CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily.Id);

         if (wakefieldFamilyResponse.StatusCode == HttpStatusCode.NotFound)
         {
             // Create an item in the container representing the Wakefield family. Note we provide the value of the partition key for this item, which is "Wakefield"
             wakefieldFamilyResponse = await this.container.Items.CreateItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily);

             // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse.
             //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
             Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", wakefieldFamilyResponse.Resource.Id, wakefieldFamilyResponse.RequestCharge);
         }
         else
         {
             Console.WriteLine("Item in database with id: {0} already exists\n", wakefieldFamilyResponse.Resource.Id);
         }
     }
    
    ```

1.  Lägg till ett anrop till `AddItemsToContainer` i metoden `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase();
        await this.CreateContainer();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainer();
    }
    ```

Kör appen genom att välja **F5**.

Grattis! Du har skapat två Azure Cosmos DB-objekt.

## <a id="Query"></a>Steg 7: Köra frågor mot Azure Cosmos DB-resurser

Azure Cosmos DB stöder [komplexa frågor](sql-api-sql-query.md) mot JSON-dokument som lagras i varje samling. I följande exempelkod visas hur du kör en fråga mot de objekt som vi infogade i det föregående steget.

1. Kopiera och klistra in metoden **RunQuery** under metoden **AddItemsToContainer**.

   ```csharp
   /*
       Run a query (using Azure Cosmos DB SQL syntax) against the container
   */
   private async Task RunQuery()
   {
       var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";
       var partitionKeyValue = "Andersen";

       Console.WriteLine("Running query: {0}\n", sqlQueryText);

       CosmosSqlQueryDefinition queryDefinition = new CosmosSqlQueryDefinition(sqlQueryText);
       CosmosResultSetIterator<Family> queryResultSetIterator = this.container.Items.CreateItemQuery<Family>(queryDefinition, partitionKeyValue);

       List<Family> families = new List<Family>();

       while (queryResultSetIterator.HasMoreResults)
       {
           CosmosQueryResponse<Family> currentResultSet = await queryResultSetIterator.FetchNextSetAsync();
           foreach (Family family in currentResultSet)
           {
               families.Add(family);
               Console.WriteLine("\tRead {0}\n", family);
           }
       }
   }
   ```

1. Lägg till ett anrop till `RunQuery` i metoden `GetStartedDemoAsync`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();

       //ADD THIS PART TO YOUR CODE
       await this.RunQuery();
   }
   ```

Kör appen genom att välja **F5**.

Grattis! Du har skickat frågor mot en Azure Cosmos DB-container.

## <a id="ReplaceItem"></a>Steg 8: Ersätta ett JSON-objekt

Vi uppdaterar nu ett objekt i Azure Cosmos DB.

1. Kopiera och klistra in metoden **ReplaceFamilyItem** under metoden **RunQuery**. Observera att vi ändrar egenskapen `IsRegistered` för familjen och `Grade` för ett av de underordnade objekten.

   ```csharp
   /*
   Update an item in the container
   */
   private async Task ReplaceFamilyItem()
   {
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>("Wakefield", "Wakefield.7");
       var itemBody = wakefieldFamilyResponse.Resource;

       // update registration status from false to true
       itemBody.IsRegistered = true;
       // update grade of child
       itemBody.Children[0].Grade = 6;

       // replace the item with the updated content
       wakefieldFamilyResponse = await this.container.Items.ReplaceItemAsync<Family>(itemBody.LastName, itemBody.Id, itemBody);
       Console.WriteLine("Updated Family [{0},{1}]\n. Body is now: {2}\n", itemBody.LastName, itemBody.Id, wakefieldFamilyResponse.Resource);
   }
   ```

1. Lägg till ett anrop till `ReplaceFamilyItem` i metoden `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();

       //ADD THIS PART TO YOUR CODE
       await this.ReplaceFamilyItem();
   }
   ```

Kör appen genom att välja **F5**.

Grattis! Du har ersatt ett Azure Cosmos DB-objekt.

## <a id="DeleteDocument"></a>Steg 9: Ta bort objekt

Vi tar nu bort ett objekt i Azure Cosmos DB.

1. Kopiera och klistra in metoden **DeleteFamilyItem** under metoden **ReplaceFamilyItem**.

   ```csharp
   /*
   Delete an item in the container
   */
   private async Task DeleteFamilyItem()
   {
       var partitionKeyValue = "Wakefield";
       var familyId = "Wakefield.7";

       // Delete an item. Note we must provide the partition key value and id of the item to delete
       CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.DeleteItemAsync<Family>(partitionKeyValue, familyId);
       Console.WriteLine("Deleted Family [{0},{1}]\n", partitionKeyValue, familyId);
   }
   ```

1. Lägg till ett anrop till `DeleteFamilyItem` i metoden `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteFamilyItem();
   }
   ```

Kör appen genom att välja **F5**.

Grattis! Du har tagit bort ett Azure Cosmos DB-objekt.

## <a id="DeleteDatabase"></a>Steg 10: Ta bort databasen

Nu tar vi bort databasen. Om du tar bort den skapade databasen tas databasen och alla underordnade resurser bort (containrar, objekt samt lagrade procedurer, användardefinierade funktioner och utlösare). Vi tar även bort **CosmosClient**-instansen.

1. Kopiera och klistra in metoden **DeleteDatabaseAndCleanup** under metoden **DeleteFamilyItem**.

   ```csharp
   /*
   Delete the database and dispose of the Cosmos Client instance
   */
   private async Task DeleteDatabaseAndCleanup()
   {
       CosmosDatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
       // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

       Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

       //Dispose of CosmosClient
       this.cosmosClient.Dispose();
   }
   ```

1. Lägg till ett anrop till `DeleteDatabaseAndCleanup` i metoden `GetStartedDemo`.

   ```csharp
   public async Task GetStartedDemoAsync()
   {
       // Create a new instance of the Cosmos Client
       this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
       await this.CreateDatabase();
       await this.CreateContainer();
       await this.AddItemsToContainer();
       await this.RunQuery();
       await this.ReplaceFamilyItem();
       await this.DeleteFamilyItem();

       //ADD THIS PART TO YOUR CODE
       await this.DeleteDatabaseAndCleanup();
   }
   ```

Kör appen genom att välja **F5**.

Grattis! Du har tagit bort en Azure Cosmos DB-databas.

## <a id="Run"></a>Steg 11: Köra C#-konsolappen i dess helhet!

Välj **F5** i Visual Studio för att bygga programmet i felsökningsläge.

Du bör nu se utdata från hela komma igång-appen i ett konsolfönster. Dessa utdata visar resultaten för de frågor som vi har lagt till och bör motsvara exempeltexten nedan.

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

För att bygga GetStarted-lösningen med alla exempel i den här artikeln behöver du följande:

- Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
- Ett [Azure Cosmos DB-konto][cosmos-db-create-account].
- [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-core-getting-started)-lösningen som finns på GitHub.

Om du vill återställa referenser till SQL API för Azure Cosmos DB .NET Core SDK i Visual Studio högerklickar du på **GetStarted**-lösningen i Solution Explorer och väljer sedan **Återställ NuGet-paket**. I filen **Program.cs** uppdaterar du sedan värdena `EndpointUri` och `PrimaryKey` enligt anvisningarna i [Ansluta till ett Azure Cosmos DB-konto](#Connect).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en .NET Core-app för att hantera Azure Cosmos DB SQL API-data. Du kan nu importera ytterligare data till ditt Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)

[cosmos-db-create-account]: create-sql-api-dotnet-preview.md#create-account
