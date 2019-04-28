---
title: Självstudie för att utveckla ett ASP.NET MVC-webbprogram med Azure Cosmos DB med hjälp av .NET Förhandsgranska SDK.
description: Den här självstudien beskrivs hur du skapar ett ASP .NET MVC-webbprogram med hjälp av Azure Cosmos DB. Du lagrar och kommer åt JSON-data från en att göra-app som hanteras i Azure.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: bf1da7e8a1041b15076ebda6eeac9b0a75c567c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439034"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>Självstudier: Utveckla ett ASP.NET MVC-webbprogram med Azure Cosmos DB med hjälp av förhandsversionen av .NET SDK 

> [!div class="op_single_selector"]
> * [NET](sql-api-dotnet-application.md)
> * [.NET-förhandsversion](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Den här självstudien beskriver hur du använder Azure Cosmos DB för lagring av och åtkomst till data från ett ASP.NET MVC-program som hanteras på Azure. I den här självstudien använder du .NET SDK-V3 som förhandsvisas just nu. Följande bild visar den webbplats som du kommer att skapa med hjälp av exemplet i den här artikeln:
 
![Skärmbild av MVC-webbprogram som skapats av den här självstudien – ASP NET MVC självstudier steg för steg för att göra-lista](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

Om du inte har tid att slutföra den här självstudien kan du ladda ned det fullständiga exempelprojektet från [GitHub][GitHub]. 

Den här självstudiekursen omfattar:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos-konto
> * Skapa en ASP.NET MVC-app
> * Ansluta appen till Azure Cosmos DB 
> * Utföra CRUD-åtgärder på data

> [!TIP]
> Den här självstudiekursen förutsätter att du har tidigare erfarenhet av MVC i ASP.NET och Azure Websites. Om du inte har använt ASP.NET eller [de verktyg som krävs](#prerequisites) tidigare rekommenderar vi att du laddar ned det fullständiga exempelprojektet från [GitHub][GitHub], lägger till de nödvändiga NuGet-paketen och kör det. När du skapar projektet kan du läsa den här artikeln för att få information om koden i projektets sammanhang.

## <a name="prerequisites"></a>Förhandskrav 

Innan du följer anvisningarna i den här artikeln bör du se till att du har följande resurser:

* **Ett aktivt Azure-konto:** Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* Microsoft Azure SDK för .NET för Visual Studio 2017, som är tillgängligt via Visual Studio-installationsprogrammet.

Alla skärmdumpar i den här artikeln har tagits med hjälp av Microsoft Visual Studio Community 2017. Om ditt system är konfigurerat med en annan version kan det hända att skärmbilder och alternativ inte ser riktigt likadana ut, men om ovanstående krav är uppfyllda ska lösningen fungera.

## <a name="create-an-azure-cosmos-account"></a>Steg 1: Skapa ett Azure Cosmos-konto

Vi börjar med att skapa ett Azure Cosmos-konto. Om du redan har ett Azure Cosmos DB SQL API-konto eller använder Azure Cosmos DB-emulatorn för den här självstudien kan du hoppa över och gå vidare till avsnittet [Skapa ett nytt ASP.NET MVC-program](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

I nästa avsnitt skapar du ett nytt ASP.NET MVC-program. 

## <a name="create-a-new-mvc-application"></a>Steg 2: Skapa ett nytt ASP.NET MVC-program

1. I Visual Studio öppnar du menyn **Arkiv**, väljer **Nytt** och sedan **Projekt**. Dialogrutan **Nytt projekt** visas.

2. I fönstret **Nytt projekt** expanderar du **Installerade** mallar, **Visual C#**, **Webb** och väljer sedan **ASP.NET-webbapp**. 

   ![Skapa nytt ASP.NET-webbapprojekt](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. I rutan **Namn** skriver du namnet på projektet. I den här självstudiekursen används namnet ”todo”. Om du väljer att använda något annat än det här ändrar du de angivna kodexemplen så att de använder det namn du gav din app på alla ställen där namnrymden todo nämns i självstudien. 

4. Välj **Bläddra** att navigera till mappen där du vill skapa projektet och välj sedan **.NET framework 4.6.1** eller högre. Välj **OK**. 

5. Dialogrutan **New ASP.NET Web Application** (Nytt ASP.NET-webbprogram) visas. Välj **MVC** i mallpanelen.

6. Välj **OK** och låt Visual Studio utföra kodskelettet runt den tomma ASP.NET MVC-mallen. 

7. När Visual Studio har skapat det formaterade MVC-programmet har du ett tomt ASP.NET-program som du kan köra lokalt.

## <a name="add-nuget-packages"></a>Steg 3: Lägg till Azure Cosmos DB NuGet-paketet i projektet

Nu när vi har det mesta av den ASP.NET MVC-ramverkskod som vi behöver för lösningen lägger vi till de NuGet-paket som krävs för att ansluta till Azure Cosmos DB.

1. Azure Cosmos DB .NET SDK paketeras och distribueras som ett NuGet-paket. Hämta NuGet-paketet i Visual Studio med hjälp av NuGet-pakethanteraren i Visual Studio genom att högerklicka på projektet i **Solution Explorer** och sedan välja **Hantera NuGet-paket**.
   
   ![Skärmbild av snabbemenyalternativen för webbprogramprojektet i Solution Explorer, med hantera NuGet-paket markerat.](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Dialogrutan **Hantera NuGet-paket** öppnas. I rutan för att **Bläddra** för NuGet skriver du **Microsoft.Azure.Cosmos**. Från resultatet installerar du **Microsoft.Azure.Cosmos** 3.0.0.1-förhandsversionen. Azure Cosmos DB-paketet och dess beroenden, t.ex. Newtonsoft.Json, laddas ned och installeras. Välj **OK** i fönstret **Förhandsgranskning** och **Jag godkänner** i fönstret **Godkännande av licens** för att slutföra installationen.
   
   Du kan även installera NuGet-paketet med hjälp av Package Manager-konsolen. Om du vill göra det går du till menyn **Verktyg**, väljer **NuGet-pakethanteraren** och därefter **Packet Manager-konsolen**. Skriv följande kommando i prompten:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. När paketet har installerats bör din Visual Studio-lösning innehålla de två nya biblioteksreferenserna till Microsoft.Azure.Cosmos.Client och Newtonsoft.Json.
  
## <a name="set-up-the-mvc-application"></a>Steg 4: Ställ in ASP.NET MVC-programmet

Nu lägger vi till modellerna, vyerna och styrenheterna till det här MVC-programmet:

* [Lägga till en modell](#add-a-model).
* [Lägga till en styrenhet](#add-a-controller).
* [Lägga till vyer](#add-views).

### <a name="add-a-model"></a> Lägga till en modell

1. I **Solution Explorer** högerklickar du på mappen **Modeller**, väljer **Lägg till** och sedan **Klass**. Dialogrutan **Lägg till nytt objekt** visas.

1. Ge den nya klassen namnet **TodoItem.cs** och välj **Lägg till**. 

1. Ersätt sedan koden i klassen ”Todoitem” med följande kod:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   De data som lagras i Azure Cosmos DB skickas via anslutningen och lagras som JSON. Du kan styra hur objekten serialiseras/deserialiseras av JSON.NET genom att använda attributet **JsonProperty** så som visas i den **TodoItem**-klass du skapade. Du kan inte bara styra formatet för det egenskapsnamn som matas in i JSON. Du kan även byta namn på dina .NET-egenskaper så som du gjorde med egenskapen **Beskrivning**. 

### <a name="add-a-controller"></a>Lägg till en controller

1. I **Solution Explorer** högerklickar du på mappen **Styrenheter**. Välj sedan **Lägg till** och sedan **Styrenhet**. Dialogrutan **Lägg till kodskelett** visas.

1. Välj **MVC 5-styrenhet – tom** och välj **Lägg till**.

   ![Skärmbild av dialogrutan Lägg till Kodskelett med MVC 5 styrenhet – tom alternativet markerat](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Ge den nya styrenheten namnet **ItemController och ersätt koden i den filen med följande kod:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   Attributet **ValidateAntiForgeryToken** används här för att skydda programmet mot attacker med förfalskning av begäran mellan webbplatser. Det finns mer att göra än att bara lägga till det här attributet. Även dina vyer bör fungera med den här antiförfalskningstoken. Mer information och implementeringsexempel finns i [Förhindra förfalskning av begäranden mellan webbplatser][Preventing Cross-Site Request Forgery]. Källkoden på [GitHub][GitHub] innehåller den fullständiga implementeringen.
   
   Vi använder även attributet **Bind** på metodparametern för att skydda mot overposting-attacker. Mer information finns i [Grundläggande CRUD-åtgärder i ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].
    
### <a name="add-views"></a>Lägg till vyer

Nu skapar vi följande tre vyer: 

* [Lägg till en listobjektvy](#AddItemIndexView).
* [Lägg till en ny objektsvy](#AddNewIndexView).
* [Lägg till en objektredigeringsvy](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Lägg till en listobjektvy

1. I **Solution Explorer** expanderar du mappen **Vyer** och högerklickar på den tomma **objektmapp** som Visual Studio skapade när du lade till **ItemController** tidigare. Klicka på **Lägg till** och sedan på **Vy**.
   
   ![Skärmbild av Solution Explorer som visar den objektmapp som Visual Studio skapade med kommandona Lägg till vy markerat](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. I dialogrutan **Lägg till vy** uppdaterar du följande värden:
   
   * I rutan **Vynamn** skriver du ***Index***.
   * I rutan**Mall** väljer du ***Lista***.
   * I rutan **Modellklass** väljer du ***Objekt (todo.Models)***.
   * Skriv ***~/Views/Shared/_Layout.cshtml*** i rutan på layoutsidan.
     
   ![Skärmbild som visar dialogrutan Lägg till vy](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

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

Det första du gör här är att lägga till en klass som innehåller logiken för att ansluta till och använda Azure Cosmos DB. För den här självstudien kapslar vi in den här logiken i en klass som heter TodoItemService.cs. Den här koden läser in Azure Cosmos DB-slutpunktsvärdena från konfigurationsfilen och utför CRUD-åtgärder såsom att lista ofullständiga objekt, skapa, redigera och ta bort objekten. 

1. I **Solution Explorer** skapar du en ny mapp under projektet med namnet **Services** (Tjänster).

1. Högerklicka på mappen **Services** (Tjänster), välj **Lägg till** och sedan **Klass**. Ge den nya klassen namnet **TodoItemService** och välj **Lägg till**.

1. Lägg till följande kod i klassen **TodoItemService** och ersätt koden i den filen med följande kod:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. Den tidigare koden läser värdena för anslutningssträng från konfigurationsfilen. För att uppdatera anslutningssträngvärdena för Azure Cosmos-kontot öppnar du filen **Web.config** filen i projektet och lägger till nedanstående rader under avsnittet `<AppSettings>`:

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. Uppdatera värdena för slutpunkt och primärnyckel med de värden som hämtats från bladet **Nycklar** på Azure-portalen. Använd den **URI** som finns på bladet Nycklar som värde för slutpunktsinställningen och använd den **PRIMÄRNYCKEL** eller **SEKUNDÄRNYCKEL** som finns på bladet Nycklar för nyckelinställningen. Nu har kopplingarna från Azure Cosmos DB till programmet skapats, och vi kan lägga till programlogiken.

1. Öppna **Global.asax.cs** och lägg till nedanstående rad i metoden **Application_Start** 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   Vid det här laget bör din lösning kunna skapa projektet utan fel. Om du kör programmet nu bör **HomeController** och vyn **Index** för den styrenheten öppnas. Det här är standardbeteendet för det MVC-mallprojekt som vi valde i början. Vi ändrar beteendet genom att dirigera om den här MVC-appen.

1. Öppna ***App\_Start\RouteConfig.cs***, leta upp den rad som börjar med ”defaults:” och uppdatera den med följande kod:

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

   Den här koden instruerar nu ASP.NET MVC att om du inte har angett något värde i den URL som ska styra dirigeringsbeteendet så ska inte **Home** utan i stället **Item** användas som styrenhet och användarens **Index** som vy.

Om du nu kör programmet anropar det till **ItemController**, som anropar GetItems-metoderna från klassen TodoItemService, som du definierar i nästa avsnitt. 

Om du bygger och kör det här projektet nu bör det se ut ungefär så här.    

![Skärmbild av webbprogram för att göra lista skapades av självstudien databas](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Steg 6: Kör programmet lokalt

Testa programmet på din lokala dator med följande steg:

1. Tryck på F5 i Visual Studio för att bygga appen i felsökningsläge. Den ska bygga appen och starta en webbläsare med den tomma rutnätssidan vi såg tidigare:
   
   ![Skärmbild av webbprogram för att göra lista skapades av självstudien databas](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Klicka på länken **Skapa nytt** och lägg till värden i fälten **Namn** och **Beskrivning**. Låt kryssrutan **Slutförd** vara avmarkerad. Annars läggs det nya objektet till i slutfört tillstånd och visas inte i den inledande listan.
   
3. Klicka på **Skapa**, så omdirigeras du tillbaka till vyn **Index** och objektet visas i listan. Du kan lägga till några fler objekt i att göra-listan.

    ![Skärmbild av vyn Index](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. Klicka på **Redigera** bredvid ett **objekt** på listan så kommer du till vyn **Redigera**, där du kan uppdatera alla egenskaper för objektet, inklusive flaggan **Slutförd**. Om du markerar flaggan **Slutförd** och klickar på **Spara** tas **objektet** bort från listan över ofullständiga uppgifter.
   
   ![Skärmbild av indexvyn med rutan slutförd ikryssad](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. När du har testat appen trycker du på Ctrl + F5 för att avsluta felsökningen. Nu är du redo att distribuera!

## <a name="deploy-the-application-to-azure"></a>Steg 7: Distribuera programmet 
När hela programmet fungerar som det ska med Azure Cosmos DB är det dags att distribuera webbappen till Azure App Service.  

1. Om du vill publicera det här programmet högerklickar du på projektet i **Solution Explorer** och väljer **Publicera**.
   
2. I dialogrutan **Publicera** väljer du **Microsoft Azure App Service** och sedan **Skapa ny** för att skapa en App Service-profil eller **Välj befintlig** om du vill använda en befintlig profil.

3. Om du har en befintlig Azure App Service-profil väljer du **Prenumeration** på den nedrullningsbara menyn. Använd filtret **Visa** om du vill sortera efter resursgrupp eller resurstyp. Sök sedan efter den Azure App Service som krävs och välj **OK**. 
   
   ![Dialogrutan App Service i Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. Om du vill skapa en ny Azure App Service-profil klickar du på **Skapa ny** i dialogrutan **Publicera**. I dialogrutan **Skapa App Service** anger du namnet på din webbapp och lämplig prenumeration, resursgrupp och App Service-plan. Välj sedan **Skapa**.

   ![Dialogrutan Create App Service (Skapa apptjänst) i Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

Efter några sekunder publicerar Visual Studio din webbapp och öppnar en webbläsare där du kan se hur projektet körs i Azure!

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du skapar en ASP.NET MVC-webbprogram som kan komma åt data som lagras i Azure Cosmos DB. Nu kan du fortsätta till nästa artikel:

> [!div class="nextstepaction"]
> [Skapa ett Java-program för att komma åt data som lagras i SQL API-konto i Azure Cosmos DB]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
