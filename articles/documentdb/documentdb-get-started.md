---
title: "Självstudiekurs om NoSQL: DocumentDB .NET SDK | Microsoft Docs"
description: "En självstudiekurs om NoSQL som skapar en onlinedatabas och en C#-konsolapp med .NET DocumentDB SDK. DocumentDB är en NoSQL-databas för JSON."
keywords: "självstudier för nosql, onlinedatabas, c#-konsolprogram"
services: documentdb
documentationcenter: .net
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/19/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 648aa01beaffccf69250f1028e27bc13ed70837c
ms.lasthandoff: 03/28/2017


---
# <a name="nosql-tutorial-build-a-documentdb-c-console-application"></a>Självstudiekurs om NoSQL: skapa en DocumentDB C#-konsolapp
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js för MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Välkommen till NoSQL-självstudier för Azure DocumentDB .NET SDK! När du har genomfört den här självstudiekursen har du en konsolapp som skapar och skickar frågor till DocumentDB-resurser.

Vi tar upp följande:

* Skapa och ansluta till ett DocumentDB-konto
* Konfigurera en Visual Studio-lösning
* Skapa en onlinedatabas
* Skapa en samling
* Skapa JSON-dokument
* Skicka frågor till samlingen
* Ersätta ett dokument
* Ta bort ett dokument
* Ta bort databasen

