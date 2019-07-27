---
title: 'ASP.NET Core MVC-självstudie för Azure Cosmos DB: Utveckling av webbapp'
description: ASP.NET Core MVC-självstudie för att skapa ett MVC-webbprogram med hjälp av Azure Cosmos DB. Du kommer att lagra JSON och komma åt data från en att göra-app som finns på Azure App Service-ASP NET Core MVC-självstudie steg för steg.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 85d9cbe7d0807ca0e7951e1e12d1edbbf7c921db
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "67985931"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Självstudier: Utveckla ett ASP.NET Core MVC-webbprogram med Azure Cosmos DB med hjälp av .NET SDK 

> [!div class="op_single_selector"]
> * [NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Den här självstudien beskriver hur du använder Azure Cosmos DB för lagring av och åtkomst till data från ett ASP.NET MVC-program som hanteras på Azure. I den här självstudien använder du .NET SDK v3. Följande bild visar den webbplats som du kommer att skapa med hjälp av exemplet i den här artikeln:
 
![Skärm bild av MVC-webbappen för att göra-listan som skapats i den här självstudien – guiden för ASP NET Core MVC-steg](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Om du inte har tid att slutföra självstudien kan du ladda ned det fullständiga exempelprojektet från [GitHub][GitHub].

Den här självstudiekursen omfattar:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos-konto
> * Skapa en ASP.NET Core MVC-app
> * Ansluta appen till Azure Cosmos DB 
> * Utföra CRUD-åtgärder på data

> [!TIP]
> Den här självstudien förutsätter att du har tidigare erfarenhet av att använda ASP.NET Core MVC och Azure App Service. Om du är nybörjare på ASP.NET Core eller de [verktyg](#prerequisites)som krävs, rekommenderar vi att du hämtar det fullständiga exempelprojektet från [GitHub][GitHub], lägger till de nödvändiga NuGet-paketen och kör det. När du skapar projektet kan du läsa den här artikeln för att få information om koden i projektets sammanhang.

## <a name="prerequisites"></a>Förhandskrav 

Innan du följer anvisningarna i den här artikeln bör du se till att du har följande resurser:

* **Ett aktivt Azure-konto:** Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Alla skärm dum par i den här artikeln har gjorts med Microsoft Visual Studio Community 2019. Om ditt system är konfigurerat med en annan version kan det hända att skärmbilder och alternativ inte ser riktigt likadana ut, men om ovanstående krav är uppfyllda ska lösningen fungera.

## <a name="create-an-azure-cosmos-account"></a>Steg 1: Skapa ett Azure Cosmos-konto

Vi börjar med att skapa ett Azure Cosmos-konto. Om du redan har ett Azure Cosmos DB SQL API-konto eller använder Azure Cosmos DB-emulatorn för den här självstudien kan du hoppa över och gå vidare till avsnittet [Skapa ett nytt ASP.NET MVC-program](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

I nästa avsnitt skapar du ett nytt ASP.NET Core MVC-program. 

## <a name="create-a-new-mvc-application"></a>Steg 2: Skapa ett nytt ASP.NET Core MVC-program

1. I Visual Studio öppnar du menyn **Arkiv**, väljer **Nytt** och sedan **Projekt**. Dialogrutan **Nytt projekt** visas.

2. I fönstret **nytt projekt** använder du kryss rutan **Sök efter mallar** för att söka efter "webb" och väljer sedan **ASP.net Core webb program**. 

   ![Skapa ett nytt ASP.NET Core webb program projekt](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. I rutan **Namn** skriver du namnet på projektet. I den här självstudiekursen används namnet ”todo”. Om du väljer att använda något annat än det här namnet kan du, när den här kursen pratar om att göra-namnområdet, justera de angivna kod exemplen så att du använder det som du har namngett ditt program. 

4. Välj **Bläddra** för att navigera till mappen där du vill skapa projektet. Välj **Skapa**. 

5. Dialog rutan **skapa en ny ASP.net Core webb program** visas. I listan Mallar väljer du **webb program (modell-Visa-kontrollant)** .

6. Välj **skapa** och låt Visual Studio göra ramverk runt den tomma ASP.net Core MVC-mallen. 

7. När Visual Studio har skapat det formaterade MVC-programmet har du ett tomt ASP.NET-program som du kan köra lokalt.

## <a name="add-nuget-packages"></a>Steg 3: Lägg till Azure Cosmos DB NuGet-paketet i projektet

Nu när vi har flest den ASP.NET Core MVC Framework-kod som vi behöver för den här lösningen ska vi lägga till de NuGet-paket som krävs för att ansluta till Azure Cosmos DB.

1. Azure Cosmos DB .NET SDK paketeras och distribueras som ett NuGet-paket. Hämta NuGet-paketet i Visual Studio med hjälp av NuGet-pakethanteraren i Visual Studio genom att högerklicka på projektet i **Solution Explorer** och sedan välja **Hantera NuGet-paket**.
   
   ![Skärm bild av de högerklickna alternativen för webb program projektet i Solution Explorer, med hantera NuGet-paket markerade.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Dialogrutan **Hantera NuGet-paket** öppnas. I rutan för att **Bläddra** för NuGet skriver du **Microsoft.Azure.Cosmos**. Installera paketet **Microsoft. Azure. Cosmos** från resultatet. Den hämtar och installerar Azure Cosmos DB-paketet och dess beroenden. Välj **Jag accepterar** i fönstret **licens godkännande** för att slutföra installationen.
   
   Du kan även installera NuGet-paketet med hjälp av Package Manager-konsolen. Om du vill göra det går du till menyn **Verktyg**, väljer **NuGet-pakethanteraren** och därefter **Packet Manager-konsolen**. Skriv följande kommando i prompten:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. När paketet har installerats ska ditt Visual Studio-projekt innehålla biblioteks referensen till Microsoft. Azure. Cosmos.
  
## <a name="set-up-the-mvc-application"></a>Steg 4: Konfigurera ASP.NET Core MVC-appen

Nu lägger vi till modellerna, vyerna och styrenheterna till det här MVC-programmet:

* [Lägga till en modell](#add-a-model).
* [Lägga till en styrenhet](#add-a-controller).
* [Lägga till vyer](#add-views).

### <a name="add-a-model"></a> Lägga till en modell

1. I **Solution Explorer** högerklickar du på mappen **Modeller**, väljer **Lägg till** och sedan **Klass**. Dialogrutan **Lägg till nytt objekt** visas.

1. Namnge din nya klass **Item.cs** och välj **Lägg till**. 

1. Ersätt sedan koden i klassen "Item.cs" med följande kod:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   De data som lagras i Azure Cosmos DB skickas via anslutningen och lagras som JSON. Om du vill styra hur objekten serialiseras/deserialiseras av JSON.NET kan du använda attributet **JsonProperty** som visas i **objekt** klassen som du skapade. Du kan inte bara styra formatet på egenskaps namnet som går in i JSON. du kan också byta namn på .NET-egenskaperna som du gjorde  med egenskapen slutförd. 

### <a name="add-a-controller"></a>Lägg till en controller

1. I **Solution Explorer** högerklickar du på mappen **Styrenheter**. Välj sedan **Lägg till** och sedan **Styrenhet**. Dialogrutan **Lägg till kodskelett** visas.

1. Välj **MVC-kontrollant-tom** och välj **Lägg till**.

   ![Skärm bild av dialog rutan Lägg till Autogenerera med alternativet MVC Controller-tomt markerat](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Namnge din nya kontrollant, **ItemController**och Ersätt koden i filen med följande kod:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   Attributet **ValidateAntiForgeryToken** används här för att skydda programmet mot attacker med förfalskning av begäran mellan webbplatser. Det finns mer att göra än att bara lägga till det här attributet. Även dina vyer bör fungera med den här antiförfalskningstoken. Mer information om ämnet och exempel på hur du implementerar detta korrekt finns i [förhindra förfalskning av begäran mellan webbplatser][Preventing Cross-Site Request Forgery]. Källkoden på [GitHub][GitHub] har detta fullständigt implementerat.

   Vi använder även attributet **Bind** på metodparametern för att skydda mot overposting-attacker. Mer information finns i [grundläggande CRUD-åtgärder i ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

### <a name="add-views"></a>Lägg till vyer

Nu skapar vi följande tre vyer: 

* [Lägg till en listobjektvy](#AddItemIndexView).
* [Lägg till en ny objektsvy](#AddNewIndexView).
* [Lägg till en objektredigeringsvy](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Lägg till en listobjektvy

1. I **Solution Explorer** expanderar du mappen **Vyer** och högerklickar på den tomma **objektmapp** som Visual Studio skapade när du lade till **ItemController** tidigare. Klicka på **Lägg till** och sedan på **Vy**.
   
   ![Skärm bild av Solution Explorer som visar objekt-mappen som Visual Studio skapade med kommandot Lägg till vy markerat](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. I dialogrutan **Lägg till vy** uppdaterar du följande värden:
   
   * I rutan **Vynamn** skriver du ***Index***.
   * I rutan**Mall** väljer du ***Lista***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
     
   ![Skärm bild som visar dialog rutan Lägg till vy](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. När du har lagt till de här värdena väljer du **Lägg till** och låter Visual Studio skapa en ny mallvy. När det är klart öppnas den cshtml-fil som har skapats. Du kan stänga den filen i Visual Studio eftersom du återkommer till den senare.

#### <a name="AddNewIndexView"></a>Lägg till en ny objektsvy

På ett liknande sätt som du skapade en vy för listobjekt skapar du en ny vy för att skapa objekt med hjälp av följande steg:

1. I **Solution Explorer** högerklickar du på mappen **Item** igen, väljer **Lägg till** och sedan **Vy**.

1. I dialogrutan **Lägg till vy** uppdaterar du följande värden:
   
   * I rutan **Vynamn** skriver du ***Skapa***.
   * I rutan**Mall** väljer du ***Skapa***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
   * Välj **Lägg till**.
   
#### <a name="AddEditIndexView"></a>Lägg till en objektredigeringsvy

Och slutligen lägger du till en vy för att redigera ett objekt med följande steg:

1. I **Solution Explorer** högerklickar du på mappen **Item** igen, väljer **Lägg till** och sedan **Vy**.

1. Gör följande i dialogrutan **Lägg till vy**:
   
   * I rutan **Vynamn** skriver du ***Redigera***.
   * I rutan**Mall** väljer du ***Redigera***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
   * Välj **Lägg till**.

När det är klart stänger du alla cshtml-dokument i Visual Studio eftersom du återkommer till de här vyerna senare.

## <a name="connect-to-cosmosdb"></a>Steg 5: Ansluta till Azure Cosmos DB 

Nu när standarddelarna i MVC är klara är det dags att lägga till koden för att ansluta till Azure Cosmos DB och genomföra CRUD-åtgärderna. 

### <a name="perform-crud-operations"></a> Utföra CRUD-åtgärder på data

Det första du gör här är att lägga till en klass som innehåller logiken för att ansluta till och använda Azure Cosmos DB. I den här självstudien kommer vi att kapsla in den här logiken i en klass `ICosmosDBService`som kallas `CosmosDBService` och ett gränssnitt som kallas. Den här tjänsten utför CRUD och Läs flödes åtgärder, till exempel för att Visa ofullständiga objekt, skapa, redigera och ta bort objekt. 

1. I **Solution Explorer** skapar du en ny mapp under projektet med namnet **Services** (Tjänster).

1. Högerklicka på mappen **Services** (Tjänster), välj **Lägg till** och sedan **Klass**. Ge den nya klassen namnet **CosmosDBService** och välj **Lägg till**.

1. Lägg till följande kod i **CosmosDBService** -klassen och Ersätt koden i filen med följande kod:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Upprepa steg 2-3, men den här gången för en klass med namnet **ICosmosDBService**och Lägg till följande kod:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. Föregående kod får en `CosmosClient` som del av konstruktorn. Efter ASP.NET Core pipeline måste vi gå till projektets **startup.cs** och initiera klienten baserat på konfigurationen som en singleton-instans som ska matas in via [beroende inmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). I **ConfigureServices** -hanteraren definierar vi:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. I samma fil definierar vi vår hjälp metod **InitializeCosmosClientInstanceAsync**, som kommer att läsa konfigurationen och initiera klienten.

    ```csharp
    private static async Task<CosmosDbService> InitializeCosmosClientInstanceAsync(IConfigurationSection configurationSection)
    {
        string databaseName = configurationSection.GetSection("DatabaseName").Value;
        string containerName = configurationSection.GetSection("ContainerName").Value;
        string account = configurationSection.GetSection("Account").Value;
        string key = configurationSection.GetSection("Key").Value;
        CosmosClientBuilder clientBuilder = new CosmosClientBuilder(account, key);
        CosmosClient client = clientBuilder
                            .WithConnectionModeDirect()
                            .Build();
        CosmosDbService cosmosDbService = new CosmosDbService(client, databaseName, containerName);
        Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
        await database.CreateContainerIfNotExistsAsync(containerName, "/id");

        return cosmosDbService;
    }
    ```

1. Konfigurationen definieras i projektets **appSettings. JSON** -fil. Öppna den och Lägg till ett avsnitt som heter **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Nu när du kör programmet kommer ASP.NET Core pipelinen att instansiera **CosmosDbService** och underhålla en enda instans som singleton. När **ItemController** används för att bearbeta begär Anden på klient sidan, kommer den att få den här enskilda instansen och kunna använda den för att utföra CRUD-åtgärder.

Om du skapar och kör det här projektet nu bör du nu se något som ser ut så här:

![Skärm bild av webb programmet att göra-lista som skapats av den här databas guiden](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Steg 6: Kör programmet lokalt

Testa programmet på din lokala dator med följande steg:

1. Tryck på F5 i Visual Studio för att bygga appen i felsökningsläge. Den ska bygga appen och starta en webbläsare med den tomma rutnätssidan vi såg tidigare:
   
   ![Skärm bild av webb programmet att göra-listan som skapats i den här självstudien](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Klicka på länken **Skapa nytt** och lägg till värden i fälten **Namn** och **Beskrivning**. Låt kryssrutan **Slutförd** vara avmarkerad. Annars läggs det nya objektet till i slutfört tillstånd och visas inte i den inledande listan.
   
3. Klicka på **Skapa**, så omdirigeras du tillbaka till vyn **Index** och objektet visas i listan. Du kan lägga till några fler objekt i att göra-listan.

    ![Skärm bild av vyn index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Klicka på **Redigera** bredvid ett **objekt** på listan så kommer du till vyn **Redigera**, där du kan uppdatera alla egenskaper för objektet, inklusive flaggan **Slutförd**. Om du markerar flaggan **fullständig** och klickar på **Spara**, kommer **objektet** att visas som slutfört i listan.
   
   ![Skärm bild av vyn index med rutan slutförd markerad](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. Du kan när som helst kontrol lera statusen för data i Azure Cosmos DB tjänsten med hjälp av [Cosmos Explorer](https://cosmos.azure.com) eller Azure Cosmos DB emulatorns datautforskaren.

6. När du har testat appen trycker du på Ctrl + F5 för att avsluta felsökningen. Nu är du redo att distribuera!

## <a name="deploy-the-application-to-azure"></a>Steg 7: Distribuera programmet 
När hela programmet fungerar som det ska med Azure Cosmos DB är det dags att distribuera webbappen till Azure App Service.  

1. Om du vill publicera det här programmet högerklickar du på projektet i **Solution Explorer** och väljer **Publicera**.
   
2. I dialog rutan **publicera** väljer du **App Service**, väljer **Skapa ny** för att skapa en App Service profil eller väljer **Välj befintlig** om du vill använda en befintlig profil.

3. Om du har en befintlig Azure App Service-profil väljer du **Prenumeration** på den nedrullningsbara menyn. Använd filtret **Visa** om du vill sortera efter resursgrupp eller resurstyp. Sök sedan efter den Azure App Service som krävs och välj **OK**.
   
   ![Dialogrutan App Service i Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Om du vill skapa en ny Azure App Service-profil klickar du på **Skapa ny** i dialogrutan **Publicera**. I dialogrutan **Skapa App Service** anger du namnet på din webbapp och lämplig prenumeration, resursgrupp och App Service-plan. Välj sedan **Skapa**.

   ![Dialogrutan Create App Service (Skapa apptjänst) i Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Under några sekunder publicerar Visual Studio ditt webb program och öppnar en webbläsare där du kan se projektet som körs i Azure!

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du skapar ett ASP.NET Core MVC-webbprogram som kan komma åt data som lagras i Azure Cosmos DB. Nu kan du fortsätta till nästa artikel:

* [Lär dig mer om att partitionera data i Azure Cosmos DB](./partitioning-overview.md)
* [Lär dig mer om hur du utför mer avancerade frågor i Azure Cosmos DB](./how-to-sql-query.md)
* [Lär dig mer om att modellera dina data i ett mer avancerat scenario](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
