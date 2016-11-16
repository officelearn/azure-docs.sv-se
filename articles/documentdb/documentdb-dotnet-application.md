---
title: "Självstudiekurs om ASP.NET MVC för DocumentDB: utveckling av webbappar | Microsoft Docs"
description: "Självstudiekurs om ASP.NET MVC för att skapa en MVC-webbapp med DocumentDB. Du kommer att lagra JSON- och åtkomstdata från en ”att göra”-app på Azure Websites – stegvis självstudiekurs om ASP NET MVC."
keywords: "asp.net mvc självstudier, webbprogramsutveckling, mvc-webbprogram, asp net mvc självstudier steg för steg"
services: documentdb
documentationcenter: .net
author: syamkmsft
manager: jhubbard
editor: cgronlun
ms.assetid: 52532d89-a40e-4fdf-9b38-aadb3a4cccbc
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/25/2016
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: af5563f875c532c0b902685219818b1cd0945a66


---
# <a name="a-nametoc395809351aaspnet-mvc-tutorial-web-application-development-with-documentdb"></a><a name="_Toc395809351"></a>ASP.NET MVC-självstudie: Webbprogramsutveckling med DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md) 
> 
> 

Den här artikeln innehåller en heltäckande beskrivning av hur du bygger en ”att göra”-app med Azure DocumentDB. Syftet är att visa hur du effektivt kan lagra och tillämpa frågor på JSON-dokument med Azure DocumentDB. Uppgifterna lagras som JSON-dokument i Azure DocumentDB.

![Skärmdump som visar MVC-webbappen med ”att göra”-listan som skapas i denna självstudiekurs – stegvis självstudiekurs om ASP NET MVC](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image1.png)

