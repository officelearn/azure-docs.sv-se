---
title: 'Självstudiekurs om ASP.NET MVC för Azure Cosmos DB: Utveckling av webbprogram | Microsoft Docs'
description: Självstudiekurs om ASP.NET MVC som beskriver hur du skapar ett MVC-webbprogram med hjälp av Azure Cosmos DB. Du kommer att lagra JSON- och åtkomstdata från en ”att göra”-app på Azure Websites – stegvis självstudiekurs om ASP NET MVC.
keywords: asp.net mvc självstudier, webbprogramsutveckling, mvc-webbprogram, asp net mvc självstudier steg för steg
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.workload: azure-vs
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: sngun
ms.custom: devcenter, vs-azure
ms.openlocfilehash: 6fbe043e0232701d2aabcbc09606864b5cc27450
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702518"
---
# <a name="_Toc395809351"></a>Självstudiekurs om ASP.NET MVC: Utveckling av webbprogram med Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Node.js- v2](sql-api-nodejs-application-preview.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Den här artikeln innehåller en heltäckande beskrivning av hur du skapar en ”att göra”-app med Azure Cosmos DB och visar hur du effektivt kan lagra och skicka frågor mot JSON-dokument med Azure Cosmos DB. Uppgifterna lagras som JSON-dokument i Azure Cosmos DB.

