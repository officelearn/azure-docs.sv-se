---
title: 'Självstudiekurs om NoSQL: DocumentDB .NET SDK | Microsoft Docs'
description: En självstudiekurs om NoSQL som skapar en onlinedatabas och en C#-konsolapp med .NET DocumentDB SDK. DocumentDB är en NoSQL-databas för JSON.
keywords: Självstudier för nosql, onlinedatabas, c#-konsolprogram
services: documentdb
documentationcenter: .net
author: AndrewHoh
manager: jhubbard
editor: monicar

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/29/2016
ms.author: anhoh

---
# Självstudiekurs om NoSQL: skapa en DocumentDB C#-konsolapp
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> 
> 

Välkommen till NoSQL-självstudier för Azure DocumentDB .NET SDK! Efter att ha hämtat QuickStart-projektet eller då självstudierna slutförts, har du ett konsolprogram som skapar och frågar DocumentDB-resurser.

* **[QuickStart](#quickstart)**: Hämta exempelprojektet, lägg till anslutningsinformation och få igång en DocumentDB-app på färre än 5 minuter.
* **[Självstudier](#tutorial)**: Skapa QuickStart-appen på bara 30 minuter.

## Krav
* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
* [Visual Studio 2013 eller Visual Studio 2015](http://www.visualstudio.com/).
* .NET Framework 4.6

## QuickStart
1. Ladda ned exempelprojektet från [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started-quickstart/archive/master.zip).
2. Använd Azure-portalen för att [skapa ett DocumentDB-konto](documentdb-create-account.md).
3. Ersätt EndpointUri- och PrimaryKey-värden i App.config-filen med värden som hämtats från [Azure-portalen](https://portal.azure.com/), genom att navigera till bladet **DocumentDB (NoSQL)**, klicka på **Kontonamn** och klicka sedan på **Nycklar** på resursmenyn.
    ![Skärmbild av EndpointUri- och PrimaryKey-värdet som ska ersättas i App.config](./media/documentdb-get-started-quickstart/nosql-tutorial-documentdb-keys.png)
4. Bygga projektet. Fönstret visar nya resurser som skapats, frågats och sedan rensats.
   
    ![Skärmbild av konsolutdata](./media/documentdb-get-started-quickstart/nosql-tutorial-documentdb-console-output.png)

## <a id="tutorial"></a>Självstudier
De här självstudierna går igenom hur du skapar en DocumentDB-databas, en DocumentDB-samling och JSON-dokument. Du måste sedan fråga samlingen, rensa och ta bort databasen. Den här kursen skapar samma projekt som QuickStart-projektet, men du skapar det inkrementellt och får förklaring om koden som du lägger till i projektet.

## Steg 1: Skapa ett DocumentDB-konto
Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera en lösning i Visual Studio](#SetupVS).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a>Steg 2: Konfigurera en lösning i Visual Studio
1. Öppna **Visual Studio 2015** på datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I dialogen **Nytt projekt** väljer du **Mallar** / **Visual C#** / **Konsolapp**. Namnge projektet och klicka sedan på **OK**.
   ![Skärmdump som visar fönstret Nytt projekt](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. I **Solution Explorer** högerklickar du på den nya konsolappen, som finns under din Visual Studio-lösning.
5. Utan att lämna menyn klickar du på **Hantera NuGet-paket ...**
   ![Skärmdump som visar högerklicksmenyn för projektet](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. På fliken **Nuget** klickar du på **Bläddra** och skriver **azure documentdb** i sökrutan.
7. Leta reda på **Microsoft.Azure.DocumentDB** i resultatet och klicka på **Installera**.
   Paket-ID för klientbiblioteket i DocumentDB är [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)
   ![Skärmdump som visar Nuget-menyn som används för att hitta DocumentDB Client SDK](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

Bra! Konfigurationen är slutförd, så vi kan börja skriva kod. Det finns ett färdigt kodprojekt för den här självstudiekursen i [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a id="Connect"></a>Steg 3: Anslut till ett DocumentDB-konto
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
> Var noga med att lägga till ovanstående beroenden så att du kan slutföra den här självstudiekursen om NoSQL.
> 
> 

Lägg till nedanstående två konstanter och *klientvariabeln* under den offentliga klassen *Program*.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUri = "<your endpoint URI>";
        private const string PrimaryKey = "<your key>";
        private DocumentClient client;

Öppna sedan [Azure Portal](https://portal.azure.com) och hämta din URI och primärnyckel. URI och primärnyckel från DocumentDB är nödvändiga för att appen ska veta var du vill ha anslutningen och för att DocumentDB ska lita på appens anslutning.

Navigera till ditt DocumentDB-konto i Azure Portal och klicka sedan på **Nycklar**.

Kopiera URI från portalen och klistra in den i `<your endpoint URI>` i filen program.cs. Kopiera sedan PRIMÄRNYCKELN från portalen och klistra in den i `<your key>`.

![Skärmdump av Azure Portal som används i självstudiekursen om NoSQL för att skapa en C#-konsolapp. Visar DocumentDB-konto med hubben AKTIV markerad, knappen NYCKLAR markerad i bladet DocumentDB-konto och värdena URI, PRIMÄRNYCKEL och SEKUNDÄRNYCKEL markerade i bladet Nycklar][keys]

Vi börjar med att skapa en ny instans av **DocumentClient**.

Under metoden **Main** lägger du till den här nya asynkrona aktiviteten kallad **GetStartedDemo**, som instantierar vår nya **dokumentklient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
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

Kör appen genom att trycka på **F5**.

Grattis! Du har anslutit till ett DocumentDB-konto! Nu ska vi gå igenom hur man arbetar med DocumentDB-resurser.  

## Steg 4: Skapa en databas
Lägg till en hjälpmetod för skrivning till konsolen innan du lägger till kod som skapar en databas.

Kopiera och klistra in metoden **WriteToConsoleAndPromptToContinue** under metoden **GetStartedDemo**.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Du kan skapa DocumentDB-[databasen](documentdb-resources.md#databases) med hjälp av metoden [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) för klassen **DocumentClient**. En databas är en logisk behållare för JSON-dokumentlagring, partitionerad över samlingarna.

Kopiera och klistra in metoden **CreateDatabaseIfNotExists** under metoden **WriteToConsoleAndPromptToContinue**.

    // ADD THIS PART TO YOUR CODE
    private async Task CreateDatabaseIfNotExists(string databaseName)
    {
            // Check to verify a database with the id=FamilyDB does not exist
            try
            {
                    await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(databaseName));
                    this.WriteToConsoleAndPromptToContinue("Found {0}", databaseName);
            }
            catch (DocumentClientException de)
            {
                    // If the database does not exist, create a new database
                    if (de.StatusCode == HttpStatusCode.NotFound)
                    {
                            await this.client.CreateDatabaseAsync(new Database { Id = databaseName });
                            this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
                    }
                    else
                    {
                            throw;
                    }
            }
    }

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under den skapade klienten. Då skapas en databas med namnet *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseIfNotExists("FamilyDB_va");

Kör appen genom att trycka på **F5**.

Grattis! Du har skapat en DocumentDB-databas.  

## <a id="CreateColl"></a>Steg 5: Skapa en samling
> [!WARNING]
> **CreateDocumentCollectionAsync** skapar en ny samling med reserverat dataflöde, vilket får konsekvenser för priset. Mer information finns på vår [sida med priser](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Du kan skapa en [samling](documentdb-resources.md#collections) med metoden [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) för klassen **DocumentClient**. En samling är en behållare för JSON-dokument och associerad JavaScript-applogik.

Kopiera och klistra in metoden **CreateDocumentCollectionIfNotExists** under metoden **CreateDatabaseIfNotExists**.

    // ADD THIS PART TO YOUR CODE
    private async Task CreateDocumentCollectionIfNotExists(string databaseName, string collectionName)
    {
        try
        {
            await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
            this.WriteToConsoleAndPromptToContinue("Found {0}", collectionName);
        }
        catch (DocumentClientException de)
        {
            // If the document collection does not exist, create a new collection
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                DocumentCollection collectionInfo = new DocumentCollection();
                collectionInfo.Id = collectionName;

                // Configure collections for maximum query flexibility including string range queries.
                collectionInfo.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

                // Here we create a collection with 400 RU/s.
                await this.client.CreateDocumentCollectionAsync(
                    UriFactory.CreateDatabaseUri(databaseName),
                    collectionInfo,
                    new RequestOptions { OfferThroughput = 400 });

                this.WriteToConsoleAndPromptToContinue("Created {0}", collectionName);
            }
            else
            {
                throw;
            }
        }
    }

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under koden som skapade databasen. Då skapas en dokumentsamling som heter *FamilyCollection_va*.

        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

        // ADD THIS PART TO YOUR CODE
        await this.CreateDocumentCollectionIfNotExists("FamilyDB_va", "FamilyCollection_va");

Kör appen genom att trycka på **F5**.

Grattis! Du har skapat en DocumentDB-dokumentsamling.  

## <a id="CreateDoc"></a>Steg 6: Skapa JSON-dokument
Du kan skapa [dokument](documentdb-resources.md#documents) med metoden [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) för klassen **dokumentklient**. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Vi kan nu infoga ett eller flera dokument. Om du redan har data som du vill lagra i databasen kan du använda [datamigreringsverktyget](documentdb-import-data.md) för DocumentDB.

Först måste vi skapa klassen **Familj** som ska representera objekt som lagras i DocumentDB i det här exemplet. Vi kommer även att skapa underklasserna **Förälder**, **Barn**, **Husdjur** och **Adress** som används inom **Familj**. Observera att dokument måste ha en **id**-egenskap serialiserad som **id** i JSON. Skapa dessa klasser genom att lägga till nedanstående interna undergrupper efter metoden **GetStartedDemo**.

Kopiera och klistra in klasserna **Familj**, **Förälder**, **Barn**, **Husdjur** och **Adress** under metoden **WriteToConsoleAndPromptToContinue**.

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

Kopiera och klistra in metoden **CreateFamilyDocumentIfNotExists** under metoden **CreateDocumentCollectionIfNotExists**.

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

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under koden som skapade dokumentsamlingen.

    await this.CreateDatabaseIfNotExists("FamilyDB_va");

    await this.CreateDocumentCollectionIfNotExists("FamilyDB_va", "FamilyCollection_va");

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", andersenFamily);

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

Kör appen genom att trycka på **F5**.

Grattis! Du har skapat två DocumentDB-dokument.  

![Diagram som illustrerar den hierarkiska relationen mellan kontot, onlinedatabasen, samlingen och dokumenten som används i NoSQL-självstudiekursen för att skapa en C#-konsolapp](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Steg 7: Frågor till DocumentDB-resurser
DocumentDB stöder omfattande [frågor](documentdb-sql-query.md) mot JSON-dokument som lagras i varje samling.  Nedanstående exempelkod visar olika frågor – med både DocumentDB SQL-syntax och LINQ – som du kan köra mot dokumenten som infogades i föregående steg.

Kopiera och klistra in metoden **ExecuteSimpleQuery** under metoden **CreateFamilyDocumentIfNotExists**.

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

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under koden som skapade det andra dokumentet.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

Kör appen genom att trycka på **F5**.

Grattis! Du har skickat en förfrågan till en DocumentDB-samling.

Nedanstående diagram illustrerar hur DocumentDB SQL-frågesyntaxen anropas mot samlingen du skapade. Samma logik som gäller även för LINQ-frågan.

![Diagram som illustrerar omfånget och innebörden av frågan som används i NoSQL-självstudiekursen för att skapa en C#-konsolapp](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

Nyckelordet [FRÅN](documentdb-sql-query.md#from-clause) är valfritt i frågan eftersom DocumentDB-frågor redan är begränsade till en enda samling. ”FROM Families f” kan därför bytas mot ”FROM root r” eller annat valfritt variabelnamn som du väljer. DocumentDB drar slutsatsen att familjer, roten eller variabelnamnet som du har valt som standard refererar till den aktuella samlingen.

## <a id="ReplaceDocument"></a>Steg 8: Ersätta JSON-dokument
DocumentDB har stöd för att ersätta JSON-dokument.  

Kopiera och klistra in metoden **ReplaceFamilyDocument** under metoden **ExecuteSimpleQuery**.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
        try
        {
            await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
            this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under frågekörningskoden. När du ersätter dokumentet körs samma fråga igen för att visa det ändrade dokumentet.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

Kör appen genom att trycka på **F5**.

Grattis! Du har ersatt ett DocumentDB-dokument.

## <a id="DeleteDocument"></a>Steg 9: Ta bort JSON-dokument
DocumentDB har stöd för att ta bort JSON-dokument.  

Kopiera och klistra in metoden **DeleteFamilyDocument** under metoden **ReplaceFamilyDocument**.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        try
        {
            await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
            Console.WriteLine("Deleted Family {0}", documentName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under koden för den andra frågekörningen.

    await this.ReplaceFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1");

Kör appen genom att trycka på **F5**.

Grattis! Du har tagit bort ett DocumentDB-dokument.

## <a id="DeleteDatabase"></a>Steg 10: Ta bort databasen
Om du tar bort databasen som skapats försvinner databasen och alla underordnade resurser (t.ex. samlingar och dokument).

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** under dokumentborttagningskoden om du vill ta bort hela databasen och alla underordnade resurser.

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

    await this.DeleteFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_va"));

Kör appen genom att trycka på **F5**.

Grattis! Du har tagit bort en DocumentDB-databas.

## <a id="Run"></a>Steg 11: Kör C#-konsolappen i sin helhet!
Tryck på F5 i Visual Studio för att bygga appen i felsökningsläge.

Du bör se Get Started-appens utdata. Dessa utdata visar resultaten för de frågor som vi har lagt till och bör motsvara exempeltexten nedan.

    Created FamilyDB_va
    Press any key to continue ...
    Created FamilyCollection_va
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

Grattis! Du har slutfört den här självstudiekursen om NoSQL och har en fungerande C#-konsolapp!

## Nästa steg
* Vill du ha en mer komplicerad självstudiekurs om ASP.NET MVC NoSQL? Se [Skapa en webbapp med ASP.NET MVC via DocumentDB](documentdb-dotnet-application.md).
* Vill du utföra skalnings- och prestandatester med DocumentDB? Se [Prestanda- och skalningstester med Azure DocumentDB](documentdb-performance-testing.md)
* Mer information om hur du [övervakar ett DocumentDB-konto](documentdb-monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).
* Mer information om programmeringsmiljön finns i avsnittet Utveckla på [dokumentationssidan för DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started-quickstart/nosql-tutorial-keys.png




<!--HONumber=sep16_HO1-->