I den här beskrivningen visas hur du använder DocumentDB-tjänsten som tillhandahålls av Azure för att lagra och komma åt data från en ASP.NET MVC-webbapp på Azure. Om du vill ha en självstudiekurs som endast fokuserar på DocumentDB och inte på MVC-komponenter i ASP.NET, finns [Bygg en C#-konsolapp med DocumentDB](documentdb-get-started.md).

> [!TIP]
> Den här självstudiekursen förutsätter att du har tidigare erfarenhet av MVC i ASP.NET och Azure Websites. Om du inte har använt ASP.NET eller [verktygen som krävs](#_Toc395637760) tidigare, rekommenderar vi att du hämtar det fullständiga exempelprojektet från [GitHub][GitHub] och följer anvisningarna i det exemplet. När du har byggt exempelprojektet kan du läsa den här artikeln för information om koden i projektets sammanhang.
> 
> 

## <a name="a-nametoc395637760aprerequisites-for-this-database-tutorial"></a><a name="_Toc395637760"></a>Förhandskrav för den här databas-självstudien
Se till att du har följande innan du börjar följa anvisningarna i den här artikeln:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio 2015](http://www.visualstudio.com/) eller Visual Studio 2013 uppdatering 4 eller högre. Om du använder Visual Studio 2013 måste du installera [Microsoft.Net.Compilers nuget-paketet](https://www.nuget.org/packages/Microsoft.Net.Compilers/) för att lägga till stöd för C# 6.0. 
* Azure SDK för .NET, version 2.5.1 eller senare, tillgängligt via [Microsoft Web Platform Installer][Microsoft Web Platform Installer].

Alla skärmdumpar i den här artikeln har tagits med Visual Studio 2013, uppdatering 4, och Azure SDK för .NET version 2.5.1. Om ditt system är konfigurerat med andra versioner kan det hända att skärmbilder och alternativ inte ser riktigt likadana ut, men om ovanstående förutsättningar är uppfyllda ska lösningen fungera.

## <a name="a-nametoc395637761astep-1-create-a-documentdb-database-account"></a><a name="_Toc395637761"></a>Steg 1: Skapa ett DocumentDB-databaskonto
Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto kan du gå vidare till [Skapa en ny MVC-app med ASP.NET](#_Toc395637762).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[!INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>
Vi kommer att gå igenom hur du skapar en ny MVC-app från grunden i ASP.NET. 

## <a name="a-nametoc395637762astep-2-create-a-new-aspnet-mvc-application"></a><a name="_Toc395637762"></a>Steg 2: Skapa ett nytt ASP.NET MVC-program
När du har ett konto skapar du ett nytt ASP.NET-projekt.

1. I Visual Studio klickar du på menyn **Arkiv**, pekar på **Nytt** och klickar sedan på **Projekt**.
   
       The **New Project** dialog box appears.
2. I panelen **Projekttyper** expanderar du **Mallar**, **Visual C#**, **Webb** och väljer sedan **ASP.NET webbtillämpningsprogram**.
   
      ![Skärmdump som visar dialogrutan Nytt projekt med projekttypen ASP.NET-webbtillämpningsprogram markerad](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image10.png)
3. I rutan **Namn** skriver du namnet på projektet. I den här självstudiekursen används namnet ”todo”. Om du väljer att använda något annat än det måste du ändra de angivna kodexemplen så att de använder det namn du gav din app på alla ställen där namnområdet todo nämns. 
4. Klicka på **Bläddra** och navigera till mappen där du vill skapa projektet och klicka sedan på **OK**.
   
      Dialogrutan **Nytt ASP.NET-projekt** visas.
   
      ![Skärmdump som visar dialogrutan Nytt ASP.NET-projekt med MVC-appmallen markerad och rutan Värd i molnet ikryssad](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image11.png)
5. Välj **MVC** i mallpanelen.
6. Om du planerar att ha appen på Azure markerar du **Värd i molnet** nere till höger, så drivs appen via Azure. Vi har valt att använda värd i molnet och köra appen från en Azure-webbplats. När du markerar det här alternativet företableras en Azure-webbplats för dig, vilket gör saker och ting mycket enklare när det blir dags att distribuera den slutliga fungerande appen. Om du vill lagra den på en annan plats eller inte vill konfigurera Azure med en gång avmarkerar du bara **Värd i molnet**.
7. Klicka på **OK** och låt Visual Studio generera ett kodskelett för den tomma ASP.NET MVC-mallen. 
8. Om du väljer värden i molnet kommer du att se minst en extra skärm där du uppmanas att logga in på ditt Azure-konto och ange några värden för din nya webbplats. Ange alla ytterligare värden och fortsätt. 
   
      Jag har inte valt någon databasserver eftersom vi inte använder någon Azure SQL-databasserver här. Vi kommer att skapa ett nytt Azure DocumentDB-konto senare i Azure Portal.
   
    Mer information om hur du väljer en **apptjänstplan** och **resursgrupp** finns i den [djupgående översikten över Azure-apptjänstplaner](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
      ![Skärmdump av dialogrutan Konfigurera Microsoft Azure-webbplats](./media/documentdb-dotnet-application/image11_1.png)
9. När Visual Studio har skapat den formaterade MVC-appen har du en tom ASP.NET-app som du kan köra lokalt.
   
    Vi kör inte projektet lokalt nu eftersom du säkert har sett ASP.NET-appen ”Hello World”. Vi hoppar direkt till att lägga till DocumentDB i projektet och bygga vår app.

## <a name="a-nametoc395637767astep-3-add-documentdb-to-your-mvc-web-application-project"></a><a name="_Toc395637767"></a>Steg 3: Lägg till DocumentDB till ditt MVC-webbprogramsprojekt
Nu har vi i princip de ASP.NET MVC-grunder vi behöver för lösningen och det är dags för det verkliga syftet med den här självstudiekursen, vilket är att lägga till Azure DocumentDB i vår MVC-webbapp.

1. DocumentDB .NET SDK paketeras och distribueras som ett NuGet paket. Hämta NuGet-paketet i Visual Studio med hjälp av NuGet-pakethanteraren i Visual Studio genom att högerklicka på projektet i **Solution Explorer** och sedan klicka på **Hantera NuGet-paket**.
   
      ![Skärmdump som visar alternativen vid högerklick för webbapprojektet i Solution Explorer, med Hantera NuGet-paket markerat.](./media/documentdb-dotnet-application/image21.png)
   
    Dialogrutan **Hantera NuGet-paket** öppnas.
2. I NuGet-rutan **Bläddra** klickar du på ***Azure DocumentDB***.
   
    Från resultatet installerar du paketet **Microsoft Azure DocumentDB klientbibliotek**. Då hämtas och installeras paketet DocumentDB samt alla beroenden, som Newtonsoft.Json. Klicka på **OK** i fönstret **Förhandsgranskning** och **Jag godkänner** i fönstret **Godkännande av licens** för att slutföra installationen.
   
      ![Skärmdump av fönstret Hantera NuGet-paket, med Microsoft Azure DocumentDB klientbiblioteket markerat](./media/documentdb-dotnet-application/nuget.png)
   
      Du kan också installera paketen med Pakethanterarkonsolen. Om du vill göra det klickar du på menyn **Verktyg**, **NuGet-pakethanteraren** och sedan på **Pakethanterarkonsolen**. Skriv följande i prompten:
   
        Install-Package Microsoft.Azure.DocumentDB
3. När paketet har installerats bör din Visual Studio-lösning likna nedanstående, med två nya referenser: Microsoft.Azure.Documents.Client och Newtonsoft.Json.
   
      ![Skärmdump av de två referenser som lagts till JSON-dataprojektet i Solution Explorer](./media/documentdb-dotnet-application/image22.png)

## <a name="a-nametoc395637763astep-4-set-up-the-aspnet-mvc-application"></a><a name="_Toc395637763"></a>Steg 4: Ställ in ASP.NET MVC-programmet
Nu ska vi lägga till modeller, vyer och styrenheter till MVC-appen:

* [Lägga till en modell](#_Toc395637764).
* [Lägga till en styrenhet](#_Toc395637765).
* [Lägga till vyer](#_Toc395637766).

### <a name="a-nametoc395637764aadd-a-json-data-model"></a><a name="_Toc395637764"></a>Lägg till en JSON-datamodell
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
   
    Alla data i DocumentDB skickas via kopplingen och lagras som JSON. Du kan styra hur objekten serialiseras/deserialiseras av JSON.NET genom attributet **JsonProperty** så som visas i den **Objekt**-klass vi just skapade. Du **måste** inte göra det, men jag vill vara säker på att mina egenskaper följer namnkonventionen med kamelnotation för JSON. 
   
    Du kan inte bara styra egenskapsnamnets format när det matas in i JSON, utan helt byta namn på dina .NET-egenskaper så som jag gjorde med egenskapen **Beskrivning**. 

### <a name="a-nametoc395637765aadd-a-controller"></a><a name="_Toc395637765"></a>Lägg till en controller
Nu har vi tagit hand om **M** och det är dags för vårt **C** i MVC, dvs. en styrenhetsklass (controller).

1. I **Solution Explorer** högerklickar du på mappen **Styrenheter**. Klicka sedan på **Lägg till** och på **Styrenhet**.
   
    Dialogrutan **Lägg till kodskelett** visas.
2. Välj **MVC 5 styrenhet – tom** och klicka sedan på **Lägg till**.
   
    ![Skärmdump av dialogrutan Lägg till kodskelett med alternativet MVC 5 styrenhet – tom markerat](./media/documentdb-dotnet-application/image14.png)
3. Ge din nya styrenhet namnet **Objektstyrning.**
   
    ![Skärmdump av dialogrutan Lägg till styrenhet](./media/documentdb-dotnet-application/image15.png)
   
    När filen har skapats bör din Visual Studio-lösning påminna om nedanstående, med den nya ItemController.cs-filen i **Solution Explorer**. Även den nya Item.cs-filen som du skapade tidigare visas.
   
    ![Skärmdump av lösningen i Visual Studio – Solution Explorer med den nya ItemController.cs-filen och Item.cs-filen markerade](./media/documentdb-dotnet-application/image16.png)
   
    Du kan stänga ItemController.cs, vi återkommer till den senare. 

### <a name="a-nametoc395637766aadd-views"></a><a name="_Toc395637766"></a>Lägg till vyer
Nu skapar vi vårt **V** i MVC, dvs. vyerna:

* [Lägga till Vyn Objektindex](#AddItemIndexView).
* [Lägga till vyn Nytt objekt](#AddNewIndexView).
* [Lägga till vyn Redigera objekt](#_Toc395888515).

#### <a name="a-nameadditemindexviewaadd-an-item-index-view"></a><a name="AddItemIndexView"></a>Lägg till en objektindexvy
1. I **Solution Explorer** expanderar du mappen **Vyer** och högerklickar på den tomma **objektmappen** som Visual Studio skapade när du lade till **ItemController** tidigare. Klicka på **Lägg till** och sedan på **Vy**.
   
    ![Skärmdump av Solution Explorer som visar den objektmapp som Visual Studio skapade med kommandot Lägg till vy markerat](./media/documentdb-dotnet-application/image17.png)
2. Gör följande i dialogrutan **Lägg till vy**:
   
   * I rutan **Vynamn** skriver du ***Index***.
   * I rutan**Mall** väljer du ***Lista***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Lämna rutan **Datakontextklass** tom. 
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
     
     ![Skärmdump som visar dialogrutan Lägg till vy](./media/documentdb-dotnet-application/image18.png)
3. När du har angett samtliga dessa värden klickar du på **Lägg till**  och låter Visual Studio skapa en ny mallvy. När det är klart öppnas cshtml-filen som skapades. Du kan stänga filen i Visual Studio eftersom vi återkommer till den senare.

#### <a name="a-nameaddnewindexviewaadd-a-new-item-view"></a><a name="AddNewIndexView"></a>Lägg till en nytt objektsvy
På liknande sätt som vi skapade vyn **Objektindex** ska vi nu skapa en ny vy där  nya **objekt** kan skapas.

1. I **Solution Explorer** högerklickar du på mappen **Objekt** en gång till. Klicka sedan på **Lägg till** och på **Vy**.
2. Gör följande i dialogrutan **Lägg till vy**:
   
   * I rutan **Vynamn** skriver du ***Skapa***.
   * I rutan**Mall** väljer du ***Skapa***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Lämna rutan **Datakontextklass** tom.
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
   * Klicka på **Lägg till**.

#### <a name="a-nametoc395888515aadd-an-edit-item-view"></a><a name="_Toc395888515"></a>Lägg till en redigera objektsvy
Lägg slutligen till en vy för redigering av ett **objekt**, på samma sätt som tidigare.

1. I **Solution Explorer** högerklickar du på mappen **Objekt** en gång till. Klicka sedan på **Lägg till** och på **Vy**.
2. Gör följande i dialogrutan **Lägg till vy**:
   
   * I rutan **Vynamn** skriver du ***Redigera***.
   * I rutan**Mall** väljer du ***Redigera***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Lämna rutan **Datakontextklass** tom. 
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
   * Klicka på **Lägg till**.

När det är klart stänger du alla cshtml-dokument i Visual Studio eftersom vi återkommer till dem senare.

## <a name="a-nametoc395637769astep-5-wiring-up-documentdb"></a><a name="_Toc395637769"></a>Steg 5: Koppla samma DocumentDB
Nu när standarddelarna i MVC är klara är det dags att lägga till koden för DocumentDB. 

I det här avsnittet vi lägger till kod som hanterar följande:

* [Lista ofullständiga objekt](#_Toc395637770).
* [Lägga till objekt](#_Toc395637771).
* [Redigera objekt](#_Toc395637772).

### <a name="a-nametoc395637770alisting-incomplete-items-in-your-mvc-web-application"></a><a name="_Toc395637770"></a>Lista ofullständiga objekt i ditt MVC-webbprogram
Det första du gör här är att lägga till en klass som innehåller all logik för att ansluta till och använda DocumentDB. I den här självstudiekursen kapslar vi in all denna logik i en centrallagerklass kallad DocumentDBRepository. 

1. Högerklicka på projektet i **Solution Explorer**. Klicka sedan på **Lägg till** och på **Klass**. Ge den nya klassen namnet **DocumentDBRepository** och klicka på **Lägg till**.
2. I den nyligen skapade **DocumentDBRepository**-klassen lägger du till nedanstående *using-satser* ovanför deklarationen för *namnområde*
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
   
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
   
   > [!TIP]
   > När du skapar en ny dokumentsamling kan du ange en valfri Frågealternativ-parameter av erbjudandetyp, vilket innebär att du kan specificera den nya samlingens prestandanivå. Om den här parametern inte överförs används typen standarderbjudande. Mer information om DocumentDB-erbjudandetyper finns i [Prestandanivåer för DocumentDB](documentdb-performance-levels.md)
   > 
   > 
3. Vi läser in vissa värden från konfigurationen, så öppna filen **Web.config** för appen och lägg till nedanstående rader under avsnittet `<AppSettings>`.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Uppdatera nu värden för *slutpunkt* och *auktoriseringsnyckel* via bladet Nycklar i Azure Portal. Använd den **URI** som finns i bladet Nycklar som värde för slutpunkt och använd den **PRIMÄRNYCKEL** eller **SEKUNDÄRNYCKEL** som finns i bladet Nycklar som värde för inställningen Auktoriseringsnyckel.

    Nu har kopplingarna till DocumentDB-centrallagret skapats och vi kan lägga till vår applogik.

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

![Skärmdump av den webbapp med att göra-lista som skapats i denna självstudie om databaser](./media/documentdb-dotnet-application/image23.png)

### <a name="a-nametoc395637771aadding-items"></a><a name="_Toc395637771"></a>Lägg till objekt
Vi lägger in några objekt i databasen så att vi kan titta på något mer än ett tomt rutnät.

Vi lägger till lite kod i DocumentDBRepository och ItemController för att spara posten i DocumentDB.

1. Lägg till nedanstående metod i klassen **DocumentDBRepository**.
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Den här metoden tar ett objekt som överförs till den och sparar det i DocumentDB.
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
   
    **Säkerhetsmeddelande**: Attributet **ValidateAntiForgeryToken** används här för att skydda appen mot attacker med förfalskning av begäran mellan webbplatser. Det är inte bara att lägga till attributet, även dina vyer behöver fungera med denna  antiförfalskningstoken. Mer information om ämnet och exempel på korrekt implementering finns i [Förhindra förfalskning av begäran mellan webbplatser][Förhindra förfalskning av begäran mellan webbplatser]. Källkoden på [GitHub][GitHub] har detta fullständigt implementerat.
   
    **Säkerhetsmeddelande**: Vi använder också attributet **binda** på metodparametern för att skydda mot overpostingattacker. Mer information finns i [Grundläggande CRUD-åtgärder i ASP.NET MVC][Grundläggande CRUD-åtgärder i ASP.NET MVC].

Detta avslutar den kod som krävs för att lägga till nya objekt i vår databas.

### <a name="a-nametoc395637772aediting-items"></a><a name="_Toc395637772"></a>Redigera objekt
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
   
    Den första av dessa metoder, **GetItem**, hämtar ett objekt ur DocumentDB som skickas tillbaka till **ItemController** och sedan vidare till vyn **Redigera**.
   
    Den andra av metoderna vi just lade till ersätter **dokumentet** i DocumentDB med den version av **dokumentet** som överförts från **ItemController**.
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
   
    Den första metoden hanterar den Http GET som inträffar när användaren klickar på länken **Redigera** från vyn **Index**. Den här metoden hämtar ett [**dokument**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) från DocumentDB och skickar det till vyn **Redigera**.
   
    Vyn **Redigera** gör sedan en Http POST till **IndexController**. 
   
    Den andra metoden som vi har lagt till hanterar överföringen av det uppdaterade objektet till DocumentDB för att sparas i databasen.

Det är allt som behövs för att köra vår app – en lista över ofullständiga **objekt** samt möjlighet att lägga till nya **objekt** och att redigera **objekt**.

## <a name="a-nametoc395637773astep-6-run-the-application-locally"></a><a name="_Toc395637773"></a>Steg 6: Kör programmet lokalt
Testa appen på din lokala dator enligt nedanstående:

1. Tryck på F5 i Visual Studio för att bygga appen i felsökningsläge. Den ska bygga appen och starta en webbläsare med den tomma rutnätssidan vi såg tidigare:
   
    ![Skärmdump av den webbapp med att göra-lista som skapats i denna självstudie om databaser](./media/documentdb-dotnet-application/image24.png)
   
    Om du använder Visual Studio 2013 och får felmeddelandet "Det går inte att vänta i texten i en catch-sats." måste du installera [Microsoft.Net.Compilers nuget-paketet](https://www.nuget.org/packages/Microsoft.Net.Compilers/). Du kan också jämföra koden mot exempelprojektet på [GitHub][GitHub]. 
2. Klicka på länken **Skapa nytt** och lägg till värden i fälten **Namn** och **Beskrivning**. Markera inte kryssrutan **Slutförd**, annars kommer det nya **objektet** att läggas till i slutfört tillstånd och visas inte i den inledande listan.
   
    ![Skärmdump av vyn Skapa](./media/documentdb-dotnet-application/image25.png)
3. Klicka på **Skapa**, så omdirigeras du tillbaka till vyn **Index** och **objektet** visas i listan.
   
    ![Skärmdump av vyn Index](./media/documentdb-dotnet-application/image26.png)
   
    Passa på att lägga till några fler **objekt** i din att göra-lista.
4. Klicka på **Redigera** bredvid ett **objekt** på listan så kommer du till vyn **Redigera**, där du kan uppdatera alla egenskaper för objektet, inklusive flaggan **Slutförd**. Om du markerar flaggan **Slutförd** och klickar på **Spara** tas **objektet** bort från listan över ofullständiga uppgifter.
   
    ![Skärmdump av indexvyn med rutan Slutförd ikryssad](./media/documentdb-dotnet-application/image27.png)
5. När du har testat appen trycker du på Ctrl + F5 för att avsluta felsökningen. Nu är du redo att distribuera!

## <a name="a-nametoc395637774astep-7-deploy-the-application-to-azure-websites"></a><a name="_Toc395637774"></a>Steg 7: Distribuera programmet till Azure Websites
När hela appen fungerar som den ska med DocumentDB är det dags att distribuera webbappen till Azure Websites. Om du valde **Värd i molnet** när du skapade det tomma ASP.NET MVC-projektet, behöver du knappt göra något eftersom Visual Studio gör största delen av arbetet. 

1. Allt du behöver göra för att publicera appen är högerklicka på projektet i **Solution Explorer** och klicka på **Publicera**.
   
    ![Skärmdump av alternativet Publicera i Solution Explorer](./media/documentdb-dotnet-application/image28.png)
2. Allt borde redan ha konfigurerats enligt dina autentiseringsuppgifter. Webbplatsen har faktiskt redan skapats i Azure på den **måladress** som visas – allt du behöver göra är att klicka på **Publicera**.
   
    ![Skärmdump av dialogrutan Publicera webbplats i Visual Studio – stegvis självstudiekurs om ASP NET MVC](./media/documentdb-dotnet-application/image29.png)

Efter några sekunder har Visual Studio publicerat din webbapp och öppnar en webbläsare där du kan se ditt arbete köras i Azure!

## <a name="a-nametoc395637775anext-steps"></a><a name="_Toc395637775"></a>Nästa steg
Grattis! Du har precis skapat din första ASP.NET MVC-webbapp med Azure DocumentDB och publicerat den på Azure Websites. Källkoden för hela appen, inklusive detalj- och ta bort-funktionerna som inte fanns med i den här självstudiekursen, kan hämtas eller klonas från [GitHub][GitHub]. Om du vill lägga till det i din app hämtar du koden och lägger till den i appen.

Om du vill lägga till ytterligare funktioner i appen tar du en titt på de API:er som finns i [Document DB .NET-biblioteket](https://msdn.microsoft.com/library/azure/dn948556.aspx). Lägg gärna till bidrag i DocumentDB-.NET-biblioteket på [GitHub][GitHub]. 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Installationsprogram för Microsoft-webbplattformen]: http://www.microsoft.com/web/downloads/platform.aspx
[Förhindra förfalskning av begäranden mellan webbplatser]: http://go.microsoft.com/fwlink/?LinkID=517254
[Grundläggande CRUD-åtgärder i ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app



<!--HONumber=Nov16_HO2-->


