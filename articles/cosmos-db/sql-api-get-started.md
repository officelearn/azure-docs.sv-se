---
title: "Azure Cosmos DB: SQL API komma igång-självstudiekurs | Microsoft Docs"
description: "En självstudiekurs som skapar en onlinedatabas och C#-konsolprogram har med hjälp av SQL-API."
keywords: "självstudier för nosql, onlinedatabas, c#-konsolprogram"
services: cosmos-db
documentationcenter: .net
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2017
ms.author: anhoh
ms.openlocfilehash: 28714106a6228b5bdaa1933d6e8ea89105eb4b30
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-sql-api-getting-started-tutorial"></a>Azure Cosmos DB: SQL API komma igång-Självstudier
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js för MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Välkommen till den Azure SQL DB-API Cosmos komma igång-kursen! När du har genomfört den här självstudiekursen har du ett konsolprogram som skapar och skickar frågor till Azure Cosmos DB-resurser.

Den här kursen ingår:

* Skapa och ansluta till ett Azure Cosmos DB-konto
* Konfigurera en Visual Studio-lösning
* Skapa en onlinedatabas
* Skapa en samling
* Skapa JSON-dokument
* Skicka frågor till samlingen
* Ersätta ett dokument
* Ta bort ett dokument
* Ta bort databasen