Har du inte tid? Oroa dig inte! Den kompletta lösningen finns på [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). En snabbguide finns i avsnittet [Hämta den fullständiga lösningen till NoSQL-självstudiekursen](#GetSolution).

Ge oss sedan feedback med röstningsknapparna högst uppe och nere på den här sidan. Om du vill att vi ska kontakta dig direkt kan du skriva din e-postadress i kommentaren.

Nu sätter vi igång!

## <a name="prerequisites"></a>Krav
Se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 
    * Du kan också använda [Azure DocumentDB-emulatorn](documentdb-nosql-local-emulator.md) för den här självstudien.
* [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).

## <a name="step-1-create-a-documentdb-account"></a>Steg 1: Skapa ett DocumentDB-konto
Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera en lösning i Visual Studio](#SetupVS). Om du använder DocumentDB-emulatorn följer du stegen i artikeln om [Azure DocumentDB-emulatorn](documentdb-nosql-local-emulator.md) för att konfigurera emulatorn och gå vidare med att [konfigurera din Visual Studio-lösning](#SetupVS).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a>Steg 2: Konfigurera en lösning i Visual Studio
1. Öppna **Visual Studio 2015** på datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I dialogen **Nytt projekt** väljer du **Mallar** / **Visual C#** / **Konsolapp**. Namnge projektet och klicka sedan på **OK**.
   ![Skärmdump som visar fönstret Nytt projekt](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. I **Solution Explorer** högerklickar du på den nya konsolappen, som finns under din Visual Studio-lösning, och klickar sedan på **Hantera NuGet-paket ...**
    
    ![Skärmdump som visar den högerklickade menyn för projektet](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
5. På fliken **Nuget** klickar du på **Bläddra** och skriver **azure documentdb** i sökrutan.
6. Leta reda på **Microsoft.Azure.DocumentDB** i resultatet och klicka på **Installera**.
   Paket-ID:t för DocumentDB-klientbiblioteket är [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Skärmdump som visar Nuget-menyn för att söka efter DocumentDB Client-SDK](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

    Om du får meddelanden om att granska ändringar i lösningen klickar du på **OK**. Om du får ett meddelande om godkännande av licens klickar du på **Jag godkänner**.

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
        private const string EndpointUrl = "<your endpoint URL>";
        private const string PrimaryKey = "<your primary key>";
        private DocumentClient client;

Gå sedan tillbaka till [Azure Portal](https://portal.azure.com) för att hämta slutpunkts-URL:en och primärnyckeln. Slutpunkts-URL:en och primärnyckeln behövs för att appen ska veta var ditt program ska ansluta till och för att DocumentDB ska lita på ditt programs anslutning.

Navigera till ditt DocumentDB-konto i Azure Portal och klicka sedan på **Nycklar**.

Kopiera URI från portalen och klistra in den i `<your endpoint URL>` i filen program.cs. Kopiera sedan PRIMÄRNYCKELN från portalen och klistra in den i `<your primary key>`.

![Skärmdump av Azure Portal som används i självstudiekursen om NoSQL för att skapa en C#-konsolapp. Visar DocumentDB-konto med hubben AKTIV markerad, knappen NYCKLAR markerad i bladet DocumentDB-konto och värdena URI, PRIMÄRNYCKEL och SEKUNDÄRNYCKEL markerade i bladet Nycklar][keys]

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

Grattis! Du har anslutit till ett DocumentDB-konto! Nu ska vi gå igenom hur man arbetar med DocumentDB-resurser.  

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

Du kan skapa DocumentDB-[databasen](documentdb-resources.md#databases) med hjälp av metoden [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) för klassen **DocumentClient**. En databas är en logisk behållare för JSON-dokumentlagring, partitionerad över samlingarna.

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** efter den skapade klienten. Då skapas en databas med namnet *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

Kör appen genom att trycka på **F5**.

Grattis! Du har skapat en DocumentDB-databas.  

## <a id="CreateColl"></a>Steg 5: Skapa en samling
> [!WARNING]
> **CreateDocumentCollectionIfNotExistsAsync** skapar en ny samling med reserverat dataflöde, vilket får konsekvenser för priset. Mer information finns på vår [sida med priser](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Du kan skapa en [samling](documentdb-resources.md#collections) med metoden [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) för klassen **DocumentClient**. En samling är en behållare för JSON-dokument och associerad JavaScript-applogik.

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** efter koden som skapade databasen. Då skapas en dokumentsamling som heter *FamilyCollection*.

        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

        // ADD THIS PART TO YOUR CODE
         await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });

Kör appen genom att trycka på **F5**.

Grattis! Du har skapat en DocumentDB-dokumentsamling.  

## <a id="CreateDoc"></a>Steg 6: Skapa JSON-dokument
Du kan skapa [dokument](documentdb-resources.md#documents) med metoden [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) för klassen **dokumentklient**. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Vi kan nu infoga ett eller flera dokument. Om du redan har data som du vill lagra i databasen kan du använda [datamigreringsverktyget](documentdb-import-data.md) för DocumentDB för att importera datan till en databas.

Först måste vi skapa klassen **Familj** som ska representera objekt som lagras i DocumentDB i det här exemplet. Vi kommer även att skapa underklasserna **Förälder**, **Barn**, **Husdjur** och **Adress** som används inom **Familj**. Observera att dokument måste ha en **id**-egenskap serialiserad som **id** i JSON. Skapa dessa klasser genom att lägga till nedanstående interna undergrupper efter metoden **GetStartedDemo**.

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

Grattis! Du har skapat två DocumentDB-dokument.  

![Diagram som illustrerar den hierarkiska relationen mellan kontot, onlinedatabasen, samlingen och dokumenten som används i NoSQL-självstudiekursen för att skapa en C#-konsolapp](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Steg 7: Skicka frågor till DocumentDB-resurser
DocumentDB stöder omfattande [frågor](documentdb-sql-query.md) mot JSON-dokument som lagras i varje samling.  Nedanstående exempelkod visar olika frågor – med både DocumentDB SQL-syntax och LINQ – som du kan köra mot dokumenten som infogades i föregående steg.

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

Grattis! Du har skickat en förfrågan till en DocumentDB-samling.

Nedanstående diagram illustrerar hur DocumentDB SQL-frågesyntaxen anropas mot samlingen du skapade. Samma logik som gäller även för LINQ-frågan.

![Diagram som illustrerar omfånget och innebörden av frågan som används i NoSQL-självstudiekursen för att skapa en C#-konsolapp](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

Nyckelordet [FROM](documentdb-sql-query.md#FromClause) är valfritt i frågan eftersom DocumentDB-frågor redan är begränsade till en enda samling. ”FROM Families f” kan därför bytas mot ”FROM root r” eller annat valfritt variabelnamn som du väljer. DocumentDB drar slutsatsen att familjer, roten eller variabelnamnet som du har valt som standard refererar till den aktuella samlingen.

## <a id="ReplaceDocument"></a>Steg 8: Ersätta JSON-dokument
DocumentDB har stöd för att ersätta JSON-dokument.  

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

Grattis! Du har ersatt ett DocumentDB-dokument.

## <a id="DeleteDocument"></a>Steg 9: Ta bort JSON-dokument
DocumentDB har stöd för att ta bort JSON-dokument.  

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

Grattis! Du har tagit bort ett DocumentDB-dokument.

## <a id="DeleteDatabase"></a>Steg 10: Ta bort databasen
Om du tar bort databasen du skapade försvinner databasen och alla underordnade resurser (t.ex. samlingar och dokument).

Kopiera och klistra in nedanstående kod till metoden **GetStartedDemo** efter dokumentborttagningskoden om du vill ta bort hela databasen och alla underordnade resurser.

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

Kör appen genom att trycka på **F5**.

Grattis! Du har tagit bort en DocumentDB-databas.

## <a id="Run"></a>Steg 11: Kör C#-konsolappen i sin helhet!
Tryck på F5 i Visual Studio för att bygga appen i felsökningsläge.

Du bör nu se utdata från din kom-igång-app. Dessa utdata visar resultaten för de frågor som vi har lagt till och bör motsvara exempeltexten nedan.

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

Grattis! Du har slutfört den här självstudiekursen om NoSQL och har en fungerande C#-konsolapp!

## <a id="GetSolution"></a>Hämta den fullständiga lösningen till NoSQL-självstudiekursen
Om du inte har tid att slutföra stegen i den här självstudien eller bara vill ladda ned kodexemplen kan du hämta den från [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

För att bygga GetStarted-lösningen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett [DocumentDB-konto][documentdb-create-account].
* [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started)-lösningen som finns på GitHub.

Om du vill återställa referenser till .NET DocumentDB SDK i Visual Studio högerklickar du på **GetStarted**-lösningen i Solution Explorer och klickar sedan på **Aktivera NuGet-paketåterställning**. I filen App.config uppdaterar du sedan värdena EndpointUrl och AuthorizationKey enligt beskrivningen i [Ansluta till ett DocumentDB-konto](#Connect).

Då är det bara att bygga den, så är du på väg!


## <a name="next-steps"></a>Nästa steg
* Vill du ha en mer komplicerad självstudiekurs om ASP.NET MVC NoSQL? Se [Skapa en webbapp med ASP.NET MVC via DocumentDB](documentdb-dotnet-application.md).
* Vill du utföra skalnings- och prestandatester med DocumentDB? Se [Prestanda- och skalningstester med Azure DocumentDB](documentdb-performance-testing.md)
* Mer information om hur du [övervakar ett DocumentDB-konto](documentdb-monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).
* Mer information om programmeringsmiljön finns i avsnittet Utveckla på [dokumentationssidan för DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

