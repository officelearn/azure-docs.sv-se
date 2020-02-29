---
title: Självstudie för ASP.NET Core MVC-webbapp med Azure Cosmos DB
description: ASP.NET Core MVC-självstudie för att skapa ett MVC-webbprogram med hjälp av Azure Cosmos DB. Du kommer att lagra JSON och komma åt data från en att göra-app som finns på Azure App Service-ASP NET Core MVC-självstudie steg för steg.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 5403725a57c68a45621d6cc509c57d864b2e0633
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164924"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Självstudie: utveckla ett ASP.NET Core MVC-webbprogram med Azure Cosmos DB med hjälp av .NET SDK

> [!div class="op_single_selector"]
> * [NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Den här självstudien beskriver hur du använder Azure Cosmos DB för lagring av och åtkomst till data från ett ASP.NET MVC-program som hanteras på Azure. I den här självstudien använder du .NET SDK v3. Följande bild visar den webb sida som du skapar med hjälp av exemplet i den här artikeln:

![Skärm bild av MVC-webbappen för att göra-listan som skapats i den här självstudien – guiden för ASP NET Core MVC-steg](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Om du inte har tid att slutföra självstudien kan du ladda ned det fullständiga exempelprojektet från [GitHub][GitHub].

Den här självstudiekursen omfattar:

> [!div class="checklist"]
>
> * Skapa ett Azure Cosmos-konto
> * Skapa en ASP.NET Core MVC-app
> * Ansluta appen till Azure Cosmos DB
> * Utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) för data

> [!TIP]
> Den här självstudien förutsätter att du har tidigare erfarenhet av att använda ASP.NET Core MVC och Azure App Service. Om du är nybörjare på ASP.NET Core eller de [verktyg](#prerequisites)som krävs, rekommenderar vi att du hämtar det fullständiga exempelprojektet från [GitHub][GitHub], lägger till de nödvändiga NuGet-paketen och kör det. När du skapar projektet kan du läsa den här artikeln för att få information om koden i projektets sammanhang.

## <a name="prerequisites"></a>Förhandskrav

Se till att du har följande resurser innan du följer anvisningarna i den här artikeln:

* Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Alla skärm dum par i den här artikeln är från Microsoft Visual Studio Community 2019. Om du använder en annan version kanske dina skärmar och alternativ inte matchar helt. Lösningen bör fungera om du uppfyller kraven.

## <a name="create-an-azure-cosmos-account"></a>Steg 1: Skapa ett Azure Cosmos-konto

Vi börjar med att skapa ett Azure Cosmos-konto. Om du redan har ett Azure Cosmos DB SQL API-konto eller om du använder Azure Cosmos DB-emulatorn går du vidare till [steg 2: skapa ett nytt ASP.NET MVC-program](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

I nästa avsnitt skapar du ett nytt ASP.NET Core MVC-program.

## <a name="create-a-new-mvc-application"></a>Steg 2: skapa ett nytt ASP.NET Core MVC-program

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt**, leta upp och välj **ASP.net Core webb program** för C#. Välj **Nästa** för att fortsätta.

   ![Skapa ett nytt ASP.NET Core webb program projekt](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. I **Konfigurera ditt nya projekt**namnger du *projektet och väljer* **skapa**.

1. I **skapa en ny ASP.net Core webb program**väljer du **webb program (modell-Visa-kontrollant)** . Fortsätt genom att välja **skapa** .

   Visual Studio skapar ett tomt MVC-program.

1. Välj **felsök** > **Starta fel sökning** eller F5 för att köra ditt ASP.NET-program lokalt.

## <a name="add-nuget-packages"></a>Steg 3: Lägg till Azure Cosmos DB NuGet-paketet i projektet

Nu när vi har flest den ASP.NET Core MVC Framework-kod som vi behöver för den här lösningen ska vi lägga till de NuGet-paket som krävs för att ansluta till Azure Cosmos DB.

1. I **Solution Explorer**högerklickar du på projektet och väljer **Hantera NuGet-paket**.

1. I **NuGet Package Manager**söker du efter och väljer **Microsoft. Azure. Cosmos**. Välj **Installera**.

   ![Installera NuGet-paket](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio laddar ned och installerar Azure Cosmos DB-paketet och dess beroenden.

   Du kan också använda **Package Manager-konsolen** för att installera NuGet-paketet. Det gör du genom att välja **verktyg** > **NuGet Package Manager** > **Package Manager-konsolen**. Skriv följande kommando i prompten:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>Steg 4: Konfigurera ASP.NET Core MVC-appen

Nu ska vi lägga till modeller, vyer och styrenheter för det här MVC-programmet.

### <a name="add-a-model"></a> Lägga till en modell

1. I **Solution Explorer**högerklickar du på mappen **modeller** , väljer **Lägg till** > **klass**.

1. I **Lägg till nytt objekt**namnger du den nya klassen *Item.cs* och väljer **Lägg till**.

1. Ersätt innehållet i *Item.cs* -klassen med följande kod:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB använder JSON för att flytta och lagra data. Du kan använda attributet `JsonProperty` för att kontrol lera hur JSON serialiserar och deserialiserar objekt. Klassen `Item` visar attributet `JsonProperty`. Den här koden styr formatet på egenskaps namnet som går till JSON. Det byter också namn på .NET-egenskapen `Completed`.

### <a name="add-views"></a>Lägg till vyer

Nu ska vi skapa följande tre vyer.

* Lägg till en vy för List objekt
* Lägg till en ny objekts vy
* Lägga till vyn Redigera objekt

#### <a name="AddItemIndexView"></a>Lägg till en listobjektvy

1. I **Solution Explorer**högerklickar du på mappen **vyer** och väljer **Lägg till** > **ny mapp**. Namnge *objektet*Folder.

1. Högerklicka på mappen tomt **objekt** och välj sedan **Lägg till** > **vy**.

1. I **Lägg till MVC-vy**anger du följande värden:

   * I **visnings namn**anger du *index*.
   * I **mall**väljer du **lista**.
   * I **modell klass**väljer du **objekt (att göra. Modeller)** .
   * Välj **Använd en layout-sida** och ange *~/views/Shared/_Layout. cshtml*.

   ![Skärm bild som visar dialog rutan Lägg till MVC-vy](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. När du har lagt till de här värdena väljer du **Lägg till** och låter Visual Studio skapa en ny mallvy.

När du är färdig öppnar Visual Studio den *cshtml* -fil som skapas. Du kan stänga filen i Visual Studio. Vi kommer tillbaka till den senare.

#### <a name="AddNewIndexView"></a>Lägg till en ny objektsvy

På ett liknande sätt som du skapade en vy för listobjekt skapar du en ny vy för att skapa objekt med hjälp av följande steg:

1. I **Solution Explorer**högerklickar du på mappen **objekt** igen och väljer **Lägg till** > **vy**.

1. I **Lägg till MVC-vy**gör du följande ändringar:

   * Skriv *skapa*i **vynamn**.
   * I **mall**väljer du **skapa**.
   * I **modell klass**väljer du **objekt (att göra. Modeller)** .
   * Välj **Använd en layout-sida** och ange *~/views/Shared/_Layout. cshtml*.
   * Välj **Lägg till**.

#### <a name="AddEditIndexView"></a>Lägg till en objektredigeringsvy

Och slutligen lägger du till en vy för att redigera ett objekt med följande steg:

1. Från **Solution Explorer**högerklickar du på mappen **objekt** igen och väljer **Lägg till** > **vy**.

1. I **Lägg till MVC-vy**gör du följande ändringar:

   * I rutan **Vynamn** skriver du *Redigera*.
   * I rutan**Mall** väljer du **Redigera**.
   * I rutan **Modellklass** väljer du **Objekt (todo.Models)** .
   * Välj **Använd en layout-sida** och ange *~/views/Shared/_Layout. cshtml*.
   * Välj **Lägg till**.

När du har slutfört de här stegen stänger du alla *cshtml* -dokument i Visual Studio när du går tillbaka till dessa vyer senare.

### <a name="initialize-services"></a>Deklarera och initiera tjänster

Först lägger vi till en klass som innehåller logiken för att ansluta till och använda Azure Cosmos DB. I den här självstudien kommer vi att kapsla in den här logiken i en klass med namnet `CosmosDBService` och ett gränssnitt som kallas `ICosmosDBService`. Den här tjänsten utför CRUD-åtgärderna. Den läser också feed-åtgärder, till exempel att inte Visa ofullständiga objekt, skapa, redigera och ta bort objekt.

1. I **Solution Explorer**högerklickar du på projektet och väljer **Lägg till** > **ny mapp**. Namnge mappen *Services*.

1. Högerklicka på mappen **tjänster** , Välj **Lägg till** > **klass**. Ge den nya klassen namnet *CosmosDBService* och välj **Lägg till**.

1. Ersätt innehållet i *CosmosDBService.cs* med följande kod:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Högerklicka på mappen **tjänster** , Välj **Lägg till** > **klass**. Ge den nya klassen namnet *ICosmosDBService* och välj **Lägg till**.

1. Lägg till följande kod i *ICosmosDBService* -klassen:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Öppna filen *startup.cs* i din lösning och ersätt `ConfigureServices`-metoden med:

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    Koden i det här steget initierar klienten baserat på konfigurationen som en singleton-instans som ska matas [in via beroende insprutning i ASP.net Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. I samma fil lägger du till följande metod **InitializeCosmosClientInstanceAsync**, som läser konfigurationen och initierar klienten.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync":::

1. Definiera konfigurationen i projektets *appSettings. JSON* -fil som visas i följande kodfragment:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a>Lägg till en controller

1. I **Solution Explorer**högerklickar du på mappen **kontrollanter** och väljer **Lägg till** > **kontrollant**.

1. I **Lägg till Autogenerera**väljer du **MVC-kontrollant-tom** och väljer **Lägg till**.

   ![Välj MVC-kontrollant-tom i Lägg till Autogenerera](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Namnge din nya kontrollants *ItemController*.

1. Ersätt innehållet i *ItemController.cs* med följande kod:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Attributet **ValidateAntiForgeryToken** används här för att skydda programmet mot attacker med förfalskning av begäran mellan webbplatser. Dina vyer bör fungera med den här antismidta token. Mer information och exempel finns i [förhindra förfalskning av CSRF-attacker (Cross-Site Request) i ASP.NET MVC-program][Preventing Cross-Site Request Forgery]. Källkoden på [GitHub][GitHub] har detta fullständigt implementerat.

Vi använder även attributet **Bind** på metodparametern för att skydda mot overposting-attacker. Mer information finns i [Självstudier: implementera CRUD-funktioner med Entity Framework i ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="run-the-application"></a>Steg 5: kör programmet lokalt

Gör så här om du vill testa programmet på den lokala datorn:

1. Tryck på F5 i Visual Studio för att bygga appen i felsökningsläge. Den ska bygga appen och starta en webbläsare med den tomma rutnätssidan vi såg tidigare:

   ![Skärm bild av webb programmet att göra-listan som skapats i den här självstudien](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   Om programmet i stället öppnas på Start sidan lägger du till `/Item` till URL: en.

1. Välj länken **Skapa ny** och Lägg till värden i fälten **namn** och **Beskrivning** . Lämna kryss rutan **slutförd** omarkerad. Om du väljer den lägger appen till det nya objektet i ett slutfört tillstånd. Objektet visas inte längre i den inledande listan.

1. Välj **Skapa**. Appen skickar tillbaka till vyn **index** och ditt objekt visas i listan. Du kan lägga till fler objekt i din **att göra-** lista.

    ![Skärm bild av vyn index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Välj **Redigera** bredvid ett **objekt** i listan. Appen öppnar vyn **Redigera** där du kan uppdatera alla egenskaper för ditt objekt, inklusive flaggan **slutförd** . Om du väljer **slutförd** och väljer **Spara**, visar appen **objektet** som slutfört i listan.

   ![Skärm bild av vyn index med rutan slutförd markerad](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Kontrol lera statusen för data i Azure Cosmos DB tjänsten med hjälp av [Cosmos Explorer](https://cosmos.azure.com) eller Azure Cosmos DB emulatorns datautforskaren.

1. När du har testat appen väljer du Ctrl + F5 för att stoppa fel sökningen av appen. Nu är du redo att distribuera!

## <a name="deploy-the-application-to-azure"></a>Steg 6: Distribuera programmet

När hela programmet fungerar som det ska med Azure Cosmos DB är det dags att distribuera webbappen till Azure App Service.  

1. Om du vill publicera det här programmet högerklickar du på projektet i **Solution Explorer** och väljer **publicera**.

1. I Välj **ett publicerings mål**väljer du **App Service**.

1. Om du vill använda en befintlig App Service profil väljer du **Välj befintlig**och sedan **publicera**.

1. I **App Service**väljer du en **prenumeration**. Använd filtret **Visa** om du vill sortera efter resursgrupp eller resurstyp.

1. Hitta din profil och välj sedan **OK**. Sök sedan efter den Azure App Service som krävs och välj **OK**.

   ![Dialogrutan App Service i Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Ett annat alternativ är att skapa en ny profil:

1. Som i föregående procedur högerklickar du på projektet i **Solution Explorer** och väljer **publicera**.
  
1. I Välj **ett publicerings mål**väljer du **App Service**.

1. I **Välj ett publicerings mål**väljer du **Skapa nytt** och sedan **publicera**.

1. I **App Service**anger du namnet på din webbapp och lämplig prenumeration, resurs grupp och värd plan och väljer sedan **skapa**.

   ![Dialogrutan Create App Service (Skapa apptjänst) i Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

Under några sekunder publicerar Visual Studio ditt webb program och öppnar en webbläsare där du kan se projektet som körs i Azure!

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar ett ASP.NET Core MVC-webbprogram. Ditt program kan komma åt data som lagras i Azure Cosmos DB. Nu kan du fortsätta med de här resurserna:

* [Partitionering i Azure Cosmos DB](./partitioning-overview.md)
* [Komma igång med SQL-frågor](./how-to-sql-query.md)
* [Så här modellerar och partitionerar du data i Azure Cosmos DB med ett verkligt exempel](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
