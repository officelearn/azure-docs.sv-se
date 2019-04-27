---
title: Skapa en .NET-konsolapp för att hantera data i Azure Cosmos DB SQL API-konto
description: En självstudiekurs som skapar en onlinedatabas och C# konsolapp med hjälp av SQL-API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: 64aef17663fdc28a467172bbe8954fc06fdb7ff0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686512"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Skapa en .NET-konsolapp för att hantera data i Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [NET](sql-api-get-started.md)
> * [.NET (förhandsversion)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (förhandsversion)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Välkommen till att Azure Cosmos DB SQL API-komma igång-Självstudier. När du har slutfört den här självstudien har du en konsolapp som skapar och skickar frågor till Azure Cosmos DB-resurser.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
>
> - Skapa ett Azure Cosmos DB-konto och ansluter till den
> - Konfigurera en Visual Studio-lösning
> - Skapa en databas
> - Skapa en samling
> - Skapa JSON-dokument
> - Fråga samlingen
> - Uppdatera ett JSON-dokument
> - Ta bort ett dokument
> - Ta bort databasen

## <a name="prerequisites"></a>Nödvändiga komponenter

Visual Studio 2017 med Azure-utveckling arbetsflödet installerad:
- Du kan hämta och använda den **kostnadsfria** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio. 

En Azure-prenumeration eller ett kostnadsfritt utvärderingskonto för Cosmos DB:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Om du använder Azure Cosmos DB-emulatorn följer du stegen i [Azure Cosmos DB-emulatorn](local-emulator.md) att konfigurera emulatorn. Starta självstudie på [ställa in Visual Studio-lösningen](#SetupVS).
  
## <a name="get-the-completed-solution"></a>Hämta den färdiga lösningen

Om du inte har tid att slutföra den här självstudien eller bara vill ha kodexemplen kan du hämta den fullständiga lösningen från [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Kör den fullständiga lösningen till hämtade: 

1. Kontrollera att du har den [krav](#prerequisites) installerad. 
1. Öppna den hämtade *GetStarted.sln* lösningsfilen i Visual Studio.
1. I **Solution Explorer**, högerklicka på den **GetStarted** projektet och välj **hantera NuGet-paket**.
1. På den **NuGet** fliken **återställa** att återställa referenser till Azure Cosmos DB .NET SDK.
1. I den *App.config* uppdaterar den `EndpointUrl` och `PrimaryKey` värden enligt beskrivningen i den [Anslut till Azure Cosmos DB-kontot](#Connect) avsnittet.
1. Välj **felsöka** > **starta utan felsökning** eller tryck på **Ctrl**+**F5** att bygga och köra appen.

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Följ dessa instruktioner för att skapa ett Azure Cosmos DB-konto i Azure-portalen. Om du redan har ett Azure Cosmos DB-konto som ska användas, gå vidare till [ställa in Visual Studio-lösningen](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Konfigurera Visual Studio-lösning

1. I Visual Studio 2017, Välj **filen** > **New** > **projekt**.
   
1. I den **nytt projekt** dialogrutan **Visual C#**   >  **Konsolapp (.NET Framework)**, namnge projektet *AzureCosmosDBApp* , och välj sedan **OK**.
   
   ![Skärmdump som visar fönstret Nytt projekt](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. I **Solution Explorer**, högerklicka på den **AzureCosmosDBApp** projektet och välj **hantera NuGet-paket**.
   
   ![Snabbmenyn för projektet](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. På den **NuGet** fliken **Bläddra**, och ange *azure documentdb* i sökrutan.
   
1. Hitta och välja **Microsoft.Azure.DocumentDB**, och välj **installera** om den inte redan är installerat.
   
   Paket-ID:t för Azure Cosmos DB-klientbiblioteket är [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).

   > [!NOTE]
   > Om du använder .NET Core finns [.NET Core-dokument](./sql-api-dotnetcore-get-started.md).

   ![Skärmbild av NuGet-menyn där du hittar Azure Cosmos DB Client SDK](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Om du får ett meddelande om att förhandsgranska ändringar i lösningen, Välj **OK**. Om du får ett meddelande om godkännande av licens väljer **jag accepterar**.

## <a id="Connect"></a>Anslut till Azure Cosmos DB-konto

Kom igång nu skriva kod. Den fullständiga *Project.cs* -fil för den här självstudien är i [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. I **Solution Explorer**väljer *Program.cs*, och i kodredigeraren, lägger du till följande referenser till början av filen:
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. Lägg sedan till följande två konstanter och `client` variabeln `public class Program`.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. Slutpunkts-URL och primärnyckel från att din app kan ansluta till ditt Azure Cosmos DB-konto och Azure Cosmos DB-kontot ska lita på anslutningen. Kopiera nycklarna från den [Azure-portalen](https://portal.azure.com), och klistra in dem i din kod. 

   
   1. I ditt Azure Cosmos DB-konto vänstra navigeringsfönstret väljer **nycklar**.
      
      ![Visa och kopiera åtkomstnycklar i Azure portal](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. Under **Läs-och skrivnycklar**, kopiera den **URI** värdet med kopieringsknappen till höger och klistra in den i `<your endpoint URL>` i *Program.cs*. Exempel: 
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Kopiera den **PRIMÄRNYCKEL** värde och klistra in den i `<your primary key>` i *Program.cs*. Exempel: 
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. Efter den `Main` metod, lägga till en nya asynkrona aktiviteten kallad `GetStartedDemo`, vilket skapar en instans av en ny `DocumentClient` kallas `client`.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```
   
1. Lägg till följande kod till den `Main` metoden för att köra den `GetStartedDemo` uppgift. Den `Main` metoden fångar undantag och skriver dem till konsolen.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Tryck på **F5** att köra din app. 
   
1. När du ser meddelandet **slutet av demo, trycker du på valfri tangent för att avsluta** i konsolfönstret, innebär en anslutning har upprättats. Tryck på valfri tangent för att stänga konsolfönstret. 

Du har anslutit till ditt Azure Cosmos DB-konto. Nu fungerar med några Azure Cosmos DB-resurser.  

## <a name="create-a-database"></a>Skapa en databas

Ett Azure Cosmos DB [databasen](databases-containers-items.md#azure-cosmos-databases) är en logisk behållare för JSON dokumentlagring, partitionerad över samlingarna. Du skapar en databas med hjälp av den [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) -metoden för den `DocumentClient` klass. 

1. Lägg till en hjälpmetod för skrivning till konsolen innan du lägger till kod som skapar en databas. Kopiera och klistra in följande `WriteToConsoleAndPromptToContinue` metoden efter den `GetStartedDemo` -metod i din kod.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Kopiera och klistra in följande rad för att din `GetStartedDemo` metod, efter den `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` rad. Den här koden skapar en databas med namnet `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Tryck på **F5** att köra din app.

Du skapat har en Azure Cosmos DB-databas. Du kan se databasen i den [Azure-portalen](https://portal.azure.com) genom att välja **Datautforskaren** i din Azure Cosmos DB-konto vänstra navigeringsfönstret. 

## <a id="CreateColl"></a>Skapa en samling

En samling är en container för JSON-dokument och associerad JavaScript-applogik. Du skapar en samling med hjälp av den [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) -metoden för den `DocumentClient` klass. 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** skapar en ny samling med reserverat dataflöde, vilket får konsekvenser för priset. Mer information finns i [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Kopiera och klistra in nedanstående kod till din `GetStartedDemo` metoden efter den `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` rad. Den här koden skapar en dokumentsamling som heter `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Tryck på **F5** att köra din app.

Du har nu skapat ett Azure Cosmos DB-dokumentsamling. Du kan se samlingen under din **FamilyDB** databasen i **Datautforskaren** i Azure-portalen.  

## <a id="CreateDoc"></a>Skapa JSON-dokument

Dokument är en användardefinierad, godtyckliga JSON-innehåll. Dokument måste ha en ID-egenskap serialiserad som `id` i JSON. Du skapar dokument med hjälp av den [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) -metoden för den `DocumentClient` klass. 

> [!TIP]
> Om du redan har data som du vill lagra i databasen kan du använda Azure Cosmos DB [datamigreringsverktyget](import-data.md) att importera den.
>

Följande kod skapar och infogar två dokument i din databassamling. Först skapar du en `Family` klass, och `Parent`, `Child`, `Pet`, och `Address` underklasser ska användas i `Family`. Sedan skapar du en `CreateFamilyDocumentIfNotExists` metod, och sedan skapa och infoga också två dokument. 

1. Kopiera och klistra in följande `Family`, `Parent`, `Child`, `Pet`, och `Address` klasserna efter den `WriteToConsoleAndPromptToContinue` -metod i din kod.
   
   ```csharp
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
   ```
   
1. Kopiera och klistra in följande `CreateFamilyDocumentIfNotExists` metoden efter den `Address` klass som du just lade till.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Kopiera och klistra in följande kod i slutet av din `GetStartedDemo` metod, efter den `await client.CreateDocumentCollectionIfNotExistsAsync` rad. Den här koden skapar och infogar två dokument, ett för familjen Andersen och Wakefield familjer.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Tryck på **F5** att köra din app.

Du har skapat två Azure Cosmos DB-dokument. Du kan se dokument under din **FamilyDB** databasen och **FamilyCollection** samling i **Datautforskaren** i Azure-portalen.   

![Diagram som illustrerar den hierarkiska relationen mellan kontot, onlinedatabasen, samlingen och dokumenten](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Köra frågor mot Azure Cosmos DB-resurser

Azure Cosmos DB stöder omfattande [frågor](how-to-sql-query.md) mot JSON-dokument som lagras i samlingar. Följande exempelkod använder LINQ- och Azure Cosmos DB SQL-syntax för att köra en fråga mot exempeldokument.

1. Kopiera och klistra in följande `ExecuteSimpleQuery` metoden efter den `CreateFamilyDocumentIfNotExists` -metod i din kod.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Kopiera och klistra in följande kod i slutet av din `GetStartedDemo` metod, efter den `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` rad.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Tryck på **F5** att köra din app.

Den föregående frågan returnerar hela objektet för familjen Andersen. Du har skickat frågor mot en Azure Cosmos DB-samling.

Följande diagram illustrerar hur Azure Cosmos DB SQL-frågesyntaxen anropas mot samlingen. Samma logik gäller för LINQ-frågan.

![Diagram som illustrerar omfånget och innebörden av frågan som används i NoSQL-självstudiekursen för att skapa en C#-konsolapp](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Den [FROM](how-to-sql-query.md#FromClause) nyckelord i SQL-frågan är valfritt, eftersom Azure Cosmos DB-frågor redan är begränsade till en enda samling. Du kan växla `FROM Families f` med `FROM root r`, eller andra variabelnamnet som du väljer. Azure Cosmos DB kommer härleda som `Families`, `root`, eller variabelnamnet som du väljer refererar till den aktuella samlingen.

## <a id="ReplaceDocument"></a>Uppdatera ett JSON-dokument

Azure Cosmos DB SQL API har stöd för uppdatering och ersätta JSON-dokument.  

1. Kopiera och klistra in följande `ReplaceFamilyDocument` metoden efter den `ExecuteSimpleQuery` -metod i din kod.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Kopiera och klistra in följande kod i slutet av din `GetStartedDemo` metod, efter den `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` rad. Koden uppdaterar data i ett av dokumenten och kör frågan igen för att visa det ändrade dokumentet.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Tryck på **F5** att köra din app.

Frågeresultatet visar att den `Grade` för familjen Andersen underordnade uppdateras från `5` till `6`. Du har har uppdaterats och ersatt ett Azure Cosmos DB-dokument. 

## <a id="DeleteDocument"></a>Ta bort ett JSON-dokument

Azure Cosmos DB SQL API stöder borttagning av JSON-dokument.  

1. Kopiera och klistra in följande `DeleteFamilyDocument` metoden efter den `ReplaceFamilyDocument` metoden.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Kopiera och klistra in följande kod i slutet av din `GetStartedDemo` metod när andra `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` rad.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Tryck på **F5** att köra din app.

Du har tagit bort ett Azure Cosmos DB-dokument. 

## <a id="DeleteDatabase"></a>Ta bort databasen

Ta bort databasen du skapade för att ta bort den och alla dess underordnade resurser, inklusive samling och dokument. 

1. Kopiera och klistra in följande kod i slutet av din `GetStartedDemo` metod, efter den `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` rad. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Tryck på **F5** att köra din app.

Du har tagit bort Azure Cosmos DB-databasen. Du kan se i den **Datautforskaren** för ditt Azure Cosmos DB-konto att FamilyDB-databasen har tagits bort. 

## <a id="Run"></a>Kör hela C# konsolapp

Tryck på **F5** i Visual Studio för att skapa och kör en fullständig C# konsolapp i felsökningsläge. Du bör se följande utdata i konsolfönstret:

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Grattis! Du har slutfört självstudiekursen och har en fungerande C# konsolapp som skapar frågor, uppdaterar och tar bort Azure Cosmos DB-resurser.  

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Cosmos DB i [Välkommen till Azure Cosmos DB](introduction.md).
* En mer komplex ASP.NET MVC-självstudiekurs finns i [självstudiekurs om ASP.NET MVC: Utveckling av webbprogram med Azure Cosmos DB](sql-api-dotnet-application.md).
* Om du vill utföra skalnings- och prestandatester med Azure Cosmos DB, se [prestanda och skalningstester med Azure Cosmos DB](performance-testing.md).
* Läs hur du övervakar Azure Cosmos DB-begäranden, användning och lagring i [övervaka konton](monitor-accounts.md).
* Kör frågor mot en exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).

