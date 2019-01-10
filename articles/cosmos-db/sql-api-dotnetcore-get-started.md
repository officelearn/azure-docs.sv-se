---
title: 'Självstudie: Skapa en .NET Core-app för att hantera data som lagras i SQL API-kontot för Azure Cosmos DB'
description: Den här självstudien beskriver hur du skapar en onlinedatabas och ett C#-konsolprogram med hjälp av SQL API:et .NET Core SDK för Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 1aad68a3248561c86e195b55c1d509ab7a15964e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035686"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>Självstudie: Skapa en .NET Core-app för att hantera data som lagras i SQL API-kontot

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (förhandsversion)](sql-api-dotnet-core-get-started-preview.md)
> * [NET](sql-api-get-started.md)
> * [.NET (förhandsversion)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Som utvecklare kan du ha program som använder NoSQL-dokumentdata. Du kan använda SQL API-kontot i Azure Cosmos DB för att lagra och komma åt dessa dokumentdata. I den här självstudien visas hur du skapar en .NET Core-app för att skapa och fråga data som är lagrade i SQL API-kontot för Azure Cosmos DB. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa och ansluta till ett Azure Cosmos-konto
> * Konfigurera en Visual Studio-lösning
> * Skapa en onlinedatabas
> * Skapa en samling
> * Skapa JSON-dokument
> * Utföra CRUD-åtgärder på objekt, container och databas

Har du inte tid att skapa programmet? Den kompletta lösningen finns på [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Ladda ned och använd [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/), som är kostnadsfri. Om du utvecklar en UWP-app (Universell Windows-plattform) bör du använda **Visual Studio 2017 med version 15.4** eller högre. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

    * För MacOS eller Linux kan du utveckla .NET Core-appar från kommandoraden genom att installera [.NET Core SDK](https://www.microsoft.com/net/core#macos) för valfri plattform. 

    * För Windows kan du utveckla .NET Core-appar från kommandoraden genom att installera [.NET Core SDK](https://www.microsoft.com/net/core#windows). 

## <a name="create-an-azure-cosmos-account"></a>Skapa ett Azure Cosmos-konto

Använd följande steg för att skapa ett Azure Cosmos-konto:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Konfigurera din Visual Studio-lösning

1. Öppna **Visual Studio 2017** i datorn.

2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.

3. Välj **Mallar** > **Visual C#** > **.NET Core** > **Konsolprogram (.NET Core)** i dialogrutan **Nytt projekt**, ge projektet namnet **DocumentDBGettingStarted** och välj **OK**.

   ![Skärmdump som visar fönstret Nytt projekt](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. Högerklicka på **DocumentDBGettingStarted** i **Solution Explorer**.

5. På samma meny väljer du **Hantera NuGet-paket**.

   ![Skärmdump som visar den högerklickade menyn för projektet](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. Välj **Bläddra** längst upp på fliken **NuGet** och skriv **azure documentdb** i sökrutan.

7. Leta reda på **Microsoft.Azure.DocumentDB.Core** i resultatet och välj **Installera**.

   Paket-ID:t för biblioteket är [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Om du arbetar med en .NET Framework-version (t.ex. net461) som inte stöds av det här .NET Core NuGet-paketet använder du sedan [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) som har stöd för alla .NET Framework-versioner från och med .NET Framework 4.5.

8. Godkänn installationen av NuGet-paketet och licensavtalet när du uppmanas att göra det.

Nu när installationen är klar kan vi börja skriva kod. Det finns ett färdigt kodprojekt för den här självstudien i [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Ansluta till ett Azure Cosmos-konto

Ansluta till ett Azure Cosmos-konto genom att importera de nödvändiga beroendena. Om du vill importera beroenden lägger du till följande kod i början av filen Program.cs:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

Lägg till nedanstående två konstanter och *klientvariabeln* under den offentliga klassen *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Gå sedan till [Azure-portalen](https://portal.azure.com) och hämta din URI och primärnyckel. Azure Cosmos DB UPI och primärnyckeln behövs för att ditt program ska veta vart det ska ansluta, och för att Azure Cosmos DB ska lita på appens anslutning.

Gå till ditt Azure Cosmos-konto på Azure-portalen och välj **Nycklar**.

Kopiera URI från portalen och klistra in den i `<your endpoint URI>` i filen program.cs. Kopiera sedan PRIMÄRNYCKELN från portalen och klistra in den i `<your key>`. Ta bort < och > men lämna de dubbla citattecknen kring din slutpunkt och nyckel.

![Hämta nycklar från Azure-portalen][keys]

Vi börjar med att skapa en ny instans av **DocumentClient**.

Under metoden **Main** lägger du till den här nya asynkrona aktiviteten kallad **GetStartedDemo**, som instantierar vår nya **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Lägg till nedanstående kod för att köra den asynkrona aktiviteten från metoden **Main**. Metoden **Main** fångar upp undantag och skriver dem till konsolen.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Välj knappen **DocumentDBGettingStarted** så skapas och körs programmet.

## <a id="CreateDatabase"></a>Skapa en databas

Lägg till en hjälpmetod för skrivning till konsolen innan du lägger till kod som skapar en databas. Kopiera och klistra in metoden **WriteToConsoleAndPromptToContinue** under metoden **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Skapa din Azure Cosmos DB-databas genom att använda metoden `CreateDatabaseAsync` i klassen **DocumentClient**. En databas är en logisk container för JSON-dokumentlagring, partitionerad över samlingarna. Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under den skapade klienten. Då skapas en databas med namnet *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Välj knappen **DocumentDBGettingStarted** när du vill köra ditt program.

## <a id="CreateColl"></a>Skapa en samling

Skapa en samling med hjälp av `CreateDocumentCollectionAsync`-metoden för klassen **DocumentClient**. En samling är en container för JSON-dokument och associerad JavaScript-applogik.

> [!WARNING]
> **CreateDocumentCollectionAsync** skapar en ny samling med reserverat dataflöde, vilket påverkar priset. Mer information finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/).

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under koden som skapade databasen. Koden skapar en dokumentsamling som heter *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Välj knappen **DocumentDBGettingStarted** när du vill köra ditt program.

## <a id="CreateDoc"></a>Skapa JSON-dokument

Skapa ett dokument med hjälp av `CreateDocumentAsync` -metoden för klassen **DocumentClient**. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Du kan nu infoga ett eller flera dokument. 

Först skapar du klassen **Familj** som representerar objekt som lagras i Azure Cosmos DB. Du kommer även att skapa underklasserna **Förälder**, **Barn**, **Husdjur** och **Adress** som används inom **Familj**. Dokument måste ha en **id**-egenskap serialiserad som **id** i JSON. Skapa dessa klasser genom att lägga till nedanstående interna undergrupper efter metoden **GetStartedDemo**. Kopiera och klistra in klasserna **Familj**, **Förälder**, **Barn**, **Husdjur** och **Adress** under metoden **WriteToConsoleAndPromptToContinue**.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

Kopiera och klistra in metoden **CreateFamilyDocumentIfNotExists** under metoden **CreateDocumentCollectionIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

Infoga också två dokument, ett för familjen Andersen och ett för familjen Wakefield. Kopiera och klistra in koden under `// ADD THIS PART TO YOUR CODE` i metoden **GetStartedDemo** under koden som skapade dokumentsamlingen.

```csharp
await this.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Välj knappen **DocumentDBGettingStarted** när du vill köra ditt program.

![Hierarkisk relation mellan kontot, onlinedatabasen, samlingen](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Skicka frågor mot Azure Cosmos DB-resurser

Azure Cosmos DB stöder komplexa frågor mot JSON-dokument som lagras i varje samling. Nedanstående exempelkod visar olika frågor – med både Azure Cosmos DB SQL-syntax och LINQ – som du kan köra mot dokumenten som infogades i föregående steg.

Kopiera och klistra in metoden **ExecuteSimpleQuery** under metoden **CreateFamilyDocumentIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under koden som skapade det andra dokumentet.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Välj knappen **DocumentDBGettingStarted** när du vill köra ditt program.

Följande diagram illustrerar hur Azure Cosmos DB SQL-frågesyntaxen anropas mot samlingen som du skapade. Samma logik gäller för LINQ-frågan.

![Diagram som illustrerar omfånget och innebörden av frågan som används i NoSQL-självstudiekursen för att skapa en C#-konsolapp](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

Nyckelordet `FROM` är valfritt i frågan eftersom Azure Cosmos DB-frågor redan är begränsade till en enda samling. ”FROM Families f” kan därför bytas mot ”FROM root r” eller annat valfritt variabelnamn som du väljer. Azure Cosmos DB härleder familjerna, roten eller variabelnamnet som du har valt och refererar som standard till den aktuella samlingen.

## <a id="ReplaceDocument"></a>Ersätta JSON-dokument

Azure Cosmos DB har stöd för ersättning av JSON-dokument.  

Kopiera och klistra in metoden **ReplaceFamilyDocument** under metoden **ExecuteSimpleQuery**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under frågekörningskoden. När du ersätter dokumentet körs samma fråga igen för att visa det ändrade dokumentet.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Välj knappen **DocumentDBGettingStarted** när du vill köra ditt program.

## <a id="DeleteDocument"></a>Ta bort JSON-dokument

Azure Cosmos DB har stöd för borttagning av JSON-dokument.  

Kopiera och klistra in metoden **DeleteFamilyDocument** under metoden **ReplaceFamilyDocument**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under koden för den andra frågekörningen.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Välj knappen **DocumentDBGettingStarted** när du vill köra ditt program.

## <a id="DeleteDatabase"></a>Ta bort databasen

Om du tar bort databasen du skapade försvinner databasen och alla underordnade resurser (t.ex. samlingar och dokument). Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under dokumentet om du vill ta bort hela databasen och alla underordnade resurser.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Välj knappen **DocumentDBGettingStarted** när du vill köra ditt program.

## <a id="Run"></a>Kör C#-konsolappen

Välj knappen **DocumentDBGettingStarted** i Visual Studio om du vill bygga programmet i felsökningsläge. Du bör nu se utdata från din app för att komma igång i konsolfönstret. Dessa utdata visar resultaten för de frågor som vi har lagt till och bör motsvara exempeltexten nedan.

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Du har nu slutfört självstudien och har ett fungerande C#-konsolprogram.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, Azure Cosmos-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en .NET Core-app för att hantera data som är lagrade i SQL API-kontot för Azure Cosmos DB. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Skapa en Java-konsolapp med SQL API-konto för Azure Cosmos DB](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