![Skärmdump som visar MVC-webbappen med ”att göra”-listan som skapas i denna självstudiekurs – stegvis självstudiekurs om ASP NET MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Den här genomgången beskriver hur du använder tjänsten Azure Cosmos DB för att lagra och komma åt data från ett ASP.NET MVC-webbprogram i Azure. Om du letar efter en självstudiekurs som bara fokuserar på Azure Cosmos DB och inte ASP.NET MVC-komponenterna läser du [Skapa ett Azure Cosmos DB C#-konsolprogram](sql-api-get-started.md).

> [!TIP]
> Den här självstudiekursen förutsätter att du har tidigare erfarenhet av MVC i ASP.NET och Azure Websites. Om du inte har använt ASP.NET eller [verktygen som krävs](#_Toc395637760) tidigare, rekommenderar vi att du hämtar det fullständiga exempelprojektet från [GitHub][GitHub] och följer anvisningarna i det exemplet. När du har byggt exempelprojektet kan du läsa den här artikeln för information om koden i projektets sammanhang.
> 
> 

## <a name="_Toc395637760"></a>Förhandskrav för den här databas-självstudien
Innan du följer anvisningarna i den här artikeln bör du se till att du har följande:

* Ett aktivt Azure-konto.  Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* Microsoft Azure SDK för .NET för Visual Studio 2017, som är tillgängligt via Visual Studio-installationsprogrammet.

Alla skärmdumpar i den här artikeln kommer från Microsoft Visual Studio Community 2017. Om ditt system är konfigurerat med en annan version kan det hända att skärmbilder och alternativ inte ser riktigt likadana ut, men om ovanstående krav är uppfyllda ska lösningen fungera.

## <a name="_Toc395637761"></a>Steg 1: Skapa ett Azure Cosmos DB-databaskonto
Vi ska börja med att skapa ett Azure Cosmos DB-konto. Om du redan har ett SQL-konto för Azure Cosmos DB eller om du använder Azure Cosmos DB-emulatorn för den här kursen kan du gå vidare till [Skapa ett nytt ASP.NET MVC-program](#_Toc395637762).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
Vi kommer att gå igenom hur du skapar en ny MVC-app från grunden i ASP.NET. 

## <a name="_Toc395637762"></a>Steg 2: Skapa ett nytt ASP.NET MVC-program

1. I Visual Studio klickar du på menyn **Arkiv**, pekar på **Nytt** och klickar sedan på **Projekt**. Dialogrutan **Nytt projekt** visas.

2. I panelen **Projekttyper** expanderar du **Mallar**, **Visual C#**, **Webb** och väljer sedan **ASP.NET webbtillämpningsprogram**.

      ![Skärmdump som visar dialogrutan Nytt projekt med projekttypen ASP.NET-webbtillämpningsprogram markerad](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. I rutan **Namn** skriver du namnet på projektet. I den här självstudiekursen används namnet ”todo”. Om du väljer att använda något annat än det måste du ändra de angivna kodexemplen så att de använder det namn du gav din app på alla ställen där namnområdet todo nämns. 
4. Klicka på **Bläddra** och navigera till mappen där du vill skapa projektet och klicka sedan på **OK**.
   
      Dialogrutan **New ASP.NET Web Application** (Nytt ASP.NET-webbprogram) visas.
   
    ![Skärmbild som visar dialogrutan New ASP.NET Web Application (Nytt ASP.NET-webbprogram) med MVC-programmallen markerad](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. Välj **MVC** i mallpanelen.

6. Klicka på **OK** och låt Visual Studio generera ett kodskelett för den tomma ASP.NET MVC-mallen. 

          
7. När Visual Studio har skapat den formaterade MVC-appen har du en tom ASP.NET-app som du kan köra lokalt.
   
    Vi kör inte projektet lokalt nu eftersom du säkert har sett ASP.NET-appen ”Hello World”. Vi fortsätter i stället med att lägga till Azure Cosmos DB till projektet och skapa vårt program.

## <a name="_Toc395637767"></a>Steg 3: Lägga till Azure Cosmos DB till MVC-webbprogramprojektet
Nu har vi i princip de ASP.NET MVC-grunder som vi behöver för lösningen och det är dags att arbeta med det verkliga syftet med den här självstudiekursen, dvs. att lägga till Azure Cosmos DB till vårt MVC-webbprogram.

1. Azure Cosmos DB .NET SDK paketeras och distribueras som ett NuGet-paket. Hämta NuGet-paketet i Visual Studio med hjälp av NuGet-pakethanteraren i Visual Studio genom att högerklicka på projektet i **Solution Explorer** och sedan klicka på **Hantera NuGet-paket**.
   
    ![Skärmdump som visar snabbemenyalternativen för webbprogramprojektet i Solution Explorer, med Hantera NuGet-paket markerat.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    Dialogrutan **Hantera NuGet-paket** öppnas.
2. I NuGet-rutan **Bläddra** klickar du på ***Azure DocumentDB***. (Paketnamnet har inte uppdaterats till Azure Cosmos DB.)
   
    Installera paketet **Microsoft.Azure.DocumentDB från Microsoft** från listan med resultat. När du gör det hämtas och installeras Azure Cosmos DB-paketet och alla beroenden, t.ex. Newtonsoft.Json. Klicka på **OK** i fönstret **Förhandsgranskning** och **Jag godkänner** i fönstret **Godkännande av licens** för att slutföra installationen.
   
    ![Skärmdump som visar fönstret Hantera NuGet-paket, med Microsoft Azure Cosmos DB-klientbiblioteket markerat](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      Du kan också installera paketen med Pakethanterarkonsolen. Om du vill göra det klickar du på menyn **Verktyg**, **NuGet-pakethanteraren** och sedan på **Pakethanterarkonsolen**. Skriv följande i prompten:
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. När paketet har installerats bör din Visual Studio-lösning likna nedanstående, med två nya referenser: Microsoft.Azure.Documents.Client och Newtonsoft.Json.
   
    ![Skärmdump av de två referenser som lagts till JSON-dataprojektet i Solution Explorer](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>Steg 4: Ställ in ASP.NET MVC-programmet
Nu ska vi lägga till modeller, vyer och styrenheter till MVC-appen:

* [Lägga till en modell](#_Toc395637764).
* [Lägga till en styrenhet](#_Toc395637765).
* [Lägga till vyer](#_Toc395637766).

### <a name="_Toc395637764"></a>Lägg till en JSON-datamodell
Vi börjar med att skapa **M** i MVC, dvs. modellen. 

1. I **Solution Explorer** högerklickar du på mappen **Modeller**. Klicka sedan på **Lägg till** och på **Klass**.
   
      Dialogrutan **Lägg till nytt objekt** visas.
2. Namnge den nya klassen **Objekt.cs** och klicka på **Lägg till**. 
3. I den nya **Item.cs**-filen lägger du till nedanstående efter den senaste *using-satsen*.
   
        using Newtonsoft.Json;
4. Ersätt nu den här koden 
   
        public class Item
        {
        }
   
    med nedanstående kod.
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Alla data i Azure Cosmos DB skickas via anslutningen och lagras som JSON. Du kan styra hur objekten serialiseras/deserialiseras av JSON.NET genom attributet **JsonProperty** så som visas i den **Objekt**-klass vi just skapade. Du **måste** inte göra det, men jag vill vara säker på att mina egenskaper följer namnkonventionen med kamelnotation för JSON. 
   
    Du kan inte bara styra egenskapsnamnets format när det matas in i JSON, utan helt byta namn på dina .NET-egenskaper så som jag gjorde med egenskapen **Beskrivning**. 

### <a name="_Toc395637765"></a>Lägg till en controller
Nu har vi tagit hand om **M** och det är dags för vårt **C** i MVC, dvs. en styrenhetsklass (controller).

1. I **Solution Explorer** högerklickar du på mappen **Styrenheter**. Klicka sedan på **Lägg till** och på **Styrenhet**.
   
    Dialogrutan **Lägg till kodskelett** visas.
2. Välj **MVC 5 styrenhet – tom** och klicka sedan på **Lägg till**.
   
    ![Skärmdump av dialogrutan Lägg till kodskelett med alternativet MVC 5 styrenhet – tom markerat](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. Ge din nya styrenhet namnet **Objektstyrning.**
   
    ![Skärmdump av dialogrutan Lägg till styrenhet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    När filen har skapats bör din Visual Studio-lösning påminna om nedanstående, med den nya ItemController.cs-filen i **Solution Explorer**. Även den nya Item.cs-filen som du skapade tidigare visas.
   
    ![Skärmdump av lösningen i Visual Studio – Solution Explorer med den nya ItemController.cs-filen och Item.cs-filen markerade](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    Du kan stänga ItemController.cs, vi återkommer till den senare. 

### <a name="_Toc395637766"></a>Lägg till vyer
Nu skapar vi vårt **V** i MVC, dvs. vyerna:

* [Lägga till Vyn Objektindex](#AddItemIndexView).
* [Lägga till vyn Nytt objekt](#AddNewIndexView).
* [Lägga till vyn Redigera objekt](#_Toc395888515).

#### <a name="AddItemIndexView"></a>Lägg till en objektindexvy
1. I **Solution Explorer** expanderar du mappen **Vyer** och högerklickar på den tomma **objektmappen** som Visual Studio skapade när du lade till **ItemController** tidigare. Klicka på **Lägg till** och sedan på **Vy**.
   
    ![Skärmdump av Solution Explorer som visar den objektmapp som Visual Studio skapade med kommandot Lägg till vy markerat](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. Gör följande i dialogrutan **Lägg till vy**:
   
   * I rutan **Vynamn** skriver du ***Index***.
   * I rutan**Mall** väljer du ***Lista***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
     
   ![Skärmdump som visar dialogrutan Lägg till vy](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. När du har angett samtliga dessa värden klickar du på **Lägg till**  och låter Visual Studio skapa en ny mallvy. När det är klart öppnas cshtml-filen som skapades. Du kan stänga filen i Visual Studio eftersom vi återkommer till den senare.

#### <a name="AddNewIndexView"></a>Lägg till en nytt objektsvy
På liknande sätt som vi skapade vyn **Objektindex** ska vi nu skapa en ny vy där  nya **objekt** kan skapas.

1. I **Solution Explorer** högerklickar du på mappen **Objekt** en gång till. Klicka sedan på **Lägg till** och på **Vy**.
2. Gör följande i dialogrutan **Lägg till vy**:
   
   * I rutan **Vynamn** skriver du ***Skapa***.
   * I rutan**Mall** väljer du ***Skapa***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
   * Klicka på **Lägg till**.
   
#### <a name="_Toc395888515"></a>Lägg till en redigera objektsvy
Lägg slutligen till en vy för redigering av ett **objekt**, på samma sätt som tidigare.

1. I **Solution Explorer** högerklickar du på mappen **Objekt** en gång till. Klicka sedan på **Lägg till** och på **Vy**.
2. Gör följande i dialogrutan **Lägg till vy**:
   
   * I rutan **Vynamn** skriver du ***Redigera***.
   * I rutan**Mall** väljer du ***Redigera***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
   * Klicka på **Lägg till**.

När det är klart stänger du alla cshtml-dokument i Visual Studio eftersom vi återkommer till dem senare.

## <a name="_Toc395637769"></a>Steg 5: Lägga till kod för Azure Cosmos DB
Nu när standarddelarna i MVC är klara är det dags att lägga till koden för Azure Cosmos DB. 

I det här avsnittet vi lägger till kod som hanterar följande:

* [Lista ofullständiga objekt](#_Toc395637770).
* [Lägga till objekt](#_Toc395637771).
* [Redigera objekt](#_Toc395637772).

### <a name="_Toc395637770"></a>Lista ofullständiga objekt i ditt MVC-webbprogram
Det första du gör här är att lägga till en klass som innehåller all logik för att ansluta till och använda Azure Cosmos DB. I den här självstudiekursen kapslar vi in all denna logik i en centrallagerklass kallad DocumentDBRepository. 

1. Högerklicka på projektet i **Solution Explorer**. Klicka sedan på **Lägg till** och på **Klass**. Ge den nya klassen namnet **DocumentDBRepository** och klicka på **Lägg till**.
2. I den nyligen skapade **DocumentDBRepository**-klassen lägger du till nedanstående *using-satser* ovanför deklarationen för *namnområde*
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net;
        
    Ersätt nu den här koden 
   
        public class DocumentDBRepository
        {
        }
   
    med nedanstående kod.
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. Vi läser in vissa värden från konfigurationen, så öppna filen **Web.config** för appen och lägg till nedanstående rader under avsnittet `<AppSettings>`.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Uppdatera nu värden för *slutpunkt* och *auktoriseringsnyckel* via bladet Nycklar i Azure Portal. Använd den **URI** som finns i bladet Nycklar som värde för slutpunkt och använd den **PRIMÄRNYCKEL** eller **SEKUNDÄRNYCKEL** som finns i bladet Nycklar som värde för inställningen Auktoriseringsnyckel.

    Nu har kopplingarna till Azure Cosmos DB-databasen skapats och vi kan lägga till vår applogik.

1. Det första vi vill kunna göra med en app med en ”att göra”-lista är att visa de uppgifter som inte är slutförda.  Kopiera och klistra in nedanstående kodutdrag var som helst inom klassen **DocumentDBRepository**.
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. Öppna **ItemController** som vi lade till tidigare och lägg till nedanstående *using-sats* ovanför deklarationen namnområde.
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    Om du inte har gett ditt projekt namnet ”todo” behöver du uppdatera med hjälp av ”todo.Models” så att namnet på ditt projekt återspeglas.
   
    Ersätt nu den här koden
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    med nedanstående kod.
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. Öppna **Global.asax.cs** och lägg till nedanstående rad i metoden **Application_Start** 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

Vid det här laget bör din lösning kunna byggas utan fel.

Om du skulle köra appen nu skulle du öppna **HomeController** och vyn **Index** från den styrenheten. Det här är standardinställningen för MVC-mallprojektet vi valde i början, men vi vill inte ha den! Vi ändrar beteendet genom att dirigera om den här MVC-appen.

Öppna ***App\_Start\RouteConfig.cs***, leta upp den rad som börjar med ”defaults:” och ändra den så som följer.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

På så vis säger du åt ASP.NET MVC att om du inte har angett ett värde i URL som ska styra dirigeringsbeteendet, ska inte **Home** utan **Objekt** användas som styrenhet och användarens **Index** som vy.

Om du kör appen nu anropar den **ItemController**, som anropar till centrallagerklassen och använder metoden GetItems för att returnera alla ofullständiga objekt till vyn **Vyer**\\**Objekt**\\**Index** . 

Om du bygger och kör det här projektet nu bör det se ut ungefär så här.    

![Skärmdump av den webbapp med att göra-lista som skapats i denna självstudie om databaser](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>Lägg till objekt
Vi lägger in några objekt i databasen så att vi kan titta på något mer än ett tomt rutnät.

Nu ska vi lägga till kod i Azure Cosmos DBRepository och ItemController så att posten sparas i Azure Cosmos DB.

1. Lägg till nedanstående metod i klassen **DocumentDBRepository**.
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Den här metoden tar bara ett objekt som skickats till den och sparar det i Azure Cosmos DB.
2. Öppna filen ItemController.cs och lägg till nedanstående kodutdrag i klassen. Det är så ASP.NET MVC vet vad den ska göra vid åtgärden **Skapa**. I det här fallet handlar det bara om att återge den associerade Create.cshtml-vyn som skapades tidigare.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    Vi behöver nu lite mer kod i styrenheten som godtar inmatningen från vyn **Skapa**.
3. Lägg till nästa kodblock i klassen ItemController.cs, som berättar för ASP.NET MVC vad som ska göras med formuläret POST för den här styrenheten.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    Den här koden anropar DocumentDBRepository och använder metoden CreateItemAsync för att spara det nya todo-objektet i databasen. 
   
    **Säkerhetsmeddelande**: Attributet **ValidateAntiForgeryToken** används här för att skydda appen mot attacker med förfalskning av begäran mellan webbplatser. Det är inte bara att lägga till attributet, även dina vyer behöver fungera med denna  antiförfalskningstoken. Mer information och implementeringsexempel finns i [Förhindra förfalskning av begäranden mellan webbplatser][Preventing Cross-Site Request Forgery]. Källkoden på [GitHub][GitHub] innehåller den fullständiga implementeringen.
   
    **Säkerhetsmeddelande**: Vi använder också attributet **binda** på metodparametern för att skydda mot overpostingattacker. Mer information finns i [Grundläggande CRUD-åtgärder i ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

Detta avslutar den kod som krävs för att lägga till nya objekt i vår databas.

### <a name="_Toc395637772"></a>Redigera objekt
Det finns en sista åtgärd vi ska utföra, vilket är att lägga till möjligheten att redigera **objekt** i databasen och markera dem som slutförda. Vi har redan lagt till redigeringsvyn i projektet, så vi behöver bara lägga till lite kod i vår styrenhet och i klassen **DocumentDBRepository**.

1. Lägg till nedanstående i klassen **DocumentDBRepository**.
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    Den första av dessa metoder, **GetItem**, hämtar ett objekt från Azure Cosmos DB som skickas tillbaka till **ItemController** och sedan vidare till vyn **Redigera**.
   
    Den andra av metoderna som vi nyss lade till ersätter **dokumentet** i Azure Cosmos DB med den version av **dokumentet** som överförts från **ItemController**.
2. Lägg till nedanstående i klassen **ItemController**.
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    Den första metoden hanterar den Http GET som inträffar när användaren klickar på länken **Redigera** från vyn **Index**. Den här metoden hämtar ett [ **dokument** ](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) från Azure Cosmos DB och skickar det till vyn **Redigera**.
   
    Vyn **Redigera** gör sedan en Http POST till **IndexController**. 
   
    Den andra metoden som vi lade till hanterar överföringen av det uppdaterade objektet till Azure Cosmos DB så att det sparas i databasen.

Det är allt som behövs för att köra vår app – en lista över ofullständiga **objekt** samt möjlighet att lägga till nya **objekt** och att redigera **objekt**.

## <a name="_Toc395637773"></a>Steg 6: Kör programmet lokalt
Testa appen på din lokala dator enligt nedanstående:

1. Tryck på F5 i Visual Studio för att bygga appen i felsökningsläge. Den ska bygga appen och starta en webbläsare med den tomma rutnätssidan vi såg tidigare:
   
    ![Skärmdump av den webbapp med att göra-lista som skapats i denna självstudie om databaser](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. Klicka på länken **Skapa nytt** och lägg till värden i fälten **Namn** och **Beskrivning**. Markera inte kryssrutan **Slutförd**, annars kommer det nya **objektet** att läggas till i slutfört tillstånd och visas inte i den inledande listan.
   
    ![Skärmdump av vyn Skapa](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. Klicka på **Skapa**, så omdirigeras du tillbaka till vyn **Index** och **objektet** visas i listan.
   
    ![Skärmdump av vyn Index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    Passa på att lägga till några fler **objekt** i din att göra-lista.
    
4. Klicka på **Redigera** bredvid ett **objekt** på listan så kommer du till vyn **Redigera**, där du kan uppdatera alla egenskaper för objektet, inklusive flaggan **Slutförd**. Om du markerar flaggan **Slutförd** och klickar på **Spara** tas **objektet** bort från listan över ofullständiga uppgifter.
   
    ![Skärmdump av indexvyn med rutan Slutförd ikryssad](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. När du har testat appen trycker du på Ctrl + F5 för att avsluta felsökningen. Nu är du redo att distribuera!

## <a name="_Toc395637774"></a>Steg 7: Distribuera programmet till Azure App Service 
När hela programmet fungerar som det ska med Azure Cosmos DB är det dags att distribuera webbappen till Azure App Service.  

1. Allt du behöver göra för att publicera appen är högerklicka på projektet i **Solution Explorer** och klicka på **Publicera**.
   
    ![Skärmdump av alternativet Publicera i Solution Explorer](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. Klicka på **Microsoft Azure App Service** i dialogrutan **Publicera** och välj sedan **Skapa ny** om du vill skapa en ny App Service-profil eller klicka på **Välj befintlig** om du vill använda en befintlig profil.

    ![Dialogrutan Publicera i Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. Om du har en befintlig Azure App Service-profil anger du namnet på din prenumeration. Använd filtret **Visa** om du vill sortera efter resursgrupp eller resurstyp och välj sedan Azure App Service. 
   
    ![Dialogrutan App Service i Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Om du vill skapa en ny Azure App Service-profil klickar du på **Skapa ny** i dialogrutan **Publicera**. Ange namnet på din webbapp och lämplig prenumeration, resursgrupp och App Service-plan i dialogrutan **Create App Service** (Skapa apptjänst) och klicka sedan på **Skapa**.

    ![Dialogrutan Create App Service (Skapa apptjänst) i Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Visual Studio publicerar din webbapp på några sekunder och öppnar en webbläsare där du kan se ditt arbete i Azure.



## <a name="_Toc395637775"></a>Nästa steg
Grattis! Du har precis skapat ditt första ASP.NET MVC-webbprogram med Azure Cosmos DB och publicerat det i Azure. Källkoden för hela appen, inklusive informations- och borttagningsfunktionerna som inte fanns med i den här självstudiekursen, kan hämtas eller klonas från [GitHub][GitHub]. Om du vill lägga till det i din app hämtar du koden och lägger till den i appen.

Om du vill lägga till ytterligare funktioner i appen tittar du igenom API:erna i [Azure Cosmos DB .NET-biblioteket](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet). Lägg gärna till egna bidrag i Azure Cosmos DB .NET-biblioteket på [GitHub][GitHub]. 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