Har du inte tid? Oroa dig inte! Den kompletta lösningen finns på [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). En snabbguide finns i avsnittet [Hämta den fullständiga lösningen till NoSQL-självstudiekursen](#GetSolution).

Nu sätter vi igång!

## <a name="prerequisites"></a>Krav

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)].

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto
Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera en lösning i Visual Studio](#SetupVS). Om du använder Azure Cosmos DB-emulatorn, Följ stegen i [Azure Cosmos DB emulatorn](local-emulator.md) konfigurera emulatorn och gå vidare till [konfigurera din Visual Studio-lösning](#SetupVS).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Steg 2: Konfigurera en lösning i Visual Studio
1. Öppna **Visual Studio 2017** i datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I dialogen **Nytt projekt** väljer du **Mallar** / **Visual C#** / **Konsolapp**. Namnge projektet och klicka sedan på **OK**.
   ![Skärmdump som visar fönstret Nytt projekt](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
4. I **Solution Explorer** högerklickar du på den nya konsolappen, som finns under din Visual Studio-lösning, och klickar sedan på **Hantera NuGet-paket ...**
    
    ![Skärmdump som visar den högerklickade menyn för projektet](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
5. I den **NuGet** klickar du på **Bläddra**, och skriv **azure documentdb** i sökrutan.
6. Leta reda på **Microsoft.Azure.DocumentDB** i resultatet och klicka på **Installera**.
   Paket-ID för klientbiblioteket för Azure Cosmos DB SQL API är [Microsoft Azure Cosmos DB klientbibliotek](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).
   ![Skärmbild som visar NuGet-menyn för att hitta Azure Cosmos DB klient-SDK](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

    Om du får ett meddelande om att granska ändringar i lösningen klickar du på **OK**. Om du får ett meddelande om godkännande av licens klickar du på **Jag godkänner**.

Bra! Konfigurationen är slutförd, så vi kan börja skriva kod. Det finns ett färdigt kodprojekt för den här självstudiekursen i [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a id="Connect"></a>Steg 3: Ansluta till ett Azure Cosmos DB-konto
Lägg först till dessa referenser till början av C#-appen, i filen Program.cs:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> För att kunna slutföra den här självstudiekursen är det viktigt att du lägger till beroendena ovan.
> 
> 

Lägg till nedanstående två konstanter och *klientvariabeln* under den offentliga klassen *Program*.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUrl = "<your endpoint URL>";
        private const string PrimaryKey = "<your primary key>";
        private DocumentClient client;

Därefter head tillbaka till den [Azure-portalen](https://portal.azure.com) att hämta dina slutpunkts-URL och primärnyckel. Slutpunkts-URL:en och den primära nyckeln behövs för att programmet ska veta vart ditt program ska ansluta, och för att Azure Cosmos DB ska lita på programmets anslutning.

Navigera till ditt Azure DB som Cosmos-konto i Azure-portalen och sedan på **nycklar**.

Kopiera URI från portalen och klistra in den i `<your endpoint URL>` i filen program.cs. Kopiera sedan PRIMÄRNYCKELN från portalen och klistra in den i `<your primary key>`.

![Skärmbild av Azure portal som används i NoSQL-självstudiekursen för att skapa ett C#-konsolprogram. Visar ett Azure DB som Cosmos-konto med hubben aktiv markerad, knappen nycklar markerad på sidan för Azure DB som Cosmos-konto och värdena URI, PRIMÄRNYCKEL och SEKUNDÄRNYCKEL markerade på sidan nycklar][keys]

Härnäst ska vi starta programmet genom att skapa en ny instans av **DocumentClient**.

Under metoden **Main** lägger du till den här nya asynkrona aktiviteten kallad **GetStartedDemo**, som instantierar vår nya **dokumentklient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
    }

Lägg till nedanstående kod för att köra den asynkrona aktiviteten från metoden **Main**. Metoden **Main** ska fånga upp undantag och skriva dem till konsolen.

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

Kör appen genom att trycka på **F5**. Konsolfönstrets utdata visar meddelandet `End of demo, press any key to exit.` som bekräftar att anslutningen har gjorts.  Därefter kan du stänga konsolfönstret. 

Grattis! Nu när du har anslutit till ett Azure Cosmos DB-konto ska vi gå vidare och se hur du arbetar med Azure Cosmos DB-resurser.  

## <a name="step-4-create-a-database"></a>Steg 4: Skapa en databas
Lägg till en hjälpmetod för skrivning till konsolen innan du lägger till kod som skapar en databas.

Kopiera och klistra in metoden **WriteToConsoleAndPromptToContinue** efter metoden **GetStartedDemo**.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Du kan skapa Azure Cosmos DB-[databasen](sql-api-resources.md#databases) med hjälp av metoden [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) i klassen **DocumentClient**. En databas är en logisk behållare för JSON-dokumentlagring, partitionerad över samlingarna.

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** efter den skapade klienten. Då skapas en databas med namnet *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

Kör appen genom att trycka på **F5**.

Grattis! Du har skapat en Azure Cosmos DB-databas.  

## <a id="CreateColl"></a>Steg 5: Skapa en samling
> [!WARNING]
> **CreateDocumentCollectionIfNotExistsAsync** skapar en ny samling med reserverat dataflöde, vilket får konsekvenser för priset. Mer information finns på vår [sida med priser](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Du kan skapa en [samling](sql-api-resources.md#collections) med metoden [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) för klassen **DocumentClient**. En samling är en behållare för JSON-dokument och associerad JavaScript-applogik.

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** efter koden som skapade databasen. Då skapas en dokumentsamling som heter *FamilyCollection*.

        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

        // ADD THIS PART TO YOUR CODE
         await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });

Kör appen genom att trycka på **F5**.

Grattis! Du har skapat en Azure Cosmos DB-dokumentsamling.  

## <a id="CreateDoc"></a>Steg 6: Skapa JSON-dokument
Du kan skapa [dokument](sql-api-resources.md#documents) med metoden [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) för klassen **dokumentklient**. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Vi kan nu infoga ett eller flera dokument. Om du redan har data som du vill lagra i databasen kan du använda Azure Cosmos DB [datamigreringsverktyget](import-data.md) att importera data till en databas.

Först måste vi skapa klassen **Familj** som ska representera objekt som lagras i Azure Cosmos DB i det här exemplet. Vi kommer även att skapa underklasserna **Förälder**, **Barn**, **Husdjur** och **Adress** som används inom **Familj**. Observera att dokument måste ha en **id**-egenskap serialiserad som **id** i JSON. Skapa dessa klasser genom att lägga till nedanstående interna undergrupper efter metoden **GetStartedDemo**.

Kopiera och klistra in klasserna **Familj**, **Förälder**, **Barn**, **Husdjur** och **Adress** efter metoden **WriteToConsoleAndPromptToContinue**.

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

Kopiera och klistra in metoden **CreateFamilyDocumentIfNotExists** under klassen **Adress**.

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

Infoga också två dokument, ett för familjen Andersen och ett för familjen Wakefield.

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** efter koden som skapade dokumentsamlingen.

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
    
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });


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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

Kör appen genom att trycka på **F5**.

Grattis! Du har skapat två Azure Cosmos DB-dokument.  

![Diagram som illustrerar den hierarkiska relationen mellan kontot, onlinedatabasen, samlingen och dokumenten som används i NoSQL-självstudiekursen för att skapa en C#-konsolapp](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Steg 7: Skicka frågor mot Azure Cosmos DB-resurser
Azure Cosmos DB stöder [komplexa frågor](sql-api-sql-query.md) mot JSON-dokument som lagras i varje samling.  Nedanstående exempelkod visar olika frågor – med både Azure Cosmos DB SQL-syntax och LINQ – som du kan köra mot dokumenten som infogades i föregående steg.

Kopiera och klistra in metoden **ExecuteSimpleQuery** efter metoden **CreateFamilyDocumentIfNotExists**.

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

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** efter koden som skapade det andra dokumentet.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Kör appen genom att trycka på **F5**.

Grattis! Du har skickat frågor mot en Azure Cosmos DB-samling.

Nedanstående diagram illustrerar hur Azure Cosmos DB SQL-frågesyntaxen anropas mot samlingen som du skapade. Samma logik gäller även för LINQ-frågan.

![Diagram som illustrerar omfånget och innebörden av frågan som används i NoSQL-självstudiekursen för att skapa en C#-konsolapp](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Den [FROM](sql-api-sql-query.md#FromClause) nyckelord är valfritt i frågan eftersom Azure DB som Cosmos-frågor redan är begränsade till en enda samling. ”FROM Families f” kan därför bytas mot ”FROM root r” eller annat valfritt variabelnamn som du väljer. Azure Cosmos-DB kommer härleda att familjer, roten eller variabelnamnet som du har valt, referera till den aktuella samlingen som standard.

## <a id="ReplaceDocument"></a>Steg 8: Ersätta JSON-dokument
Azure Cosmos DB har stöd för ersättning av JSON-dokument.  

Kopiera och klistra in metoden **ReplaceFamilyDocument** efter metoden **ExecuteSimpleQuery**.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
         await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
         this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** efter frågekörningskoden i slutet av metoden. När du ersätter dokumentet körs samma fråga igen för att visa det ändrade dokumentet.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Kör appen genom att trycka på **F5**.

Grattis! Du har ersatt ett Azure Cosmos DB-dokument.

## <a id="DeleteDocument"></a>Steg 9: Ta bort JSON-dokument
Azure Cosmos DB har stöd för borttagning av JSON-dokument.  

Kopiera och klistra in metoden **DeleteFamilyDocument** efter metoden **ReplaceFamilyDocument**.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
         await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
         Console.WriteLine("Deleted Family {0}", documentName);
    }

Kopiera och klistra in följande kod till metoden **GetStartedDemo** efter den andra frågekörningskoden i slutet av metoden.

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);
    
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
    
    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

Kör appen genom att trycka på **F5**.

Grattis! Du har tagit bort ett Azure Cosmos DB-dokument.

## <a id="DeleteDatabase"></a>Steg 10: Ta bort databasen
Om du tar bort databasen du skapade försvinner databasen och alla underordnade resurser (t.ex. samlingar och dokument).

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** efter dokumentborttagningskoden om du vill ta bort hela databasen och alla underordnade resurser.

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

Kör appen genom att trycka på **F5**.

Grattis! Du har tagit bort en Azure Cosmos DB-databas.

## <a id="Run"></a>Steg 11: Kör C#-konsolappen i sin helhet!
Tryck på F5 i Visual Studio för att bygga appen i felsökningsläge.

Du bör se utdata från get started-appens i konsolfönstret. Dessa utdata visar resultaten för de frågor som vi har lagt till och bör motsvara exempeltexten nedan.

    Created FamilyDB
    Press any key to continue ...
    Created FamilyCollection
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

Grattis! Du har slutfört självstudiekursen och har ett fungerande C#-konsolprogram!

## <a id="GetSolution"></a>Hämta den fullständiga lösningen för självstudiekursen
Om du inte har tid att slutföra stegen i den här självstudien eller bara vill ladda ned kodexemplen kan du hämta den från [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

För att bygga GetStarted-lösningen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En [Azure Cosmos DB konto][cosmos-db-create-account].
* [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started)-lösningen som finns på GitHub.

Om du vill återställa referenser till Azure Cosmos DB .NET SDK i Visual Studio högerklickar du på den **GetStarted** lösningen i Solution Explorer och klicka sedan på **aktivera NuGet-Paketåterställning**. I filen App.config uppdaterar du sedan värdena EndpointUrl och AuthorizationKey enligt anvisningarna i [Ansluta till ett Azure Cosmos DB-konto](#Connect).

Då är det bara att bygga den, så är du på väg!


## <a name="next-steps"></a>Nästa steg
* Behöver du en mer komplex ASP.NET MVC-självstudiekurs? Se [självstudiekurs om ASP.NET MVC: utveckling av Webbappar med Azure Cosmos DB](sql-api-dotnet-application.md).
* Vill du testa skalning och prestanda med Azure Cosmos DB? Se [prestanda och skalning testa med Azure Cosmos DB](performance-testing.md)
* Lär dig hur du [övervaka Azure Cosmos DB begäranden, användning och lagring](monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).
* Läs mer om Azure Cosmos DB i [Välkommen till Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
[cosmos-db-create-account]: create-sql-api-dotnet.md#create-account
